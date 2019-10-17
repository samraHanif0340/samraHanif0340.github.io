---
title: "Python"
---

## Code style and linting
+ Follow the [PEP 8 Python style guide](https://www.python.org/dev/peps/pep-0008/), except TensorFlow uses 2 spaces instead of 4. 
+ Please conform to the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html).
+ All naming of folders, files, and functions, shall follow `camelCase` format.
+ A good code formatter is `autopep8`. `autopep8` automatically formats Python code to conform to the PEP 8 style guide. Install `autopep8` via pip:
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
1. [<span style="color:green">Production Code</span>] `doc` folder contains the documents generated from Python code comments. For details of how the documents are generated, refer to the code [documentation](#documentation) steps.   
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
1. [<span style="color:green">Production Code</span>] `requirements` file contains the dependent libraries and their version require to run this code. For details on how the `requirements.txt` file is generated, refer to the [dependency](#dependencies) management section.   

Note: All <span style="color:green">Production Code</span> needs to undergo code review before being merged into the master branch. Whereas, coding standards of <span style="color:blue">Non-Production Code</span> need not necessarily be scrutinised.

## Documentation
+ Variables, functions, and methods, which are only meant for local use within a library, must be made private and non-exportable. Add “__” (double underscore) in front of the name to hide them when accessing them from out of class. For example, `__x` and `def __normFace(self, img, face):` represent a hidden variable and a hidden method, respectively.
+ Hidden variables, functions, and methods, will not be included in the documentation.
+ Write docstrings in `numpy` [format](https://numpydoc.readthedocs.io/en/latest/format.html) to document the Python code. 
+ Docstrings must be written for 
    + Classes, and class methods
    + Packages, and functions
    + Scripts
+ To do notes are written as part of docstrings. 
    ```python
    """
    TODO
    ----
    Blah blah.     
    """
    ````    
+ Documents are stored in the `./doc` directory at the root of the project repository.
+ Sphinx is the desired tool to generate code documentation.
    + Install Sphinx
        ```bash
        $ pip install sphinx
        $ pip install sphinx-rtd-theme
        ```
    + Make doc directory
        ```bash
        $ cd /path/to/project/root
        $ mkdir doc
        ```
    + Setup Sphinx   
        ```bash
        $ sphinx-quickstart [options] [path/to/project/root/doc]

        [options]
        -q
           QUIET. Skips interactive wizard
        -p PROJECT
            Project name
        -a AUTHOR 
            Author name
        -v VERSION
            Version of project
        --ext-autodoc
            Enable sphinx.ext.autodoc extension.    
        --ext-todo
            Enable sphinx.ext.todo extension.
        --ext-coverage
            Enable sphinx.ext.coverage extension.    
        --ext-mathjax
            Enable sphinx.ext.mathjax extension.    
        --ext-viewcode
            Enable sphinx.ext.viewcode extension.
        --extensions='sphinx.ext.napoleon'    
            Napoleon supports Google and NumPy styled docstrings.
        ```
        For example:
        ```bash
        $ cd /path/to/project/root
        $ sphinx-quickstart \
            -q -p Emotion-Recognition -a Adaickalavan \
            -v v0.0.1 \
            --ext-autodoc \
            --ext-todo \
            --ext-coverage \
            --ext-mathjax \
            --ext-viewcode \
            --extensions='sphinx.ext.napoleon' \
            ./doc
        ```
    + Change the following in `/doc/conf.py` file
        ```python
        ...
        ...
        # import os
        # import sys
        # sys.path.insert(0, os.path.abspath('.'))
        ...
        ...
        html_theme = 'alabaster'
        ...
        ...
        ```
        to
        ```python
        ...
        ...
        import os
        import sys
        sys.path.insert(0, os.path.abspath('..'))
        ...
        ...
        html_theme = 'sphinx_rtd_theme'
        ...
        ...
        ```
    + Populate your master file at `/doc/index.rst` as needed.    
    + Generate documents.
        ```bash 
        $ cd /path/to/project/root
        $ make -C ./doc html
        ```
    + View the Sphinx documentation by browsing `/doc/_build/html/index.html` in your web browser.
+ Alternatively, for quick and easy document generation, pdoc3 tool may be used. 
    + Execute the following commands at the project's root directory.
        ```bash
        $ pip install pdoc3
        $ cd /path/to/project/root
        $ mkdir doc1
        $ pdoc --html --force --output-dir doc1 ./lib
        ```
    + View the pdoc3 documentation by browsing `/doc1/lib/index.html` in your web browser.    

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
    pipreqs [options] /path/to/project/root

    [options]
    --force
        to overwrite existing file
    --proxy <url> 
        when using behind a corporate proxy
    ```
    Example command assuming the project is located at `home/admin/src/github.com/scalable-deployment/tfsemonet` and we are behind a corporate proxy of `http://10.0.0.0:8080/`. 
    ```bash
    $ pipreqs --force --proxy http://10.0.0.0:8080/ /home/admin/src/github.com/scalable-deployment/tfsemonet
    ```
+ To install dependencies, issue the following command.
    ```python
    pip install -r /path/to/requirements.txt
    ```

## Configuration
+ Setup proxy configurations for pip when using pip behind a corporate proxy, e.g., `http://10.0.0.0:8080/`. 
+ To achieve this, create a `pip.conf` file at `~/.pip/` such that `~/.pip/pip.conf` file contains the following.
    ```
    [global]
    proxy = http://10.0.0.0:8080/     
    ```