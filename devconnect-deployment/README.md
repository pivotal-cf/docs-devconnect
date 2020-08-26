# A Guide to deploying the DevConnect BOSH release

## Dependencies
The DevConnect bosh release requires CF to be installed. DevConnect leverages Gorouter and the networking rules provided by CF.

The DevConnect bosh release has dependence on the following releases (which are downloaded from bosh.io if not already available):
-  postgres
-  uaa
-  bpm
-  routing

## Sample Deployment Mainfest
A sample deployment manifest is available in `manifest/sample-manifest.yml`. It is broken up into three parts and has a dependency on
[YTT](https://github.com/k14s/ytt) (YAML templating tool)

- Configurations for DevConnect `manifest/instance_groups/devconnect-server.lib.yml`
- Configurations for UAA `manifest/instance_groups/uaa.lib.yml`
- Configurations for a postgres Database for DevConnect `manifest/instance_groups/postgres-for-devconnect.lib.yml`

To build the deployment manifest, install [YTT](https://github.com/k14s/ytt) and execute the following:

`ytt -f manifest/ > MANIFEST-FILENAME`

### Bosh Deployment Variables
The following variables will need to be provided to perform a bosh deployment.
A sample bosh variables file is available in `manifest/sample-vars-file.yml`.

* **devconnect-auth-client-id**
  * A UAA authentication client with id `devconnect-auth-client-id` is automatically created. This client is used by DevConnect to authenticate users with its UAA.
* **devconnect-auth-client-secret**
  * A UAA authentication client with secret `devconnect-auth-client-secret` is automatically created. This client is used by DevConnect to authenticate users with its UAA.
* **devconnect-auth-base-url**
  * The URL of the auth server. It should be a subdomain of your foundation app domain i.e. DEVCONNECT-AUTH-SUBDOMAIN.apps.FOUNDATION-FQDN; this allows the deployment to properly register the route with the routing release.
  * for example: https://devconnect-uaa.apps.redlands.cf-app.com
* **devconnect-session-secret**
  * Arbitrary string used to encrypt session cookies
* **devconnect-server-domain**
  * The devconnect server domain, `devconnect.<foundation-apps-domain>`
* **devconnect-server-route-port**
  * The DevConnect server route port. This is the port for the external application route as found in CF Gorouter. Defaults to 443.
* **devconnect-server-app-port**
  * The DevConnect server application port. This is the application port on the Diego cell. Defaults to 8888.
* **devconnect-server-scheme**: https
  * The DevConnect server scheme (http or https). Recommended to be `https`
* **devconnect-entitlements-custom-template-variables**
  * The list of custom variables used to construct project names.
 Example:
```
[
  {
    "variableName": "CostCenter",
    "displayName": "Cost Center",
    "displayText": "$$$ Please enter your cost center $$$",
    "characterLimit": 10
  }
]
```
* **devconnect-entitlements-foundations**
  * see Tanzu Developer Connect docs section "Configuring Entitlements for Developer Connect" for examples
* **uaa-admin-client-secret**
  * The admin client secret for the deployed uaa.
* uaa-vm-type
  * The VM size for the deployed uaa. VM sizes can be found in the cloud config of the bosh director.
* **postgres-vm-type**
  * The VM size of the deployed postgres database for devconnect. VM sizes can be found in the cloud config of the bosh director.
* **devconnect-vm-type**
  * The VM size of the deployed devconnect server. VM sizes can be found in the cloud config of the bosh director.
* **postgres-disk-type**:
  * The persistent disk for the deployed postgres database for devconnect. Disk sizes can be found in the cloud config of the bosh director.
* **uaa-disk-type**
  * The persistent disk for the deployed uaa (it brings in its own postgres database). Disk sizes can be found in the cloud config of the bosh director.
* **devconnect-vm-extension**:
  * The VM extension that will allow devconnect to make outbound calls. VM extensions can be found in the cloud config.

 ```
 - cloud_properties:
     ephemeral_external_ip: true
     tags:
       - pcf-redlands
   name: vm-extension-devconnect
 ```

* **network**
  * The network used for the bosh deployment. Networks can be found in the cloud config of the bosh director.
* **az**
  * The availability zone used for the bosh deployment. Availability zones can be found in the cloud config of the bosh director.
* **cf-deployment**
  * The name of the cf bosh deployment.
* **foundation-apps-domain**
  * The devconnect bosh release's foundation app subdomain where uaa and devconnect endpoints will be accessible.
  * For example: apps.redlands.cf-app.com
* **devconnect-_server-_tls-_cert**
  * (Optional) TLS certificate if setting up TLS for DevConnect. Requires a user to add the root CA cert to credhub
* **devconnect_server_tls_private_key**
  * (Optional) TLS private key if setting up TLS for DevConnect. Requires a user to add the root CA cert to credhub

## Configuring an IDP with UAA
DevConnect's UAA can be configured with your own IPD. A sample IDP config for UAA is provided in `manifest/instance_groups/uaa.lib.yml`. Add and configure the `login.saml.providers` section.

## How to Deploy
Target your bosh director and execute the following:

```
bosh -d devconnect deploy -l manifest/sample-vars-file.yml <(ytt -f manifest/)
```
