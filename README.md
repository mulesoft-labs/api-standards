The set of RAML fragments that are included in this library are meant to be followed by API designers at Mulesoft in order to standardize the APIs by reusing common patterns. This RAML Fragment is a library which means it has to be imported in each RAML spec by including the following snippet:

```
uses:
  std: exchange_modules/68ef9520-24e9-4cf2-b2f5-620025690913.engineering.anypoint-platform.management-center/amc-standards/0.0.11/standards.raml

```

## **Data Types**

### *Error Message Data type*

All error messages in Management Center _MUST_ be of the Error Message data type.

### *UUID*

UUID string pattern


## **Security Schemes**

### *X-Token Security Scheme*

When using Authorization Server (Core Services) to authenticate and authorize the API _MUST_ be secured by this security scheme.

## **Resource Types**

### *Collection*

Standard way of managing collections of eleents within the Management Center APIs. It standardizes the following pattern:

- GET /{resources} ## Get a list of all the existing resources
- POST /{resources} ## Create a new resource


Both verbs are [optional](https://github.com/raml-org/raml-spec/blob/master/versions/raml-10/raml-10.md#declaring-http-methods-as-optional) for the `resource` type, and can be used by declaring them in the API spec. Either `async-create` or `sync-create` traits _MUST_ be defined when using `POST`.


**Usage**

The `Collection` resource type requires the following parameters:

 - collectionExamples: A map of examples to exemplify the response of a list of items.
 - itemType: The Data Type of the items in the collection.
 - itemCreateRequestType: The type of the request when creating a new item of the collection.
 - itemCreateRequestExamples: Request examples to create a new item of the collection.


**Example**

```
/deployments:
  type:
     std.collection:
     collectionExamples:
       single-item: !include examples/created-deployments.raml
     itemType: types.Deployment
     itemCreateRequestType : types.DeploymentRequest
     itemCreateRequestExamples:
       mc-deployment-request: !include examples/deployment-request.raml
  get:
  post:
   is: [  
     std.sync-create: {
       itemCreateResponseType: types.DeploymentGenericResponse,
       itemCreateResponseExamples: {
         mc-deployment-response: !include examples/created-deployment.raml
       }
     }]
```

### *Collection Item*

Standard way of operating on resources within a collection. The verbs are all [optional](https://github.com/raml-org/raml-spec/blob/master/versions/raml-10/raml-10.md#declaring-http-methods-as-optional).

**Usage**

The Collection Item Resource Type requires the following parameters:

- itemType: The item data type returned when doing a `GET`
- itemExamples: Examples of the action of getting an item
- itemPutType: The Data Type used to modify an item
- itemPutExamples: Request examples of `PUT`
- itemPatchType: The Data Type used to modify an item
- itemPatchExamples: Request examples of `PATCH`

**Example**

```
/deployments/{deploymentId}:
  type:
     std.collection-item:
     itemExamples:
       single-item: !include examples/created-deployments.raml
     itemPutType: types.DeploymentRequest
     itemPutExamples:
       mc-deployment-request: !include examples/deployment-request.raml
  put:
   is: [  
     std.sync-update: {
       updateItemResponseType: types.DeploymentGenericResponse,
       updateItemResponseExamples: {
         mc-deployment-response: !include examples/created-deployment.raml
       }
     }]

```

## **Traits**

### *Sync Create*

Trait to be used when creating resources synchronously. It provides the standard response on `POST` requests.

**Usage**

This trait requires the following parameters:

- itemCreateResponseType: The Data Type of the response body
- itemCreateResponseExamples: Examples of the response.

**Example**

```
post:
  is: [  
    std.sync-create: {
      itemCreateResponseType: types.DeploymentGenericResponse,
      itemCreateResponseExamples: {
        mc-deployment-response: !include examples/created-deployment.raml
      }
  }]

```

### *Async Create*

Used to define the response when creating resources asynchronously.

**Example**

```
post:
  is: [ std.async-create ]
```

### *Sync Update*

Trait to be used when updating resources.


**Usage**

This trait requires the following parameters:

- updateItemResponseType: The Data Type of the response body
- updateItemResponseExamples: Examples of the response.

**Example**

```
put:
  is: [  
    std.sync-update: {
      updateItemResponseType: types.DeploymentGenericResponse,
      updateItemResponseExamples: {
        mc-deployment-response: !include examples/created-deployment.raml
      }
  }]

```

### *Cursor Paginated*

Trait to be used when doing cursor pagination.

**Example**

```
/deployments:
  type:
     std.collection:
     collectionExamples:
       single-item: !include examples/created-deployments.raml
     itemType: types.Deployment
     itemCreateRequestType : types.DeploymentRequest
     itemCreateRequestExamples:
       mc-deployment-request: !include examples/deployment-request.raml
   is: [cursor-paginated]
```


### *Offset Paginated*

Trait to be used when doing Offset pagination.

**Example**

```
/deployments:
  type:
     std.collection:
     collectionExamples:
       single-item: !include examples/created-deployments.raml
     itemType: types.Deployment
     itemCreateRequestType : types.DeploymentRequest
     itemCreateRequestExamples:
       mc-deployment-request: !include examples/deployment-request.raml
   is: [offset-paginated]
```

### *Common Errors*

Trait for common error codes like _too many requests_.
