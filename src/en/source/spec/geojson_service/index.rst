GeoJson Service Specification
=============================

General Description of the Interface
------------------------------------

The GeoJson Service offers a powerful and flexible API for providing geospatial data in GeoJSON format.  
The goal of the interface is to provide developers with an easy way to query, visualize, and manipulate geospatial data.  
The API adheres to established standards and allows for the consumption and modification of spatial data via GET, POST, PUT, and DELETE requests.

The motivation behind developing this interface was to enable an approach that is both simple and powerful for accessing geospatial data.  
Existing interfaces such as **WMS** are very simple but offer limited possibilities for querying geospatial data.  
**WFS** provides querying capabilities, but the standard is based on XML, which can be cumbersome to implement in web environments using JavaScript.  
ESRI’s **GeoServices** interface is a REST interface that also allows easy access to geospatial data.  
However, its syntax for *features* is somewhat proprietary.

**GeoJson Services** aims to provide a middle ground between existing interfaces, combining the simplicity of **WMS**, the power of **WFS(-T)**, and modern technologies like **ESRI GeoServices**.  
The interface is designed as a **REST** API with *JSON requests and JSON responses*.  
The syntax should resemble **GeoJSON**.  
Particularly, features should be provided and transferred in **GeoJSON** format, as this standard is natively supported by many *web mapping frameworks*.

The interface should provide the following functions:

* **Catalog:** Lists available GeoServices.

* **Capabilities:** Displays the capabilities of a GeoService.

* **Mapping:** Generates map representations for a specified area and customizable layer visibility within the service.

* **Query:** Queries geospatial data from the provided services by passing attribute filters and/or spatial filters.

* **Edit:** Creates/Edits/Deletes geospatial data.

* **Security:** GeoServices should be accessible with authentication to grant clients specific permissions (Edit/Query).  
  Requests must include a **Bearer Token** in the **Authorization Header**.

API Link Structure
------------------

The API follows a consistent URL structure, making it easy to retrieve information or make changes.  
The following outlines the various API endpoints along with a brief description of their function.

**Services**

- **GET**
  
  - ``https://{server}/geojsonservice/v1/services``
  
    Returns a list of all available geospatial data services on the server.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}``
    
    Returns all services within the specified folder.

**Capabilities**

- **GET**
  - ``https://{server}/geojsonservice/v1/services/{service}/capabilities``
    
    Returns the properties and information of the specified service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities``
    
    Returns the properties and information of the specified service within a specific folder.

**Map**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/map``
    
    Generates a map based on the specified service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/map``
    
    Generates a map for a service within a specific folder.

**Legend**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/legend``
    
    Returns the legend for the specified service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/legend``
    
    Returns the legend for a service within a specific folder.

**Query**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/query/{layerId}``
    
    Executes a query on the specified layer within the service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/query/{layerId}``
    
    Executes a query on the layer of a service within a specific folder.

**Features**

- **POST/PUT/DELETE**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/features/{layerId}``
    
    Adds, updates, or deletes features in a specific layer of the service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/features/{layerId}``
    
    Adds, updates, or deletes features in a specific layer of a service within a specific folder.

**Token**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/Token``
    
    Returns a token for authentication or performs the authentication process.

.. toctree::
   :maxdepth: 1
   :caption: Table of contents:

   types
   info
   services
   capabilities
   map
   legend
   query
   features
   token
   errorhandling