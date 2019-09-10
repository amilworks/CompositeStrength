# BisQue Development


This guide details the installation of a bisque server on an Ubuntu 16.04 LTS environment.

Project Source

- Github: https://github.com/UCSB-VRL/bisque

Developer Installation

- Bisque Github Pages: https://ucsb-vrl.github.io/bisque-dev/guides/bisque

Reference

- [Bique Bioimage Google Groups](https://groups.google.com/forum/#!topic/bisque-bioimage/jwo_5sHFeHU)
- [Instructions on installing bisque using docker](https://bitbucket.org/CBIucsb/bisque/src/default/README.md)

## Source Code Installation

---

> __NOTE__: The source code installation has only been tested on `Ubuntu 16.04`. 

__Pre-requisites__
```sh
sudo apt-get install -y python python-dev python-virtualenv python-numpy python-scipy 
sudo apt-get install -y libxml2-dev libxslt1-dev libhdf5-dev
sudo apt-get install -y libmysqlclient-dev libpq-dev mercurial git cmake
sudo apt-get install -y postgresql postgresql-client libsqlite3-dev
sudo apt-get install -y python-paver python-setuptools
sudo apt-get install -y graphviz libgraphviz-dev pkg-config
sudo apt-get install -y openslide-tools  python-openslide
sudo apt-get install -y libfftw3-dev libbz2-dev libz-dev
sudo apt-get install -y liblcms2-dev libtiff-dev libpng-dev
sudo apt-get install -y libgdcm2.6 libopenslide-dev libopenslide0
```

__Install Openjpeg__
```sh
git clone https://github.com/uclouvain/openjpeg
cd openjpeg && mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
sudo make -j4 install
sudo ldconfig
```

__Install BioImageConvert__

- [BioImageConvert Source Repository](https://biodev.ece.ucsb.edu/projects/imgcnv)
- [Prebuilt Binaries Repository](https://bitbucket.org/dimin/bioimageconvert/downloads/)
- Setup for pre-built binaries (below)

```sh
# Ubuntu 18 version
wget https://bitbucket.org/dimin/bioimageconvert/downloads/imgcnv_ubuntu18_2.5.0.tar.gz

# Ubuntu 16 version
wget https://bitbucket.org/dimin/bioimageconvert/downloads/imgcnv_ubuntu16_2.4.3.tar.gz
tar -xvzf imgcnv_ubuntu16_2.4.3.tar.gz

sudo cp imgcnv_ubuntu16_2.4.3/imgcnv /usr/local/bin/
sudo cp imgcnv_ubuntu16_2.4.3/libimgcnv.so.2.4.3 /usr/local/lib/
sudo ln -s /usr/local/lib/libimgcnv.so.2.4.3 /usr/local/lib/libimgcnv.so.2.4
sudo ln -s /usr/local/lib/libimgcnv.so.2.4 /usr/local/lib/libimgcnv.so.2
sudo ln -s /usr/local/lib/libimgcnv.so.2 /usr/local/lib/libimgcnv.so
sudo ldconfig
```

Alternately, Compile by source and Install (You are on your own here)

```sh
hg clone --insecure http://biodev.ece.ucsb.edu/hg/imgcnv
cd imgcnv && make -j6 
sudo make install
```

---

### Clone the repository and Prepare Virtual Environment {-}

We will clone the stable repository and work inside a python virtual environment for setup requirements.

```sh
git clone https://github.com/UCSB-VRL/bisque.git bisque-stable
```
- Its always a good practive to use a virtualenv to develop projects

```sh
sudo pip install virtualenvwrapper
```
- Edit ~/.bashrc and export the following variables
  - In case of issues around virtualenvwrapper make sure the paths in the variables below exist

```sh
# virtualenv and virtualenvwrapper
export PATH=/usr/local/bin:$PATH
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
source /usr/local/bin/virtualenvwrapper.sh
```

- Now load this using "source ~/.bashrc"
- Create a virtual envrionment "mkvirtualenv -p /usr/bin/python2 bqdev"
- Change environment "workon bqdev"

__Deprecated Bootstrap Installer (Do Not Use this Script)_ _

```sh
$ mkdir bisque && cd bisque
$ wget http://biodev.ece.ucsb.edu/projects/bisquik/export/tip/bisque-stable/contrib/bootstrap/bisque-bootstrap.py
$ python bisque-bootstrap.py bqenv

# Activate Virtualenv
bisque$ source bqenv/bin/activate
```

- Now Install requirements

```sh
pip install -i https://biodev.ece.ucsb.edu/py/bisque/xenial/+simple/ -r requirements.txt

Alternate Index Url for Development: https://biodev.ece.ucsb.edu/py/bisque/dev/+simple
Index Url Debian Stretch: https://biodev.ece.ucsb.edu/py/bisque/stretch/+simple/
Index Url Ubuntu xenial: https://biodev.ece.ucsb.edu/py/bisque/xenial/+simple/
```

- Fix the requirements.txt (Only if the installation fails)

```sh
#Fix the requirements.txt file using sed -i 's/.*psycopg2==2.6.1.*/psycopg2==2.7.1./' requirements.txt
psycopg2==2.7.1
Minimatic==1.0
Paste==1.7.5.1
httplib2==0.7.3
#tgext.registration==0.1dev

# Install separately since packages may be deprecated in PyPi
easy_install http://biodev.ece.ucsb.edu/binaries/depot/tgext.registration2/tgext.registration2-0.5.2.tar.gz
```

---

### Configure Bisque Environment {-}

- Run the Paver setup

```sh
$ paver setup
```

- Expected paver log tail

```sh
Installing collected packages: bqengine
  Running setup.py develop for bqengine
Successfully installed bqengine

Now run:
bq-admin setup 
```

- Run the bq-admin standalone setup 

```sh
$ bq-admin setup
```

- Expected bq-admin setup log tail (also observe the various inputs in the log for this setup)
 
```sh
  (bqdev) rahul@loup:~/repository/github/bisque$ bq-admin setup 
  INFO:root:Generating grammar tables from /usr/lib/python2.7/lib2to3/Grammar.txt
  INFO:root:Generating grammar tables from /usr/lib/python2.7/lib2to3/PatternGrammar.txt
  Developer installation
  DIRS:  {'bin': '/home/rahul/.virtualenvs/bqdev/bin', 'run': '.', 'share': '.', 'plugins': './plugins', 'packages': '/home/rahul/.virtualenvs/bqdev/lib/python2.7/site-packages', 'data': './data', 'virtualenv': '/home/rahul/.virtualenvs/bqdev', 'default': './config-defaults', 'jslocation': 'bqcore', 'modules': './modules', 'depot': './external', 'config': './config', 'public': './public'}
  INFO:root:Generating grammar tables from /usr/lib/python2.7/lib2to3/Grammar.txt
  INFO:root:Generating grammar tables from /usr/lib/python2.7/lib2to3/PatternGrammar.txt
  Developer installation
  DIRS:  {'bin': '/home/rahul/.virtualenvs/bqdev/bin', 'run': '.', 'share': '.', 'plugins': './plugins', 'packages': '/home/rahul/.virtualenvs/bqdev/lib/python2.7/site-packages', 'data': './data', 'virtualenv': '/home/rahul/.virtualenvs/bqdev', 'default': './config-defaults', 'jslocation': 'bqcore', 'modules': './modules', 'depot': './external', 'config': './config', 'public': './public'}
  This is the main installer for Bisque

      The system will initialize and be ready for use after a succesfull
      setup has completed.

      Several questions must be answered to complete the install.  Each
      question is presented with default in brackets [].  Pressing
      <enter> means that you are accepting the default value. You may
      request more information by responding with single '?' and then <enter>.

      For example:
      What is postal abbreviation of Alaska [AK]?

      The default answer is AK and is chosen by simply entering <enter>

      
  Beginning install of ['bisque', 'engine'] with ['server'] 
  CALLING  <function install_external_binaries at 0x7f9533ffac08>
  Fetch external binary files from Bisque development server [Y]? Y
  Matched section Linux-64bit-*
  extjs.zip found locally
  bioformats-pack.zip found locally
  libmbgl-linux-64-large.a found locally
  feature_extractors.zip found locally
  ImarisConvert.tar.gz found locally
  opencv-2.4.6.zip found locally
  CALLING  <function install_dependencies at 0x7f9533ffad70>
  2.4.3

  Found imgcnv version 2.4.3


  Imgcnv is installed and no-precompiled version exists. Using installed version
  ImarisConvert is up to date
  No pre-compiled version of openslide exists for your system
  Please visit our mailing list https://groups.google.com/forum/#!forum/bisque-bioimage for help
  Bioformats is up to date
  CALLING  <function install_features at 0x7f9533ffb050>
  Install feature extractors (Feature Server) [Y]? 
  Unpacking ./external/feature_extractors.zip into ./bqfeature/bq
  Install source code for feature extractors [N]? 
  To enable the feature service to read OME-bigtiff for feature extraction install
          libtiff4
          For Debian use the command apt-get install libtiff5-dev
          
  Install OpenCV-2.4.6 [Y]? 
  Extracted opencv-2.4.6/python2.7/cv.py -> /home/rahul/.virtualenvs/bqdev/lib/python2.7/site-packages/cv.py
  Extracted opencv-2.4.6/python2.7/cv2.so -> /home/rahul/.virtualenvs/bqdev/lib/python2.7/site-packages/cv2.so
  CALLING  <function install_plugins at 0x7f9533ff9b18>
  Try to install plugins [Y]? 
  INFO 'svn'not found: cannot fetch source repositories with svn 
  INSTALL PLGINS  ['./plugins']
  Checking ./plugins for modules
  CALLING  <function install_public_static at 0x7f9533ffa848>
  Deploy all static resources to public directory [Y]? 
  Creating ./public
  INFO:bq.image_service.server:Available converters: openslide (1.1.1), imgcnv (2.4.3), ImarisConvert (8.0.2), bioformats (5.1.1)
  Problem loading registration = bq.registration.controllers.registration_service: 'User'

  Generating packaged JS and CSS files

  INFO:minimatic:Combined -> ./public/core/css/all_css.css:
  INFO:minimatic:Combined -> ./public/core/js/all_js.js:
  CALLING  <function install_server_defaults at 0x7f9533ffa410>
  Server config
  Top level site variables are:
    bisque.admin_email=YourEmail@YourOrganization
    bisque.admin_id=admin
    bisque.organization=Your Organization
    bisque.paths.root=.
    bisque.server=http://0.0.0.0:8088
    bisque.title=Image Repository
  Change a site variable [N]? Y
  Enter the root URL of the server  [http://0.0.0.0:8088]? http://loup.ece.ucsb.edu:8080
  Your real name  administrator account []? 
  A login ID for the administrator account [admin]? 
  An email for the administrator [YourEmail@YourOrganization]? 
  A small organization title for the main page [Your Organization]? 
  The main title for the web page header [Image Repository]? 
  Installation Directory [.]? 
  Do you want to create new server configuations [Y]? 
  {'backend': 'paster',
  'e1.bisque.has_database': 'false',
  'e1.bisque.static_files': 'false',
  'e1.services_enabled': 'engine_service',
  'e1.url': 'http://0.0.0.0:27000',
  'h1.bisque.static_files': 'true',
  'h1.services_disabled': '',
  'h1.services_enabled': '',
  'h1.url': 'http://0.0.0.0:8088',
  'servers': 'h1'}
  The server agent for bisque [paster]? 
  list of server entries to be configured [h1]? 
  Install (update) paster configs (application server and configs) [Y]? 
  PARAMS {'h1.bisque.static_files': 'true', 'h1.services_enabled': '', 'e1.bisque.has_database': 'false', 'h1.url': 'http://0.0.0.0:8088', 'servers': 'h1', 'h1.services_disabled': '', 'e1.url': 'http://0.0.0.0:27000', 'e1.bisque.static_files': 'false', 'e1.services_enabled': 'engine_service', 'backend': 'paster'}
  h1: the url of the server [http://0.0.0.0:8088]? http://loup.ece.ucsb.edu:8080
  h1: Services enabled []? 
  h1: Services disabled []? 
  Created paster config:  ./config/h1_paster.cfg
  CALLING  <function install_mail at 0x7f9533ffa758>
  Enable mail delivery [Y]? N
  CALLING  <function install_secrets at 0x7f9533ffa8c0>
  Encrypt cookies with secret phrase [CEMWOJ65]? 
  CALLING  <function install_database at 0x7f9533ff9578>
  A database URI [sqlite:///data/bisque.db]? 
  Trying to import driver sqlite3...
  Driver successfully imported.
  Create and initialize database [Y]? 
  A database URI [sqlite:///data/bisque.db]? 
  Checking whether database "data/bisque.db" already exists...
  Yes, it exists.
  Upgrading database version (alembic)
  INFO  [alembic.migration] Context impl SQLiteImpl.
  INFO  [alembic.migration] Will assume non-transactional DDL.
  CALLING  <function install_preferences at 0x7f9533ffa7d0>
  Initialize Preferences  [Y]? 
  Force initialization  [N]? 
  INFO:bq.config:DATABASE sqlite:///data/bisque.db
  INFO:bq.config:SQLLite special handling NullPool timoout
  INFO:bq.image_service.server:Available converters: openslide (1.1.1), imgcnv (2.4.3), ImarisConvert (8.0.2), bioformats (5.1.1)
  INFO:bq.blobs.mounts:Loaded drivers OrderedDict([('local', {'top': 'file://$datadir/imagedir/', 'mounturl': 'file://$datadir/imagedir/$user/'})])
  WARNING:bq.blobs.mounts:SQLITE does not support subtransactions: some mount service operations will fail
  INFO:bq.blobs.plugins:Resource plugins: ['(bisque_pipeline, bq)', '(cellprofiler_pipeline, cppipe)', '(cellprofiler_pipeline, cp)', '(dream3d_pipeline, json)', '(imagej_pipeline, ijm)', '(text, asc)', '(text, ascii)', '(text, zw)', '(text, hz)', '(text, txt)', '(text, utxt)', '(text, utf8)', '(table, csv)', '(table, hdf)', '(table, hdf5)', '(table, h5)', '(table, he5)', '(table, h5ebsd)', '(table, dream3d)', '(table, csv)', '(table, xls)', '(table, xlsx)']
  WARNING:bq.service:Following service were not found: set(['mnt'])
  /home/rahul/.virtualenvs/bqdev/local/lib/python2.7/site-packages/sqlalchemy/sql/sqltypes.py:226: SAWarning: Unicode type received non-unicode bind param value 'system'. (this warning may be suppressed after 10 occurrences)
    (util.ellipses_string(value),))
  NO ACTION: System object initialized at <system created="2019-02-04T10:51:57.823907" name="system" owner="http://localhost/data_service/00-2jimYQLLjB3yy3HuTwQM5c" permission="published" resource_uniq="00-UAZfWJYjDKp4yhabV3PP7S" ts="2019-02-04T10:51:57.823907" uri="http://localhost/data_service/00-UAZfWJYjDKp4yhabV3PP7S"/> 
  Problem initializing preferences.. please use bq-admin preferences

  Initialize your database with:
    $ bq-admin setup createdb

  You can start bisque with:
    $ bq-admin server start
  then point your browser to:
      http://loup.ece.ucsb.edu:8080
  If you need to shutdown the servers, then use:
    $ bq-admin server stop
  You can login as admin and change the default password.

  Send Installation/Registration report [Y]? N
  (bqdev) rahul@loup:~/repository/github/bisque$ 


```

- Add "config/runtime-bisque.cfg" for module configuration and docker image registry
  - Edit and add run config from config-defaults/runtime-bisque.defaults to config/runtime-bisque.cfg
  - Here are some local changes but requires docker to be setup

```
runtime.platforms = command
runtime.staging_base = staging/

[docker]
docker.enabled = true
# A  hub where to push containers to
docker.hub = biodev.ece.ucsb.edu:5000
```

---

### Run Bisque Server {-}

- Start/Stop the server

  ```
  $ bq-admin server start
  $ bq-admin server stop
  ```

- Overview of Installation (Just for review)

  ```
  workon bqdev
  paver setup    [server|engine]
  bq-admin setup [server|engine]
  bq-admin deploy public
  ```

Open in browser
![Browser Client](img/BisqueClientScreen.png )


### Upload Dataset {-}

- Upload an [image file](img/cells.ome.tif) from your local directory to Bisque 
  ![Upload Image file](img/UploadImageData.png)

- Browse uploaded dataset in the file explorer
    - Select Browse menu item in the navigation bar and click on dataset
    - Thereafter click on the files tab and click through the local database folder in the left document tree section.

  ![Upload Image file](img/DatasetBrowse.png)

- Select the uploaded file to view 
    - This visualization is by default 2D in nature. 
    - The various slices can be stepped through or played using the scroll bar on righ bottom of the image view area

  ![View 2D Image file](img/ViewCell2DImage.png)

  - A 3D visualization can be explored by clicking the cube icon towards the right of share and delete icons on the tool bar right below the navigation bar.

  ![View 3D Image file](img/ViewCell3DImage.png)

---

### Module {-}

__Load/Install Module packages and dependencies__

Lets take module/MetaData code as an example.

- Ensure ~/bisque/config/runtime-bisque.cfg has the configuration as below
```sh
runtime.staging_base = staging/
docker.hub = biodev.ece.ucsb.edu:5000
```
- Make sure the runtime-module.cfg is as per below
```sh
module_enabled = True
runtime.platforms=command
[command]
environments = Staged
executable = python MetaData.py
files = MetaData.py
```
- Now build/compile this module
```sh
 cd ~/bisque/modules/MetaData
 python setup.py
```

Open up the Bisque web interface at http://loup.ece.ucsb.edu:8088

- Login using admin:admin 
- Update the email address in the users context menu item (This is important)
- Open Module Manager from the admin user context menu
- Paste the following URL in the Module->Engine Module
```
http://loup.ece.ucsb.edu:8088/engine_service/
```
- List of engine modules could be seen
- Drag-n-Drop say MetaData module from the list to the left window and close the window

  ![Bisque Module](img/module_metadata/DragRegModule.png)
  ![Bisque Module](img/module_metadata/RegisterModuleMetaData.png)

### Debug Module {-}

- Every module run will generate a model execution identifier to track this execution. You can observe this after clicking the run command and you have a result. It could be success or fail in the Results section

```
http://loup.ece.ucsb.edu:8088/module_service/MetaData/?wpublic=1#mex=http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF
```

- The mex identifier (==MEX_ID=00-ZmuAoE43wTxByycmaCnvBF==) observed from URL can be used to locate this execution code & resulting logs in the staging folders
- change directory to this staging folder and observe the files there

```sh
cd ~/bisque/staging/00-ZmuAoE43wTxByycmaCnvBF
rahul@loup:~/bisque/staging/00-ZmuAoE43wTxByycmaCnvBF$ tree 
.
├── MetaData.log
├── MetaData.py
├── python.log
└── state00-ZmuAoE43wTxByycmaCnvBF.bq
0 directories, 4 files
```

- Here we can see that the MetaData.log and python.log files are generated
- In case of issues these are the log files that we need to look into for detailed error reporting
- main log file is the bisque_8088.log file at the ~/bisque home directory 
  
```sh
13:45:11,996 INFO  [bq.root] [admin] POST http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF?view=short
13:45:12,001 INFO  [bq.module_server] MEX APPEND http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF?view=short
13:45:12,205 INFO  [bq.engine_service.command_run] SUCCESS Command python MetaData.py http://loup.ece.ucsb.edu:8088/data_service/00-6dpWgEAue2iz8YZcKPHejh http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF admin:00-ZmuAoE43wTxByycmaCnvBF with 0
13:45:12,206 INFO  [bq.engine_service.command_run] All processes have returned ['finished']
13:45:12,206 INFO  [bq.engine_service.command_run] finishing 1 mexes -> [{'files': ['MetaData.py'], 'status': 'finished', 'executable': ['python', 'MetaData.py', 'http://loup.ece.ucsb.edu:8088/data_service/00-6dpWgEAue2iz8YZcKPHejh', 'http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF', 'admin:00-ZmuAoE43wTxByycmaCnvBF'], 'initial_dir': '/home/rahul/repository/github/bisque-dev', 'module_enabled': 'True', 'named_args': {'bisque_token': 'admin:00-ZmuAoE43wTxByycmaCnvBF', 'image_url': 'http://loup.ece.ucsb.edu:8088/data_service/00-6dpWgEAue2iz8YZcKPHejh', 'mex_url': 'http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF'}, 'bisque_token': u'admin:00-ZmuAoE43wTxByycmaCnvBF', 'environments': 'Staged', 'runtime.staging_base': 'staging/', 'mex_id': '00-ZmuAoE43wTxByycmaCnvBF', 'runtime.matlab_launcher': 'config-defaults/templates/matlab_launcher_SYS.tmpl', 'arguments': [], 'module_dir': '/home/rahul/repository/github/bisque-dev', 'staging_path': '/home/rahul/repository/github/bisque-dev/staging/00-ZmuAoE43wTxByycmaCnvBF', 'iterables': False, 'log_name': '/home/rahul/repository/github/bisque-dev/staging/00-ZmuAoE43wTxByycmaCnvBF/python.log', 'runtime.matlab_home': '', 'mex_url': 'http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF', 'rundir': '/home/rahul/repository/github/bisque-dev/staging/00-ZmuAoE43wTxByycmaCnvBF', 'runtime.platforms': 'command'}]
```

- This will show us the COMMAND that was run to execute this module with MEX_URL

```sh
python MetaData.py \
http://loup.ece.ucsb.edu:8088/data_service/00-6dpWgEAue2iz8YZcKPHejh \ 
http://loup.ece.ucsb.edu:8088/module_service/mex/00-ZmuAoE43wTxByycmaCnvBF \
admin:00-ZmuAoE43wTxByycmaCnvBF
```
- This is the command that we can use to debug and rerun/replay this MEX in order to update the execution result. This is different from running the module again from the web interface since that will produce a new MEX_ID and create another staging folder corresponding to that.

### G. Module MEX/UI {-}

- Click on Browse->mex to go to the Model execution page

  ![Mex Module](img/module_metadata/NavBrowseMex.png)

  ![Mex Browse](img/module_metadata/ModuleMexView.png )

- Here we will be able to see the various MEX sessions that were run for all modules under execution
- We will be able to obtain the MEX_ID and view its state from here by selecting one of the executions of interest.

  ![Mex Success Module](img/module_metadata/ModuleRunMexView.png )

- In case we select a module that has errors in it then it would turn out to show the status with messages in red

  ![Mex Success Module](img/module_metadata/ModuleMexFailure.png )

- Now let us go to Browse->dataset and select the file that we ran the module for. Here we will be able to view the Annotation that was added by the Metadata module to this image.
    - Annotation can be visualized using the "Annotations" tab on the right
  
  ![Data Annot Module](img/module_metadata/DatasetFileMetadataAnnotated.png )

  - The Model execution runs can be seen in the "Analysis" tab on the right

  ![Data Mex Module](img/module_metadata/DatasetFileMetadataMex.png )


---

#### Further {-}


#### Docker & Condor based modules require additional setup {-}


- Install Docker (https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  - Also go through the post-installation steps
  - Also install the [Nvidia-docker](https://github.com/nvidia/nvidia-docker/) in case you are planning to use GPU based modules or connoisseur services.
- Install Condor (https://research.cs.wisc.edu/htcondor/ubuntu/)
  - Understand the configurations as well for master & slave setup 
  - [Bisque Condor Setup Instructions](../rancher2_condor) 

  ```
  rahul@bqdev:~$ /etc/init.d/condor restart
  [ ok ] Restarting condor (via systemctl): condor.service.
  rahul@bqdev:~$ ps -ef | grep condor
  ```
  
  

## Planteome Deep Segment 



**This module segments a marked object (creating a graphical object) within an input image.**

Then the module will classify either the entire original image or the segment created in the first step. This uses PyTorch in order to do this deep segmentation.

#### [Reference](https://github.com/pndaly/BisQue_Platform_Guide) 
- Module Development Guide: https://github.com/pndaly/BisQue_Platform_Guide
- Sample Deep Learning Module: [Planteome Deep Segment Analysis](https://github.com/Planteome/planteome-deep-segmenter-dockerized)

#### Pre-requisite: [link](./bisque.md)
- Working bisque environment at http://loup.ece.ucsb.edu:8088/
- Docker should be enabled and setup on this environment
- The bisque deployment server should have access to good CPU & RAM since Torch & Scikit-image is used
- Access to module folder for deploying this module (Say at ~/bisque/module)


#### Add/Deploy the [Planteome module](https://github.com/Planteome/planteome-deep-segmenter-dockerized)
- Identify the bisque module folder at path ~/bisque/module
- Login to http://loup.ece.ucsb.edu:8088/ with credentials admin:admin
- Download the [Planteome module](https://github.com/Planteome/planteome-deep-segmenter-dockerized) to the bisque module folder
- Register the module to Bisque by opening the Module Manager
![Module Manager Menu](img/module_planteome/module_menu.png?raw=true) 
- Provide the engine URL(http://loup.ece.ucsb.edu:8088/engine_service) in the Engine service section and click load. This will list the modules by querying the engine service. 
- Select the module named "Planteome", drag this module to the left pane and drop it to register. 
![Module Register](img/module_planteome/module_add_planteome.png?raw=true) 


#### Build/Configure the module 
- Edit the runtime-module.cfg with relevant configuration
  - Changed the docker.hub configuration to biodev.ece.ucsb.edu:5000
- View/Edit the Dockerfile for the relevant packages 
  - Edited versions for numpy\==1.16.1 & scikit-image\==0.14.2
- Source to your bisque python environment "workon bqenv" for installation
  - pip install -r requirements.txt
  - python setup.py

This will install all the dependency in the modules requirements.txt file, build modules code, and create a docker image for running it. Based on your "docker.hub" configuration the setup will push the docker image to registry as [biodev.ece.ucsb.edu:5000/bisque_uplanteome]



#### Steps to run the algorithm from Bisque Client

- Select an image to be analyzed.
- The foreground/background annotation tooltip can be found on the top-right of the module image viewer. Mark the part of the image to be segmented with foreground line(s) annotation(s). Mark the image with background annotations around the object to be segmented.
![Test Segmentation Setup](img/module_planteome/setup_planteome.png?raw=true)

- Select which deep classifier to use, whether to segment the image, the segmentation quality, and whether to classify the entire image or the segmented object instead.

- Press the 'RUN' button. Analysis may take some time depending on the image and segmentation quality.

- Results are given in visual and table formats, depending on whether the segmentation and classification functionalities respectively were enabled in the options. 
![Test Segmentation Results](img/module_planteome/results_planteome.png?raw=true)


#### Isolated/Development test setup

Make sure you have annotated the image and have an a mex identifier availablefor manual test/run. 
This can be done by,
- Opening the module and select image
- Annotate the image as per the directions above
- Configure and run the module once
- Make note of the mex URL for this run by looking at the docker_run.log in the staging folder. This will be used for replaying the test run from the modules folder.

Additional module execution information
- When we annotate the image and click RUN on the module user interface
- The Planteome module is created from biodev.ece.ucsb.edu:5000/bisque_uplanteome:latest

    ```
    docker create biodev.ece.ucsb.edu:5000/bisque_uplanteome \
    python PlanteomeDeepSegment.py \
    http://loup.ece.ucsb.edu:8088/module_service/mex/00-NKpU4CWiHfupgckuXBNFDd \
    admin:00-NKpU4CWiHfupgckuXBNFDd Simple True 3 False
    ```
- This module is run with the hash id of the docker create 


##### Setup/Run the docker container for test

- Build the docker container
  - docker build --no-cache -t bisque_uplanteome -f Dockerfile .
  - docker build  -t biodev.ece.ucsb.edu:5000/bisque_uplanteome .
- Run the container and bash into it
  - docker run -it --net=host biodev.ece.ucsb.edu:5000/bisque_uplanteome bash
- test run the code on mex identifier
  - python PlanteomeDeepSegment.py http://loup.ece.ucsb.edu:8088/module_service/mex/00-NKpU4CWiHfupgckuXBNFDd admin:00-NKpU4CWiHfupgckuXBNFDd Simple True 3 False





------------
Version: 0.3
Author(s): Dimitrios Trigkakis, Justin Preece





## Rancher 2.0 Setup (w/Kubernetes engine)


### Pre-requisite
- [Rancher 2.0 with bq-cluster workload instructions](/guides/rancher2_bisque)
- [Rancher 2.0 with PostgreSql workload instructions](/guides/rancher2_postgresql)

##### General instructions
- Setup Cluster with [/rke-clusters/custom-nodes](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/custom-nodes/)

##### Note: Assuming we have bq-cluster with postgres and persistent volumes

---------------------------------
### Setup Workload on the bq-cluster

Bisque Test environment where workloads are deployed with open NodePort
https://rancher.com/managing-kubernetes-workloads-with-rancher-2-0/

- We will be using the image at custom registry biodev.ece.ucsb.edu:5000/ucsb-bisque05-svc or another available at [vishwakarmarhl/ucsb-bisque05-svc:dev](https://hub.docker.com/r/vishwakarmarhl/ucsb-bisque05-svc)

##### Bisque Service Workload configuration
- Name: ucsb-bisque05-svc
- Pods: 2
- Docker Image : biodev.ece.ucsb.edu:5000/bisque-caffe-xenial:dev or [vishwakarmarhl/ucsb-bisque05-svc:dev](https://hub.docker.com/r/vishwakarmarhl/ucsb-bisque05-svc)
- Port Mapping: 8080-tcp-NodePort-Random & 27000-tcp-NodePort-Random 
- Environment Variables: Copy paste the "Environment Configuration" section 
- Node Scheduling: Run all the pods on a particular host
- Health Check: No change
- Volumes: Persistent Volume claim and set the mount point as /run/bisque
- Scaling: No change
- Command: No Change
- Networking: No Change
- Labels: No change
- Security: No change

##### Environment Configuration

- Bisque service variables
```
      BISQUE_USER= bisque
      BISQUE_BISQUE_ADMIN_EMAIL= admin@loup.ece.ucsb.edu
      BISQUE_BISQUE_BLOB_SERVICE_STORES= blobs,local
      BISQUE_BISQUE_STORES_BLOBS_MOUNTURL= file://$$datadir/blobdir/$$user/
      BISQUE_BISQUE_STORES_BLOBS_TOP= file://$$datadir/blobdir/
      BISQUE_BISQUE_STORES_LOCAL_MOUNTURL= file://$$datadir/imagedir/$$user/
      BISQUE_BISQUE_STORES_LOCAL_READONLY= true
      BISQUE_BISQUE_STORES_LOCAL_TOP= file://$$datadir/imagedir/
      BISQUE_DOCKER_DOCKER_HUB= biodev.ece.ucsb.edu:5000
      BISQUE_SECRET= bq123
      BISQUE_UID= 12027
      BISQUE_RUNTIME_STAGING_BASE= /run/bisque/data/staging
      BQ__BISQUE__IMAGE_SERVICE__WORK_DIR= /run/bisque/local/workdir
      BQ__BISQUE__PATHS__DATA= /run/bisque/data
      MAIL_SERVER= dough.ece.ucsb.edu
      BISQUE_DBURL=postgresql://postgres:postgres@10.42.0.15:5432/postgres

      DEBIAN_FRONTEND=noninteractive
      IMGCNV=imgcnv_ubuntu16_2.4.3
```
##### Condor provisioning 

Condor Master 
- Image: biodev.ece.ucsb.edu:5000/condor 
- Ports: 9618, 9886 as HostPort
- Environment
```
CONDOR_DAEMONS = COLLECTOR,MASTER,NEGOTIATOR,SCHEDD,SHARED_PORT
CONDOR_MANAGER_HOST = master
```

Condor Worker
- Same configuration as above
- Ports: 9886 NodePort Random

##### Workload (bq-cluster) Dashboard
![Rancher Workload Dashboard](img/bqranch/workload_bisque_dash.png?raw=true)



----------------------------------------
## Connoisseur/GPU Workload provisioning
----------------------------------------
Here lets take a look at connoissuer service in Bisque. We will run a Bisque H1 server disabling the engine_service and connoisseur in part (A) and later another service in part (B) with Connoisseur and engine service for the actual compute. 

##### Pre-requisites
- Create a namespace "connoisseur" and deploy everything isolated from the existing bisque-svc
- Create a postgres database for this deployment
  - psql -h 10.42.0.15 -U postgres --password -p 5432 postgres
  - "create database connoissuer;"
  - "grant all privileges on database connoisseur to postgres;"
- Create a volume bqcon-vol mounted at 192.168.1.123:/opt/bisque/connoisseur over NFS 

#### A.) Bisque Client Service Workload configuration
- Name: bq-connoisseur-client-svc
- Pods: 1
- Docker Image : biodev.ece.ucsb.edu:5000/bisque-caffe-xenial:dev
- Port Mapping: 80-tcp-NodePort-Random & 27000-tcp-NodePort-Random 
- Environment Variables: Copy paste the "Environment Configuration" section 
- Node Scheduling: Run all the pods on a particular host that has GPU, Say "arkady" 
- Health Check: No change
- Volumes: Persistent Volume claim of bqcon-vol and set the mount point as /run/bisque/
- Scaling: No change
- Command: No Change
- Networking: No Change
- Labels: No change
- Security: No change

##### Environment Configuration

- Bisque client service variables
```
      BISQUE_USER= bisque
      BISQUE_BISQUE_ADMIN_EMAIL= admin@loup.ece.ucsb.edu
      BISQUE_BISQUE_BLOB_SERVICE_STORES= blobs,local
      BISQUE_BISQUE_STORES_BLOBS_MOUNTURL= file://$$datadir/blobdir/$$user/
      BISQUE_BISQUE_STORES_BLOBS_TOP= file://$$datadir/blobdir/
      BISQUE_BISQUE_STORES_LOCAL_MOUNTURL= file://$$datadir/imagedir/$$user/
      BISQUE_BISQUE_STORES_LOCAL_READONLY= true
      BISQUE_BISQUE_STORES_LOCAL_TOP= file://$$datadir/imagedir/
      BISQUE_DOCKER_DOCKER_HUB= biodev.ece.ucsb.edu:5000
      BISQUE_SECRET= bq123
      BISQUE_UID= 12027
      BISQUE_RUNTIME_STAGING_BASE= /run/bisque/data/staging
      BQ__BISQUE__IMAGE_SERVICE__WORK_DIR= /run/bisque/local/workdir
      BQ__BISQUE__PATHS__DATA= /run/bisque/data
      MAIL_SERVER= dough.ece.ucsb.edu
      DEBIAN_FRONTEND=noninteractive
      IMGCNV=imgcnv_ubuntu16_2.4.3
      BISQUE_DBURL=postgresql://postgres:postgres@10.42.0.15:5432/connoisseur
      BISQUE_SERVERS_H1_SERVICES_DISABLED = engine_service,connoisseur
```

####  B.) Bisque Engine Service Workload configuration
- All the same configuration as above but skipping one environment variable
- Skip the disable variable --> BISQUE_SERVERS_H1_SERVICES_DISABLED = engine_service,connoisseur

- Verify connoisseur GPU variables 
```
      NVIDIA_REQUIRE_CUDA=cuda>=8.0
      NVIDIA_VISIBLE_DEVICES=all
      NVIDIA_DRIVER_CAPABILITIES=compute,utility
      CUDA_PKG_VERSION=8-0=8.0.61-1
      CAFFE_PKG_VERSION=0.15.13-1ubuntu16.04+cuda8.0
      CAFFE_VERSION=0.15
      CUDA_VERSION=8.0.61

      CONDOR_MANAGER_HOST=master.condor
      CONDOR_DAEMONS=MASTER,SCHEDD,SHARED_PORT

```

##### Workload (bq-cluster) with Namespace Connoissuer
![Rancher Workload Dashboard](img/bqranch/workload_connoisseur.png?raw=true)


-----
TODO:  
- Fix Caffe and CUDA within the image 
- Probably write your own Docker file for a new CUDA/GPU enabled container

```
bisque@bq-connoisseur-engine-svc-74755f798b-6lbgk:/source$ caffe deveice_query -gpu all
E0213 00:36:24.855478  1798 caffe.cpp:77] Available caffe actions:
E0213 00:36:24.856573  1798 caffe.cpp:80]       device_query
E0213 00:36:24.856690  1798 caffe.cpp:80]       test
E0213 00:36:24.856793  1798 caffe.cpp:80]       time
E0213 00:36:24.856899  1798 caffe.cpp:80]       train
F0213 00:36:24.857019  1798 caffe.cpp:82] Unknown action: deveice_query
*** Check failure stack trace: ***
    @     0x7fafbc7c65cd  google::LogMessage::Fail()
    @     0x7fafbc7c8433  google::LogMessage::SendToLog()
    @     0x7fafbc7c615b  google::LogMessage::Flush()
    @     0x7fafbc7c8e1e  google::LogMessageFatal::~LogMessageFatal()
    @           0x40863a  main
    @     0x7fafbb218830  __libc_start_main
    @           0x408dd9  _start
    @              (nil)  (unknown)
Aborted (core dumped)

```

