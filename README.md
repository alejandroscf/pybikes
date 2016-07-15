pybikes ![build](https://api.travis-ci.org/eskerda/pybikes.svg?branch=master)
=======
![pybikes](http://citybik.es/files/pybikes.png)

pybikes provides a set of tools to scrape bike sharing data from different
websites and APIs, thus providing a coherent and generalized set of classes
and methods to access this sort of information.

The library is distributed and intended mainly for statistics and data
sharing projects. More importantly, it powers the [CityBikes] [1] project, and
is composed of a set of classes and a pack of data files that provide instances
for all different systems.

Installation
------------

Install directly from GitHub:
```bash
pip install git+https://github.com/eskerda/pybikes.git
```

Or after downloading/cloning the source:
```bash
python setup.py install
```

The following dependencies are required (example using Ubuntu package manager):
```
sudo apt-get install python
sudo apt-get install python-setuptools
sudo apt-get install libxml2 libxml2-dev libxslt1-dev
```


Usage
-----
```python
>>> import pybikes

# Capital BikeShare instantiation data is in bixi.json file
>>> capital_bikeshare = pybikes.get('capital-bikeshare')

# The instance contains all possible metadata regarding this system
>>> print(capital_bikeshare.meta)
{
    'name': 'Capital BikeShare',
    'city': 'Washington, DC - Arlington, VA',
    'longitude': -77.0363658,
    'system': 'Bixi',
    'company': 'PBSC',
    'country': 'USA',
    'latitude': 38.8951118
}
# The update method retrieves the list of stations
>>> print(len(capital_bikeshare.stations))
0
>>> capital_bikeshare.update()
>>> print(len(capital_bikeshare.stations))
191
>>> print(capital_bikeshare.stations[0])
--- 31000 - 20th & Bell St ---
bikes: 7
free: 4
latlng: 38.8561,-77.0512
extra: {
    'installed': True,
    'uid': 1,
    'locked': False,
    'removalDate': '',
    'installDate': '1316059200000',
    'terminalName': '31000',
    'temporary': False,
    'name': '20th & Bell St',
    'latestUpdateTime': '1353454305589'
}
```

Some systems might require an API key to work (for instance, Cyclocity). In
these cases, the instance factory can take an extra API key parameter.

```python
>>> key = "This is not an API key"
>>> velib = pybikes.get('velib', key)
```

Note that pybikes works as an instance factory and, choicely, instances can be
generated by passing the right arguments to the desired class

```python
>>> from pybikes.cyclocity import BixiSystem
>>> capital_bikeshare = BixiSystem(
        tag = 'foo_tag',
        root_url = 'http://capitalbikeshare.com/data/stations/',
        meta = {'foo':'bar'}
    )
```

The way information is retrieved can be tweaked using the PyBikesScraper class
included on the utils module thus allowing session reusing and niceties such as
using a proxy. This class uses [Requests] [2] module internally.

```python
>>> scraper = pybikes.utils.PyBikesScraper()
>>> scraper.enableProxy()
>>> scraper.setProxies({
        "http" : "127.0.0.1:8118",
        "https": "127.0.0.1:8118"
    })
>>> scraper.setUserAgent("Walrus™ v3.0")
>>> scraper.headers['Foo'] = 'bar'
>>> capital_bikeshare.update(scraper)
```

[1]: http://www.citybik.es              "CityBikes"
[2]: http://docs.python-requests.org    "Requests"
