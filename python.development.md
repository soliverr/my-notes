---
id: msw83vcsr4hyv3kvdvoxgde
title: Development
desc: ''
updated: 1646113068259
created: 1630473623206
---

## Python packages and package documentation

* [Pipy - package searching](https://pypi.org/)
* [ReadTheDocs - package documentation](https://readthedocs.org/)

## PyUp

[Python Dependency Security](https://pyup.io/) - Keep your Python dependencies **secure**, **up-to-date** & **compliant**.

## Полезные пакеты

* [Watchdog](https://pypi.org/project/watchdog/)
    * <https://python-watchdog.readthedocs.io/>
* [Pykka](https://pypi.org/project/pykka/)
    * <https://pykka.readthedocs.io/en/stable/>

## Профилирование

* [Top 5 Python Memory Profilers](https://stackify.com/top-5-python-memory-profilers/)
* [MemoryProfiler](https://github.com/pythonprofilers/memory_profiler)
* https://mg.pov.lt/
    * [objgraph](https://github.com/mgedmin/objgraph)
    * [Dozer](https://github.com/mgedmin/dozer)
* [Valgrind](https://valgrind.org/)
* [How can I profile memory of multithread program in Python?](https://stackoverflow.com/questions/4799166/how-can-i-profile-memory-of-multithread-program-in-python)

## Метрики приложения

* [AppMetrics](https://github.com/avalente/appmetrics)
    * https://pypi.org/project/AppMetrics/
* [Prometeus Client](https://github.com/prometheus/client_python)
    * https://pypi.org/project/prometheus-client/
* [Elasticsearch Python Client](https://github.com/elastic/elasticsearch-py)

## Некоторые полезные техники

### Dict to Class

[Python 101: How to Change a Dict Into a Class](https://www.blog.pythonlibrary.org/2014/02/14/python-101-how-to-change-a-dict-into-a-class/)

```python
class Dict2Obj(object):
    """
    Turns a dictionary into a class
    """

    #----------------------------------------------------------------------
    def __init__(self, dictionary):
        """Constructor"""
        for key in dictionary:
            setattr(self, key, dictionary[key])
        
    #----------------------------------------------------------------------
    def __repr__(self):
        """"""
        return "" % self.__dict__

    #----------------------------------------------------------------------
    #def __repr__(self):
    #    """"""
    #    attrs = str([x for x in self.__dict__])
    #    return "" % attrs
    
#----------------------------------------------------------------------
if __name__ == "__main__":
    ball_dict = {"color":"blue",
                 "size":"8 inches",
                 "material":"rubber"}
    ball = Dict2Obj(ball_dict)
```
