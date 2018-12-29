# **Composite Strength Module**
### Amil Khan, Marat Latypov | Version 2
***

## Description
This module implements a surrogate model that predicts effective yield strength of a composite given its 3-D microstructure. The model is developed for composites with strength contrast of 5: strength of hard phase s2/s1 = 5. The model works best on periodic 3-D microstructures. Calibration of the model is done by finite element simulation data by multivariate polynomial regression between principal component scores of 2-point statistics (representing microstructure) and effective yield strength (property). 

![Workflow](https://bisque.ece.ucsb.edu/module_service/Composite_Strength/public/marat_workflow.png)

## BisQue Platform

BisQue is a free, open source web-based platform for the exchange and exploration of large, complex datasets. It is being developed at the Vision Research Lab at the University of California, Santa Barbara. BisQue specifically supports large scale, multi-dimensional multimodal-images and image analysis. Metadata is stored as arbitrarily nested and linked tag/value pairs, allowing for domain-specific data organization. Image analysis modules can be added to perform complex analysis tasks on compute clusters. Analysis results are stored within the database for further querying and processing. The data and analysis provenance is maintained for reproducibility of results. BisQue can be easily deployed in cloud computing environments or on computer clusters for scalability. BisQue has been integrated into the NSF Cyberinfrastructure project CyVerse.


## Getting Started

Here is a preview of a logged in [BisQue](https://bisque.ece.ucsb.edu/client_service/) user, me. Don't judge me too much. 

![bisque](https://github.com/amilworks/BisQue_CompositeStrength/blob/master/public/interface.png)

Next, to see the module in all its glorious action, click on __Analyze__ at the top, find and select __Composite Strength__. You should arrive at a page that looks like this. 

To run your analysis, upload your HDF5 file, select either your own `Reducer` and `Predictor`, or our calibrated ones. 

Note: If your file needs Dream3D, we gotchu. Head over to the Dream3D module and upload the pipeline and your Dream3D file. Hit run, get your output HDF5, and run it through Composite Strength.

Another Note: I am making the huge assumption that you are familiar with Material Science or know what you're doing.

## Results 

![bisque](https://github.com/amilworks/BisQue_CompositeStrength/blob/master/public/composite_strength_GUI2.png)

Once your run is complete, you will see the results in the block below. If your input was a dataset, you would see results for each table. Therein, you can go through each table to make sure everything makes sense.
