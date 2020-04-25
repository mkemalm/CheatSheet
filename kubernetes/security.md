## API Access

#### Authentication

* Authentication are used to include client certificates, password or tokens
    * Human accounts typically use client certificates
    * Service accounts typicall use tokens
* Request that can't authenticated are rejected with HTTP 401
* k8s doesn't have user objects. A user is a just a set of client certificates.

#### Authorization

* After authentication user must be authorization
* k8s is doing this with Authorization Modes
