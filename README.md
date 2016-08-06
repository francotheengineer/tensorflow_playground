# Tensor Flow Playgrounds in Various Virtual Environments 

### Introduction: 
      Here are some lessons learned and working approaches after many trials-and-errors to build tensor flow experiment virtual environments on Mac OSX. The built playgrounds should be able to experiment with various input / output interactively. Many online articles and FAQs are actually not working correctly, at least on my MacBook. 
      Two kinds of virtual environments are verified, 
        (1) python virtual environment
        (2) docker/container
      This one describes a tensorflow container with novnc, so that X11 GUI accessible via browser. Other verified ones are also listed in appendix.

### Setup the runtime playground container
        docker pull quay.io/draft/tensorflow-novnc
        docker run -v $PWD:/source -d -p 8080:8080 quay.io/draft/tensorflow-novnc
        open http://localhost:8080/vnc.html

        // It might be easier to experiment with CLI and see the output figures on browser
        docker exec -it <tensorflow-novnc container id> bash

### Appendix:
#### 1. Python virutal environment 
    (1) install specific python version
        brew update
        brew install pyenv 
        brew install pyenv-virtualenv
        pyenv install 3.5.2  // install Python 3.5.2
        
    (2) create a virtual environment and activate it 
        eval "$(pyenv init -)" // add this line to ~/.bash_profile if needed
        pyenv virtualenv 3.5.2 tf-py352  // create virtual environment (tf-py352) with Python 3.5.2
        pyenv activate tf-py352 // activate tf-py352 virtual environment
        
        Related commands:
        pyenv deactivate <virtual-env> // deactivate specified virtual environment
        pyenv uninstall <virtual-env> // delete specified virtual environment
        pyenv versions // list installed python versions
        pyenv virtualenvs // list created virtual environments

    (3) install libraries/modules within virtual environment 
        pip install numpy matplotlib ipython jupyter
        export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/mac/cpu/tensorflow-0.10.0rc0-py3-none-any.whl
	    pip install --upgrade $TF_BINARY_URL
        
    (4) setup matplotlib backends on Mac
        // get matplotlib configuration file place
        >>> import matplotlib
        >>> matplotlib.matplotlib_fname()
        // change backend to TkAgg via editor
        
        Notes:
        <1> default backend "macos" doesn't work with python virtual environments. furthermore, the workarounds listed on matplotlib.org's FAQ don't work as well. (http://matplotlib.org/faq/virtualenv_faq.html)
        <2> backend "Qt4Agg" doesn't work as well. To use it, it's needed to have PySide, which will take quite a long time to build. Even worse, after time/space spent for setup build environment/tool (Xcode w/ right SDK version) and built it successfully, it just still not work...
    
    (5) experiment with python 
        // when use matplotlib.pyplot, its output will show on an interactive window 
        
    (6) experiment with ipython
        // when use matplotlib.pyplot, its output will show on an interactive window
        
    (7) experiment with jupyter
        jupyter notebook // show a new tab for the notebook on the default browser 
        // if you'd like the output figure shown inlined instead of a pop out interactive window, you should add follow line.
        %matplotlib inline
        
#### 2. Docker/container:
    (1) download official tensor flow Docker image & launch it
        docker pull gcr.io/tensorflow/tensorflow
        <1> Jupyter notebook
            docker run -it -p 8888:8888 -v ${PWD}:/source --rm gcr.io/tensorflow/tensorflow
            // remember to add "%matplotlib inline" to show matplotlib figures
            
        <2> CLI
            docker run -it -p 8888:8888 -v ${PWD}:/source --rm --entrypoint bash gcr.io/tensorflow/tensorflow
            // can not show matplotlib figures
            
    (2) download custom Docker image with tensor flow & novnc
        // customize official docker image by adding novnc, to enable X11 GUI access via browser
        // refer to "Setup the runtime playground container" section