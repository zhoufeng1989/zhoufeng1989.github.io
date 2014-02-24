---
layout: post
title: Useful Python command options
tags: [Python]
---
#Useful Python command options   
1.  **-c command**: specify the command to execute, This terminates the option list(following options are passed as arguments to the command).    
        
    {% highlight bash %}
    $ python --version           
    => Python 2.7.2
    $ python -c 'import sys; print sys.argv' --version
    => ['-c', '--version'] # --version is passed as argument
    {% endhighlight %}

2.  **-i**: When a script is passed as first argument or the -c option is used, enter interactive mode after executing the script or the command. It does not read the $PYTHONSTARTUP file.This can be useful to inspect global variables or a stack trace when a script raises an exception. 

    for example, we hava a Python script, called test.py:
    
    {% highlight python %}
    x = 1
    try:
        raise KeyError('key error here!')
    except KeyError, e:
        pass
    {% endhighlight %}

    then execute:    

    {% highlight bash %}
    $ python -i test.py
    >>> print x
    1
    >>> print e
    KeyError('key error here!',)
    {% endhighlight %}

3. **-m module_name**: Searches sys.path for the named module and runs the corresponding .py file as a script.
    for example:   

    {% highlight bash %}
    calendar:

    $ python -m calendar  # shows the calendar of current year
    $ python -m calendar y m  # shows the calendar of the month m in year y
    $ python -m calendar -h # -h option for more helps
    {% endhighlight %}    

    {% highlight bash %}
    zipfile:

    $ python -m zipfile
     Usage:
        zipfile.py -l zipfile.zip        # Show listing of a zipfile
        zipfile.py -t zipfile.zip        # Test if a zipfile is valid
        zipfile.py -e zipfile.zip target # Extract zipfile into target dir
        zipfile.py -c zipfile.zip src ... # Create zipfile from sources
    {% endhighlight %}

    {% highlight bash %}
    json

    $ echo '{"a":"b", "c": "d"}' | python -m 'json.tool'
    {
        "a": "b", 
        "c": "d"
    }
    {% endhighlight %}

    {% highlight bash %}
    http server: start a http server in current directory, convenient to share files.

    $ python -m 'SimpleHTTPServer' 1234
    Serving HTTP on 0.0.0.0 port 1234 ...

    also an CGI version:
    
    $ python -m CGIHTTPServer 1234
    {% endhighlight %}

    {% highlight bash %}
    pydoc:

    $ python -m pydoc
    pydoc - the Python documentation tool

    pydoc.py <name> ...
        Show text documentation on something.  <name> may be the name of a
        Python keyword, topic, function, module, or package, or a dotted
        reference to a class or function within a module or module in a
        package.  
        If <name> contains a '/', it is used as the path to a
        Python source file to document. If name is 'keywords', 'topics',
        or 'modules', a listing of these things is displayed.

    pydoc.py -k <keyword> #maybe slow
        Search for a keyword in the synopsis lines of all available modules.

    pydoc.py -p <port>  # only for localhost
        Start an HTTP server on the given port on the local machine.

    pydoc.py -g
        Pop up a graphical interface for finding and serving documentation.

    pydoc.py -w <name> ...
        Write out the HTML documentation for a module to a file in the current
         directory.  If <name> contains a '/', it is treated as a filename; if
        it names a directory, documentation is written for all the contents.
    {% endhighlight %}

    {% highlight bash %}
    pdb:debug python programes step by step

    $ python -m pdb scripname [arg] ... 
    {% endhighlight %}
    
    {% highlight bash %}
    doctest:

    $ python -m doctest scriptname
    {% endhighlight %}

    {% highlight bash %}
    timeit:

    $ python -m timeit
    {% endhighlight %}

    {% highlight bash %}
    filecmp: compare two directories:

    $ python -m filecmp dir1 dir2
    {% endhighlight %}

    At last , the Zen of Python:

    {% highlight bash %}
    $ python -m this
    {% endhighlight %}


### resources:  
1.  the [e module](https://pypi.python.org/pypi/e). like `python -c command`, but more powerful.
2.  [stackoverflow](http://stackoverflow.com/questions/14545001/what-tools-are-available-in-python-standard-library/14545364) discussion.
2.  [Pyp](https://code.google.com/p/pyp/) is a linux command line text manipulation tool similar to awk or sed, but which uses standard python string and list methods as well as custom functions evolved to generate fast results in an intense production environment.
