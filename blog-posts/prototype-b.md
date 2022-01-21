- All applications, be they web or mobile, have a “backend” they are communicating with to enable users to do things like authenticate, fetch data, add data, etc. The layer in between the application, aka client, and the backend is an API, short for Application Programming Interface. In most software development teams there are frontend developers building the client, and coding the client to make API calls to the backend. While frontend developers build the client, backend developers are building out the API for the client to be able to access the backend. Frontend developers only need to know the API endpoints their client needs to hit to perform required functions, these endpoints are published as part of the API by the backend engineers. While there are many approaches to API development, we’ve adopted the Google Cloud prescribed methodology, which dictates the use of Swagger OpenAPI Specification Version 2. This Specification is essentially a YAML file describing the API, as you’ll see later, this file will be used by the API Gateway Google Cloud resource to deploy our API and act as the aforementioned layer between the client and backend.

- Due to the importance of APIs we decided to develop this prototype as scaffolding for all future applications. The flexibility of this methodology means that we can use the same scaffolding to build other APIs acting as intermediaries between other client types like IoT and all other backend services, not just web servers running on virtual machines. Having an API implementation of this nature makes it backend agnostic. For example, in this exercise we will create a Node.js web server running as a Docker container in Google Cloud Run responding to client requests, but if we change our mind and decide to use Serverless Cloud Functions as the backend instead of Cloud Run we can, and all we’ll need to do is change the “backend” argument in the spec YAML file.

- The Node.js web server we’ll be deploying is performing some very basic Google Cloud Storage tasks like providing the client abilities to create buckets, delete buckets, delete objects, list buckets, list objects, and change object names. The entry file is `server.js` and it’s leveraging Express.js to create routes for the client to hit. There are modules corresponding to each route to do things like fetch buckets, delete buckets, etc. When the time comes to provision our entire API infrastructure, we will first create this docker image and deploy it to Google Cloud for Cloud Run to later use in provisioning itself. For now it’s important to understand what this image is doing, and that’s returning Google Cloud Storage data to the client by leveraging the JavaScript Google Cloud Storage client. However, it’s important to discuss, albeit in short, the security implications of this web server. As you’ll see in the infrastructure provisioning section, this Cloud Run service running the Node.js Express web server will have limited permissions, permissions only to do the storage operations it needs to do, nothing more.

- In deploying this API we will be taking a two-step approach to infrastructure deployment, and that is due to the way Cloud Run services work. Since Cloud Run needs to know what container image to use we need to first build and push the image to Google Cloud Artifact Registry. Once the image is deployed we can proceed and build out all the other infrastructure pieces listed below.

- To test that our API is working as expected we’ll take the API Gateway default hostname returned by terraform and append the routes described in the open api spec file, inserting parameters wherever necessary.