--- 
site: bookdown::bookdown_site
documentclass: bookdown
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
description: "Bisque (Bio-Image Semantic Query User Environment) : Store, visualize, organize and analyze images in the cloud."
output:
  bookdown::gitbook:
    highlight: pygments
---



# Getting Started {-}


![](images/bisque_logo.svg)



![](images/storage.svg)


![](images/modules.svg)
__Our modules currently span research areas that include Material Science, Biological Science, Biomedical Imaging, and Marine Science.__

![](images/modulelist.svg)

__Users can develop and deploy custom state of the art modules by simply containerizing their module with Docker.__ 

## BisQue Docker Installation  {-}

> If you would like to have your own version of BisQue with minimal effort, use the Docker version of BisQue. The source code installation can be found [here](#source-code-installation).

1. Ensure you have the latest stable locally

    ```
      docker pull cbiucsb/bisque05:stable
    ```

2. Run a bisque server on the host port 8080:

    ```
      docker run --name bisque --rm -p 8080:8080 cbiucsb/bisque05:stable
    ```
  and point your browser at `http://<hostname>:8080`


3. Using host mounted modules to make it easier to edit.

   1. Copy the module directory  out of the container

      ```
         docker cp bisque:/source/modules container-modules
      ```

   2. restart the container with host mounted module

      ```
      docker stop bisque
      docker run --name bisque --rm -p 8080:8080 -v $(pwd)/container-modules:/source/modules  cbiucsb/bisque05:stable
      ```

4. Registering a module to your local server
   *  Login to your bisque server using admin:admin
   * Find the module manager under Admin button
   * put `http://localhost:8080/engine_service` in the right panel and hit load
     *  use localhost:8080 here because it's internal to the container.
   * Drag and Drop "MetaData" to the left panel


5.  Use an external data directory so you don't lose data when service stops
    - Uploaded image and workdirs are store in /source/data.  You can change this to be a host mounted directory with

    ```
     docker run --name bisque --rm -p 8080:8080 -v $(pwd)/container-data:/source/data  cbiucsb/bisque05:stable
    ```

    - The default sqlite database is stored inside the container at /source/data/bisque.db
    - The uploaded images are stored inside the container at /source/data/imagedir



