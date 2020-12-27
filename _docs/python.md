---
title: "Python"
---

## Code style and linting
+ Please conform to the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html).
+ A good code formatter is `autopep8`. `autopep8` automatically formats Python code to conform to the PEP 8 style guide. Install `autopep8` via pip:
    ```bash
    $ pip install --upgrade autopep8     
    ```

## Naming
+ Python filenames must have a `.py` extension.
+ Avoid dashes (-) in any file/package/module/definition name.
+ While Python supports making things private by using a leading double underscore __ (aka. “dunder”) prefix on a name, this is discouraged. Prefer the use of a single underscore.
+ Naming guide:

    | Type                       | Public             | Internal                       |
    | :--------------------------|:-------------------| :------------------------------|
    | Packages	                 | lower_with_under   | 	                           |
    | Modules	                 | lower_with_under   |	_lower_with_under              |
    | Classes                    | CapWords           |	_CapWords                      |
    | Exceptions	             | CapWords           |	                               |
    | Functions	                 | lower_with_under() |	_lower_with_under()            |
    | Global/Class Constants	 | CAPS_WITH_UNDER    | _CAPS_WITH_UNDER               |
    | Global/Class Variables	 | lower_with_under   | _lower_with_under              |
    | Instance Variables         | lower_with_under   | _lower_with_under              |
    | Method Names               | lower_with_under() | _lower_with_under()            |
    | Function/Method Parameters | lower_with_under   |	                               |
    | Local Variables	         | lower_with_under   |                                |	

## Machine learning folder structure
The following serves as a representative project folder structure.

