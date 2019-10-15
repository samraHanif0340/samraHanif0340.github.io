---
title: "Python"
---

## Code style
+ Follow the [PEP 8 Python style guide](https://www.python.org/dev/peps/pep-0008/), except TensorFlow uses 2 spaces instead of 4. 
+ Please conform to the [Google Python Style Guide](https://github.com/google/styleguide/blob/gh-pages/pyguide.md).
+ All naming of folders, files, and functions, shall follow `camelCase` format.

## Code linting
+ A good code formatter is `autopep8`.
+ `autopep8` automatically formats Python code to conform to the PEP 8 style guide.
+ Install `autopep8` via pip:
    ```bash
    $ pip install --upgrade autopep8     
    ```

## Machine learning folder structure

The following serves as a representative project folder structure.

```text
emotion-recognition                             # Main project directory 
├── assets                                      # Assets
|   ├── images
|       ...
|       ...
├── dataset                                     # Dataset
|   ├── fer2013.csv
|   └── happy-1.jpg
├── doc                                         # Documents
|   ├── lib                                     # Library packages
|       ...
|       ...
├── example                                     # Standalone example code 
|   └── emoRecStream.py
├── model                                       # Machine learning model folder 
|   ├── analysis                                # Package - Analysis    
|   |   └── predictions.py                      # Module - Predictions 
|   ├── checkpoints                             # Checkpoints    
|   |   ├── emotion_recognition_weights.h5      # Machine learning saved weights
|   |   └── emotion_recognition_structure.json  # Machine learning saved structure
|   ├── helper                                  # Package - Helper    
|   |   └── convert.py                          # Module - Convert 
|   └── emoRec.py                               # Code used during model development
├── lib                                         # Libraries
|   ├── emonetLabels.json                       # Labels
|   ├── haarcascade_frontalface_default.xml     # .xml file for ML models
|   └── preprocess.py                           # Pre-processing functions   
├── tfgraph                                     # TensorFlow graph folder 
|   └── cnn.py                                  # Graph architecture
├── tfserving                                   # TensorFlow Serving 
|   ├── cnn
|       ├── ...                                  
|       ...
├── .env                                        # Environment variables 
├── README.md                                   # Readme file 
└── requirements.txt                            # Code dependencies
```

1. [<span style="color:blue">Non-Production Code</span>] `assets` folder should contain miscellaneous files. For example, it can contain images used for explanation in `Readme.md`.
1. [<span style="color:blue">Non-Production Code</span>] `dataset` folder should contain minimal amount of sample test data used in the project for testing and demonstration purposes. Complete dataset is advised to be stored in an external Hadoop cluster.
1. [<span style="color:green">Production Code</span>] `doc` folder contains the documents generated from Python code comments. For details of how the documents are generated, refer to the [code documentation](#Documentation) steps.   
1. [<span style="color:blue">Non-Production Code</span>] `example` folder contains a complete, runnable, well-abstracted Python code to illustrate the entire machine learning model. For example, `emoRecStream.py` is a complete standalone example to analyse video stream.
1. [<span style="color:blue">Non-Production Code</span>] `model` folder contains all peripheral code used in developing and testing the machine learning tensorflow graph. 
    + Contains code files (i.e., `emoRec.py`) used during training, testing, and building phase of the machine learning model.
    + Local code should be abstracted into packages and modules. For example, `analysis` and `helper` are local packages, whereas `predictions.py` and `convert.py` are local modules.
    + Package and module naming should be intuitive and non-repetitive. For example, reading  an import statement in Python, such as `import model.analysis.predictions`, should clearly indicate the meaning or functionality of the code being imported.
    + A `checkpoints` sub-folder is desirable to keep track of previously trained architecture and weights. 
1. [<span style="color:green">Production Code</span>] `lib` folder is a top level folder containing well abstracted local (i.e., self-written) and third party libraries, which will be ported into production.
1. [<span style="color:green">Production Code</span>] `tfgraph` folder is a top level folder containing well abstracted and commented machine learning TensorFlow graph written in Python. The graph (e.g., `cnn.py`) is converted into `SavedModel` format and saved with an identical name in the `tfserving` folder (e.g., `cnn`). Although the contents of this folder are not directly used in production, the Python TensorFlow graphs are needed to understand the corresponding `tfserving` which was actually deployed. 
1. [<span style="color:green">Production Code</span>] `tfserving` folder. Please see `TensorFlow` wiki to learn more about the structure of `tfserving` folder. The `tfserving` model will be deployed using Docker containers in production.
1. [<span style="color:green">Production Code</span>] `.env` file should contain the environment variables, e.g., `ROOT` variable, which will be ported into production.
1. [<span style="color:blue">Non-Production Code</span>] `README.md` file shall contain a brief description of the following:
    + Explanation of what the project is about
    + Instructions to run a sample of the code
    + Desired input and output of the machine learning model
1. [<span style="color:green">Production Code</span>] `requirements` file contains the dependent libraries and their version require to run this code. For details on how the `requirements.txt` file is generated, refer to the [dependency management](#Dependencies) section.   

Note: All <span style="color:green">Production Code</span> needs to undergo code review before being merged into the master branch. Whereas, coding standards of <span style="color:blue">Non-Production Code</span> need not necessarily be scrutinised.

## Documentation
+ Variables, functions, and methods, which are only meant for local use within a library, must be made private and non-exportable. Add “__” (double underscore) in front of the name to hide them when accessing them from out of class. For example, `__x` and `def __normFace(self, img, face):` represent a hidden variable and a hidden method, respectively.
+ Hidden variables, functions, and methods, will not be included in the documentation.

+ Use [`numpy` formatted](https://numpydoc.readthedocs.io/en/latest/format.html) docstring to document the Python code. 
+ Docstrings must be written for 
    + Classes and class methods
    + Packages, modules, and functions
    + Scripts
+ Documents are stored in the `./doc` directory at the root of the project repository.
+ Sphinx is the desired tool to generate code documentation.
+ For Sphinx tool, generate documents using:
    ```bash
    $ pip install sphinx
    $ cd /path/to/project/root
    $ pdoc --html --force --output-dir doc ./lib
    ```
+ Alternatively for pdoc3 tool, generate documents using the following in the root directory of the project.
    ```bash
    $ pip install pdoc3
    $ cd /path/to/project/root
    $ pdoc --html --force --output-dir doc ./lib
    ```

## Import paths
+ Strictly do not perform relative import for any files or modules in Python.
+ Never perform wildcard imports such as
    ```python
    from libraries import *
    ```
+ Always perform absolute import. 
    1. Assume the following project structure.
        ```text
        emotion-recognition                         # Main project directory 
        ├── model                                   # Machine learning model folder 
        |   ├── analysis                            # Package    
        |   |   └── predictions.py                  # Module 
        |   ├── checkpoint                          # Checkpoint folder    
        |   |   └── emotion_recognition_weights.h5  # Machine learning saved weights
        |   └── emoRec.py                           # Python code 
        ├── tfgraph                                 # TensorFlow graph folder 
        |   └── cnn.py                              # Graph architecture
        └── .env                                    # Environment variables 
        ```
    1. First, install the `dotenv` library.
        ```python
        pip install -U python-dotenv
        ```
    1. Specify the `ROOT` environment variable, which refers to the directory path containing the project, in the `.env` file as follows.
        ```
        # File: emotion-recognition/.env
        ROOT = /home/admin/src/github.com/emotion-recognition
        ```
    1. Place the following piece of code at the top of the Python code file. It will add the project directory given by `ROOT` to `sys.path` and make it searchable by Python. 
        ```python
        # File: emotion-recognition/model/emoRec.py
        # Setup
        import os
        import sys
        from dotenv import load_dotenv, find_dotenv
        load_dotenv(find_dotenv())
        ROOT = os.getenv("ROOT")
        sys.path.append(ROOT)
        ```
    1. Import local packages or modules as follows.
        ```python
        # File: emotion-recognition/model/emoRec.py
        # Import local packages
        from tfgraph import cnn
        from model.analysis import predictions
        ```
    1. To open files in Python, create absolute paths by adding the file path within the project directory and the `ROOT`. An example is as follows. 
        ```python
        # File: emotion-recognition/model/emoRec.py
        model.load_weights(ROOT+"/model/checkpoint/emotion_recognition_weights.h5")  # Load weights for TensorFlow model
        ```
+ Only import Python packages and modules. Never import Python functions, i.e., `def`, directly into another Python file.

## Dependencies   
+ To generate `requirements.txt` file for Python dependencies.
    ```python
    pipreqs [options] <path to main project folder>

    [options]
        --force : to overwrite existing file
        --proxy <url> : when using behind a corporate proxy
    ```
    Example command assuming the project is located at `home/admin/src/github.com/scalable-deployment/tfsemonet` and we are behind a corporate proxy of `http://10.0.0.0:8080/`. 
    ```
    pipreqs --force --proxy http://10.0.0.0:8080/ /home/admin/src/github.com/scalable-deployment/tfsemonet
    ```

+ To install dependencies, issue the following command.
    ```python
    pip install -r /path/to/requirements.txt
    ```
