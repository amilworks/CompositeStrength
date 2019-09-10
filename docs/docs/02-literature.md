# Module Development

> BisQue Modules are analysis extensions to the bisque system that allow users to incorporate their own custom analysis scripts written in `Python, C++, Java, MATLAB, etc.` that perform high-end computations such as deep learning based methods for use by the system and others. 

![__Module Files.__ There are six files that are needed for a module to be built and deployed on the BisQue platform. ](images/bisqueModuleIntegration.svg)

__[Dockerfile](#dockerfile)__

Docker can build images automatically by reading the instructions from a `Dockerfile.` A `Dockerfile` is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.



__[Module XML](#module-xml)__

The module XML file provides the user interface for a module. Instead of writing `HTML`, fill in the fields necessary for your module such as the input should be an image and the output should be an image as well, i.e. a segmented image. 


__[Source Code](#source-code)__

The source code of a module can be written in almost any language. The way the code author structures their code---either in multiple files and folders or one large file---is also not an issue. Just make sure to copy all source code files into the Docker container.


__[Python Script Wrapper](#python-script-wrapper)__

This script will enable the communication between the module and BisQue. If this script is not present, good luck.

__[Python Setup](#python-setup)__

The `python setup.py` script is a standard in the python community. This will build the module.

__[Runtime Module Configuration](#runtime-module-configuration)__

This configuration script is simple: give a name to the module docker container. The other arguments can be left alone.

## Dockerfile 

> __FILENAME: __ `Dockerfile`

```sh
FROM ubuntu:xenial
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get -y update                                            && \
    apt-get -y upgrade                                           && \
    apt-get -y install                                              \
      python
RUN apt-get -y install python-lxml python-numpy
RUN apt-get -y install python-pip liblapack3 libblas-dev liblapack-dev gfortran
RUN apt-get -y install python-scipy python-configparser python-h5py
RUN apt-get update
RUN pip install pymks
RUN pip install tables scipy
RUN pip install --user --install-option="--prefix=" -U scikit-learn==0.19.1
RUN pip install -i https://biodev.ece.ucsb.edu/py/bisque/prod/+simple bisque-api==0.5.9
RUN pip install requests==2.10.0
WORKDIR /module
COPY PythonScriptWrapper /module/
COPY PythonScriptWrapper.py /module/
COPY predict_strength.py /module/
COPY pydist /module/pydist/
ENV PATH /module:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
#CMD [ 'python' '/predict_strength.py' ]
CMD [ 'PythonScriptWrapper' ]
FROM ubuntu:xenial
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get -y update                                            && \
    apt-get -y upgrade                                           && \
    apt-get -y install                                              \
      python
RUN apt-get -y install python-lxml python-numpy
RUN apt-get -y install python-pip liblapack3 libblas-dev liblapack-dev gfortran
RUN apt-get -y install python-scipy python-configparser python-h5py
RUN apt-get update
RUN pip install pymks
RUN pip install tables scipy
RUN pip install --user --install-option="--prefix=" -U scikit-learn==0.19.1
RUN pip install -i https://biodev.ece.ucsb.edu/py/bisque/prod/+simple bisque-api==0.5.9
RUN pip install requests==2.10.0
WORKDIR /module
COPY PythonScriptWrapper /module/
COPY PythonScriptWrapper.py /module/
COPY predict_strength.py /module/
COPY pydist /module/pydist/
ENV PATH /module:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
#CMD [ 'python' '/predict_strength.py' ]
CMD [ 'PythonScriptWrapper' ]
FROM ubuntu:xenial
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get -y update                                            && \
    apt-get -y upgrade                                           && \
    apt-get -y install                                              \
      python
RUN apt-get -y install python-lxml python-numpy
RUN apt-get -y install python-pip liblapack3 libblas-dev liblapack-dev gfortran
RUN apt-get -y install python-scipy python-configparser python-h5py
RUN apt-get update
RUN pip install pymks
RUN pip install tables scipy
RUN pip install --user --install-option="--prefix=" -U scikit-learn==0.19.1
RUN pip install -i https://biodev.ece.ucsb.edu/py/bisque/prod/+simple bisque-api==0.5.9
RUN pip install requests==2.10.0
WORKDIR /module
COPY PythonScriptWrapper /module/
COPY PythonScriptWrapper.py /module/
COPY predict_strength.py /module/
COPY pydist /module/pydist/
ENV PATH /module:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
#CMD [ 'python' '/predict_strength.py' ]
CMD [ 'PythonScriptWrapper' ]
```

## Module XML

> __FILENAME: __ `NAME_OF_MODULE.xml`, where `NAME_OF_MODULE` is replaced by the name of your module, i.e. `Dream3D.xml`.

The module definition file lays out the interface that the system can call the module with. The simplest form simply lists the name, location and arguments need to run the modules. Here is an example of a module definition document:

```xml
<module name="MyData" type="runtime">
    <tag name="inputs">
        <tag name="mex_url"      type="system-input" />
        <tag name="bisque_token" type="system-input" />
        <tag name="image_url"    type="image" />
    </tag>
    
    <tag name="outputs">
         <tag name="MetaData" />
         <tag name="MyData" type="tag" />
    </tag>
    
    <tag name="help" value="No help yet..." /> 
    <tag name="thumbnail" type="file" value="public/thumbnail.png" />   
    <tag name="title" value="MetaData" /> 
    <tag name="authors" value="The Bisque Team" /> 
    <tag name="description" value="This module annotates an image with its embedded metadata." /> 
</module>
```

The definition above allows the bisque system to call the module by creating a MEX.

The module definition document is actually a templated MEX document. The template parameters in this case are used to render the UI for this module if the user does not want to fully implement the UI. More about this will follow.

A module can define inputs and outputs and rely on the automated interface generation or can provide a fully customized user interface delivered by the module server by proxying the data made available by the engine service. Input configurations may also be used by the modules that define their own interfaces since they can call renderers provided by the module service.

### Module Description {-}
Each module has to be described in various way to be useful. Each module has a number required and optional parameters it has/may contain. Type in this case can control where the data is coming from, for example default "string" suggests the data is in-place. "file" directs the engine server to look for the file starting in the module root directory.


__Title__
```xml
<tag name="title" value="MetaData" />  
```

__Description__
```xml
<tag name="description" value="This module annotates an image with its embedded metadata." />   
```

__Authors__
```xml
<tag name="authors" value="The Bisque team" /> 
```
__Thumbnail__
```xml
<tag name="thumbnail" type="file" value="public/thumbnail.png" />   
```

__Help__

An HTML document with a module help that the user can be directed to the document can be inline.
```xml
<tag name="help" type="file" value="public/help.html" /> 
```
__Module Options__:
```xml
<tag name="module_options" >
    <tag name="version" value="2" />
</tag>
```

### Configurations for Images, Datasets, and Resources {-}

__Label__ 

Specifies the label rendered before asking for a resource.
```xml
<tag name="label" value="Select input image" />
```

__Accepted Type__ 

Defines multiple allowed types of input resource.
```xml
<tag name="accepted_type" value="dataset" />
<tag name="accepted_type" value="image" />
```


__Prohibit Upload__

Used with resources of type image or dataset to specify that the uploader should not be allowed.
```xml
<tag name="prohibit_upload" value="true" type="boolean" />
```

__Example Query__

Allow a button "from Example" specifies the query string.
```xml
<tag name="example_query" value="*GFAP*" />
```

__Allow Blank__ 

Makes resource optional.
```xml
<tag name="allow_blank" value="true" type="boolean" />
```



***

#### `Image` Specific Configurations {-}

__Require Geometry__

Enforce input _image_ geometry.

Here the `z` or `t` value may be:

  - null or undefined - means it should not be enforced
  - 'single' - only one plane is allowed
  - 'stack' - only stack is allowed

```xml
<tag name="require_geometry">
    <tag name="z" value="stack" />
    <tag name="t" value="single" />
    <tag name="fail_message" value="Only supports 3D images!" />
</tag>
```

__Fail Message__

Specify a message to show if failed requires validation.

```xml
<tag name="fail_message" value="Only supports 3D images!" />
```
***



#### `gobject` Specific Configurations {-}

__Example__
```xml
<gobject name="tips">
  <template>
    <tag name="gobject" value="point" />
    <tag name="require_gobjects">              
        <tag name="amount" value="many" />
        <tag name="fail_message" value="Requires selection of root tips" />
    </tag>              
  </template>
</gobject>
```

__gobject__

Defines multiple allowed types of input gobject.
```xml
<tag name="gobject" value="point" />
<tag name="gobject" value="polygon" />
```
Semantic types can also be specified here:
```xml
<tag name="gobject" value="foreground">
    <tag name="color" value="#00FF00" type="color" />
</tag>
<tag name="gobject" value="background">
    <tag name="color" value="#FF0000" type="color" />
</tag>
```

Moreover, colors could be proposed for these semantic types to differentiate graphical annotations in modules visually.

Users can also force only semantic annotations to be created basically prohibiting creation of primitive graphical elements without semantic meaning:
```xml
<tag name="semantic_types" value="require" />
```

__Require gobjects__

Validate input _gobject_.

```xml
<tag name="require_gobjects">
    <tag name="amount" value="many" />
    <tag name="fail_message" value="Requires select of root tips" />
</tag>
```

Configuration for `require_gobjects` consists of:

  - __amount__ - constraint on the amount of objects of allowed type. The amount can take the following values:
    - null or undefined - means it should not be enforced
    - 'single' - only one object is allowed
    - 'many' - only more than one object allowed
    - 'oneornone' - only one or none
    - number - exact number of objects allowed
    - $\leq X$ - operand followed by a number, accepts: $<,>,<=,>=,==$ 

      
      Example. Note that $<$ sign should be encoded in an XML attribute.
      ```xml
      <tag name="amount" value="3" type="number" />
      or
      <tag name="amount" value=">=2" />
      or
      <tag name="amount" value="&lt;30" />
      ```



__Fail Message__

Specify a message to show if failed requires validation.

```xml
<tag name="fail_message" value="Requires select of root tips" />
```

__Color__

Specify a default color for created gobjects

```xml
<tag name="color" value="#00FFFF" type="color" />
```
Example with semantic types and other configuration:
```xml
<gobject name="stroke">
    <template>
        <tag name="gobject" value="freehand_line" />
        <tag name="gobject" value="foreground">
            <tag name="color" value="#00FF00" type="color" />
        </tag>
        <tag name="gobject" value="background">
            <tag name="color" value="#FF0000" type="color" />
        </tag>
        <tag name="semantic_types" value="require" />
        
        <tag name="require_gobjects">
            <tag name="amount" value=">=2" />
            <tag name="fail_message" value="Requires two polylines; 
            first one inside object of interest (foreground)
            and second across background." />
        </tag>
    </template>
</gobject>
```
***





### Data-Parallel Execution {-}

It is possible to execute any module in a data-parallel way by passing a dataset instead of an individual image. In order to do this you need to:

  1. Indicate the resource that can be iterated on
  2. Allow that resource to accept datasets and possibly
  3. Configure renderers for iterated run
  

```xml
<!-- allow iterable resource to accept datasets -->
<tag name="inputs">
    <tag name="image_url" type="resource">
        <template>
            <tag name="accepted_type" value="image" />
            <tag name="accepted_type" value="dataset" />
            ...
        </template>
    </tag> 
    ...
</tag> 

...
<!-- configure renderers for iterated run -->
<tag name="outputs">
    ...         
    <!-- Iterated outputs -->
    <tag name="mex_url" type="mex" />
    <tag name="resource_url" type="dataset" />
</tag>

.....

<!-- indicate the resource that can be iterated -->
<tag name="execute_options">
    <tag name="iterable" value="image_url" type="dataset" />
</tag> 
```
This definition is used by the module UI to add a dataset selector for this image and let module server know by sending a proper MEX that this resource should be iterated upon. Module server will create a parallel execution iterating over the selected dataset and creating an output MEX with sub MEXes for each individual image.

__Other Data-Parallel Types__

One can also request parallel execution over resource types other than `dataset`. For example a very useful would be to request iteration over a MEX, where a module could accept parallelized MEX as input and iterate over sub-MEXs for parallelized processing of results. In order to do that we need to indicate the type of the input resource and additionally provide an xpath expression within that resource to find elements we would like to iterate over:

```xml
<tag name="execute_options">
    <tag name="iterable" value="input_mex" type="mex" >
        <tag name="xpath" value="./mex/@uri" />
    </tag>
</tag> 
```




## Python Script Wrapper

> __FILENAME: __ `PythonScriptWrapper.py`

### _Example._  Python Script Wrapper {-}

#### Imports {-}

The main imports for the `PythonScriptWrapper` are mostly for logging and communicating with BisQue. In this code snippet, the line `from NAME_OF_MODULE import predict_function` is importing a prediction function from a single `Python` file. If multiple functions need to be imported from a source folder, make sure there is an `__init__.py` or there will be import errors. 


```python
import sys
import io
from lxml import etree
import optparse
import logging


from NAME_OF_MODULE import predict_function


logging.basicConfig(filename='PythonScript.log',filemode='a',level=logging.DEBUG)
log = logging.getLogger('bq.modules')


from bqapi.comm import BQCommError
from bqapi.comm import BQSession
```


#### Python Script Wrapper Class {-}

The class contains all of the functions needed to initialize, run, and save the module results back to BisQue as a resource. For instance, if the output is an image, the resource would be of type image and uploaded to BisQue as an image.


```python
class PythonScriptWrapper(object):
    def run(self):
        """
        Run Python script
        """
        bq = self.bqSession
        
        # call script
        outputs = predict_function( bq, log, **self.options.__dict__ )
        
        # save output back to BisQue
        for output in outputs:
            self.output_resources.append(output)
    
    def setup(self):
        """
        Pre-run initialization
        """
        self.bqSession.update_mex('Initializing...')
        self.mex_parameter_parser(self.bqSession.mex.xmltree)
        self.output_resources = []

    def teardown(self):
        """
        Post the results to the mex xml
        """
        self.bqSession.update_mex( 'Returning results')

        outputTag = etree.Element('tag', name ='outputs')
        for r_xml in self.output_resources:
            if isinstance(r_xml, basestring):
                r_xml = etree.fromstring(r_xml) 
            res_type = r_xml.get('type', None) or r_xml.get('resource_type', None) or r_xml.tag
            # append reference to output
            if res_type in ['table', 'image']:
                outputTag.append(r_xml)
                #etree.SubElement(outputTag, 'tag', name='output_table' if res_type=='table' else 'output_image', type=res_type, value=r_xml.get('uri',''))
            else:
                outputTag.append(r_xml)
                #etree.SubElement(outputTag, r_xml.tag, name=r_xml.get('name', '_'), type=r_xml.get('type', 'string'), value=r_xml.get('value', ''))
        self.bqSession.finish_mex(tags=[outputTag])

    def mex_parameter_parser(self, mex_xml):
        """
            Parses input of the xml and add it to options attribute (unless already set)

            @param: mex_xml
        """
        # inputs are all non-"script_params" under "inputs" and all params under "script_params"
        mex_inputs = mex_xml.xpath('tag[@name="inputs"]/tag[@name!="script_params"] | tag[@name="inputs"]/tag[@name="script_params"]/tag')
        if mex_inputs:
            for tag in mex_inputs:
                if tag.tag == 'tag' and tag.get('type', '') != 'system-input': #skip system input values
                    if not getattr(self.options,tag.get('name', ''), None):
                        log.debug('Set options with %s as %s'%(tag.get('name',''),tag.get('value','')))
                        setattr(self.options,tag.get('name',''),tag.get('value',''))
        else:
            log.debug('No Inputs Found on MEX!')

    def validate_input(self):
        """
            Check to see if a mex with token or user with password was provided.

            @return True is returned if validation credention was provided else
            False is returned
        """
        if (self.options.mexURL and self.options.token): #run module through engine service
            return True

        if (self.options.user and self.options.pwd and self.options.root): #run module locally (note: to test module)
            return True

        log.debug('Insufficient options or arguments to start this module')
        return False
```

#### Main Function {-}

The main function enables the communication between BisQue and the module. For example, when a module is run under a user, we need to make sure that the unique ID is registered with the user.


```python
def main(self):
    parser = optparse.OptionParser()
    parser.add_option('--mex_url'         , dest="mexURL")
    parser.add_option('--module_dir'      , dest="modulePath")
    parser.add_option('--staging_path'    , dest="stagingPath")
    parser.add_option('--bisque_token'    , dest="token")
    parser.add_option('--user'            , dest="user")
    parser.add_option('--pwd'             , dest="pwd")
    parser.add_option('--root'            , dest="root")
        
    (options, args) = parser.parse_args()

    fh = logging.FileHandler('scriptrun.log', mode='a')
    fh.setLevel(logging.DEBUG)
    formatter = logging.Formatter('[%(asctime)s] %(levelname)8s --- %(message)s ' +
                              '(%(filename)s:%(lineno)s)',datefmt='%Y-%m-%d %H:%M:%S')
    fh.setFormatter(formatter)
    log.addHandler(fh)

    try: #pull out the mex

        if not options.mexURL:
            options.mexURL = sys.argv[-2]
        if not options.token:
            options.token = sys.argv[-1]

    except IndexError: #no argv were set
        pass

    if not options.stagingPath:
        options.stagingPath = ''

    log.info('\n\nPARAMS : %s \n\n Options: %s' % (args, options))
    self.options = options

    if self.validate_input():

        #initalizes if user and password are provided
        if (self.options.user and self.options.pwd and self.options.root):
            self.bqSession = BQSession().init_local( self.options.user, self.options.pwd, bisque_root=self.options.root)
            self.options.mexURL = self.bqSession.mex.uri

        #initalizes if mex and mex token is provided
        elif (self.options.mexURL and self.options.token):
            self.bqSession = BQSession().init_mex(self.options.mexURL, self.options.token)

        else:
            raise ScriptError('Insufficient options or arguments to start this module')

        try:
            self.setup()
        except Exception as e:
            log.exception("Exception during setup")
            self.bqSession.fail_mex(msg = "Exception during setup: %s" %  str(e))
            return

        try:
            self.run()
        except (Exception, ScriptError) as e:
            log.exception("Exception during run")
            self.bqSession.fail_mex(msg = "Exception during run: %s" % str(e))
            return

        try:
            self.teardown()
        except (Exception, ScriptError) as e:
            log.exception("Exception during teardown")
            self.bqSession.fail_mex(msg = "Exception during teardown: %s" %  str(e))
            return
    
        self.bqSession.close()

if __name__=="__main__":
    PythonScriptWrapper().main()
```

## Source Code

> You can have all your files in a `/source` folder or have one `Python, MATLAB, C++,` etc. file.


### _Example._ Composite Strength Module {-}

Let's take a look at the structure of the Composite Strength module. In this example, we only have one `Python` file that contains all of our source code, `predict_strength.py`. 

```sh
TwoPhasePrediction/
├── Dockerfile
├── PythonScriptWrapper
├── PythonScriptWrapper.py
├── TwoPhasePrediction.xml
├── predict_strength.py
├── public
│   ├── help.html
│   ├── marat_workflow.png
│   ├── thumbnail.png
│   ├── webapp.css
│   └── webapp.js
├── runtime-module.cfg
└── setup.py
```


## Runtime Module Configuration

> __FILENAME: __ `runtime-module.cfg`

The runtime module configuration file only has one line that needs to be changed: `docker.image = NAME_OF_MODULE`. This should be the name of the docker image of the module.

```sh
#  Module configuration file for local execution of modules
runtime.platforms = command

#  Module configuration file for local execution of modules
module_enabled = True
runtime.platforms=command

[command]
docker.image = NAME_OF_MODULE
environments = Staged,Docker
executable = python PythonScriptWrapper.py
files = pydist, PythonScriptWrapper.py
```

## Python Setup

> __FILENAME: __ `setup.py`

The only changes to make in this file are naming. Specifically, this line:

```python
docker_setup('Composite_Strength', 'TwoPhasePrediction', 'twophaseprediction', params=params)
```

__NOTE:__ Before running `python setup.py`, please make sure that all the files are created and configured correctly. If not, save yourself hours of troubleshooting by going through the other file tutorials.




```python
import sys
from bq.setup.module_setup import python_setup, docker_setup, require, read_config


def setup(params, *args, **kw):
    python_setup('PythonScriptWrapper.py', params=params)
    docker_setup('Composite_Strength', 'TwoPhasePrediction', 'twophaseprediction', params=params)
    
if __name__ =="__main__":
    params = read_config('runtime-bisque.cfg')
    if len(sys.argv)>1:
        params = eval (sys.argv[1])
    sys.exit(setup(params))
```