```text
emotion-recognition                             # Repository name / Project root 
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
├── dev                                         # Machine learning model folder 
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
├── graph                                       # TensorFlow graph folder 
|   └── cnn.py                                  # Graph architecture
├── serving                                     # TensorFlow Serving 
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
1. [<span style="color:blue">Non-Production Code</span>] `dev` folder contains all peripheral code used in developing and testing the machine learning tensorflow graph. 
    + Contains code files (i.e., `emoRec.py`) used during training, testing, and building phase of the machine learning model.
    + Local code should be abstracted into packages and modules. For example, `analysis` and `helper` are local packages, whereas `predictions.py` and `convert.py` are local modules.
    + Package and module naming should be intuitive and non-repetitive. For example, reading  an import statement in Python, such as `import model.analysis.predictions`, should clearly indicate the meaning or functionality of the code being imported.
    + A `checkpoints` sub-folder is desirable to keep track of previously trained architecture and weights. 
1. [<span style="color:green">Production Code</span>] `lib` folder is a top level folder containing well abstracted local (i.e., self-written) and third party libraries, which will be ported into production.
1. [<span style="color:green">Production Code</span>] `graph` folder is a top level folder containing well abstracted and commented machine learning TensorFlow graph written in Python. The graph (e.g., `cnn.py`) is converted into `SavedModel` format and saved with an identical name in the `serving` folder (e.g., `cnn`). Although the contents of this folder are not directly used in production, the Python TensorFlow graphs are needed to understand the corresponding `serving` which was actually deployed. 
1. [<span style="color:green">Production Code</span>] `serving` folder. Please see `TensorFlow` wiki to learn more about the structure of `serving` folder. The `serving` model will be deployed using Docker containers in production.
1. [<span style="color:green">Production Code</span>] `.env` file should contain the environment variables, e.g., `ROOT` variable, which will be ported into production.
1. [<span style="color:blue">Non-Production Code</span>] `README.md` file shall contain a brief description of the following:
    + Explanation of what the project is about
    + Instructions to run a sample of the code
    + Desired input and output of the machine learning model
1. [<span style="color:green">Production Code</span>] `requirements` file contains the dependent libraries and their version require to run this code. For details on how the `requirements.txt` file is generated, refer to the [dependency](#dependencies) management section.   

Note: All <span style="color:green">Production Code</span> needs to undergo code review before being merged into the master branch. Whereas, coding standards of <span style="color:blue">Non-Production Code</span> need not necessarily be scrutinised.

## Documentation
+ Variables, functions, and methods, which are only meant for local use within a library, must be made private and non-exportable. Add “_” (single underscore) in front of the name to hide them when accessing them from out of class. For example, `_x` and `def _normFace(self, img, face):` represent a hidden variable and a hidden method, respectively.
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
        $ sphinx-quickstart [options] path/to/project/root/doc

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
    + Populate your `/doc/index.rst` file as needed. An example `index.rst` file for the Emotion Recognition template project is as follows.
        ```rst
        Welcome to Emotion-Recognition's documentation!
        ===============================================

        .. toctree::
            :maxdepth: 2
            :caption: Contents:

        .. automodule:: lib.preprocess
            :members:

        .. automodule:: lib.util
            :members:

        Example
        =======

        .. automodule:: example.emoRecStream

        Indices and tables
        ==================

        * :ref:`genindex`
        * :ref:`modindex`
        * :ref:`search`
        ```   
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
        ├── graph                                   # TensorFlow graph folder 
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
        from graph import cnn
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
    Example command assuming the project is located at `home/admin/src/github.com/scalable-deployment/tfsemonet`.
    ```bash
    $ pipreqs --force /home/admin/src/github.com/scalable-deployment/tfsemonet
    ```
+ To install dependencies, issue the following command.
    ```bash
    $ pip install -r /path/to/requirements.txt
    ```

## Profiling
1. For profiling, install the following libraries.
    ```bash
    $ pip install cProfile 
    $ pip install pstats
    $ pip install line_profiler 
    ```
1. Include the following `profiler.py` file in your project.    
    ```python
    # Filename: profiler.py
    import os
    from dotenv import load_dotenv, find_dotenv
    load_dotenv(find_dotenv())
    ROOT = os.getenv("ROOT")

    import cProfile
    import pstats
    import line_profiler
    import atexit
    profile_line = line_profiler.LineProfiler()
    atexit.register(profile_line.dump_stats, ROOT+"results/profile_line.prof")
    stream = open(ROOT+"results/profile_line.txt", 'w')
    atexit.register(profile_line.print_stats, stream)

    # Function profiling decorator
    def profile_function(func):
        def _profile_function(*args, **kwargs):
            pr = cProfile.Profile()
            pr.enable()
            result = func(*args, **kwargs)
            pr.disable()

            # save stats into file
            pr.dump_stats(ROOT+"results/profile_function.prof")
            stream = open(ROOT+"results/profile_function.txt", 'w')
            ps = pstats.Stats(ROOT+"results/profile_function.prof", stream=stream)
            ps.sort_stats('tottime')
            ps.print_stats()
            
            return result  
        return _profile_function
    ```
1. For profiling at function-call level, we use `cProfile`. Add the decorator `@profile_function` above the function to be profiled. For line-by-line profiling, we use `line_profiler`. Add the decorator `@profile_line` above the function to be profiled.
    ```python
    # Filename: main.py
    import profiler

    @profiler.profile_function
    def function_to_be_profiled_at_function_call_level():
        ...

    @profiler.profile_line
    def function_to_be_profiled_at_line_by_line_level():
        ...

    if __name__ == '__main__':
        function_to_be_profiled_at_function_call_level()
        function_to_be_profiled_at_line_by_line_level()
    ```
1. Then, execute the python file normally. For example 
    ```bash
    $ python main.py
    ```
    The function-level profile results will be written to `./results/profile_function.txt` and `./results/profile_function.prof` files. The line-level profile results will be written to `./results/profile_line.txt` and `./results/profile_line.prof` files.
1. To view the function-level profile output interactively in the browser, install `cprofilev` and read the `profile_function.prof` file using cprofilev.
    ```bash
    $ pip install cprofilev
    $ cprofilev -f /path/to/profile_function.prof
    ```
    Navigate to http://localhost:4000 to view the profile output.
1. Note: If you want to profile several functions, only instantiate once the `LineProfiler()` and import it in the other files. Otherwise, profiler output might have some issues and have weird reporting.

## Proxy
+ Setup proxy configurations for conda/pip when using conda/pip behind a corporate proxy, e.g., `http://10.0.0.0:8080/`.
+ For conda, run the following commands in a terminal
    ```bash
    $ conda config --set proxy_servers.http http://10.0.0.0:8080/
    $ conda config --set proxy_servers.https https://10.0.0.0:8080/
    ```
    A `.condarc` file will be created with the proxy server details and placed at `~/.condarc`.
+ For pip, create a `pip.conf` file at `~/.pip/` such that `~/.pip/pip.conf` file contains the following
    ```
    [global]
    proxy = http://10.0.0.0:8080/     
    ```

## Unit test
1. For info see : https://realpython.com/python-testing/
1. Run unit tests in python 
    ```bash
    $ cd /path/to/source/code/root
    $ python -m unittest discover -s <test folder>
    ```
    `unittest` will scan the `<test folder>` for all `test*.py` files and execute them.    
