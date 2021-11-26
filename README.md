# Eidas Keycloak Extension

This repository contains a [keycloak](https://www.keycloak.org/) extension which adds support for the 
SAML v2.0 dialect of the [eIDAS](https://en.wikipedia.org/wiki/EIDAS) nodes.
It provides an identity provider extension which allows keycloak to be setup as an "identity broker". 

[Keycloak](https://www.keycloak.org/about) is an open source Identity and Access Management system for modern 
applications.

[eIDAS-Nodes](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+version+2.5) are operated 
from EU member states according to the eIDAS Regulation in order to ensure that people and businesses can use 
their own national eIDs (electronic identification schemes) to access public services available online in 
other countries.

aThe eIDAS Nodes use an extended version of SAML v2.0 which defines a number of SAML elements and attribute 
definitions which are not supported by default in standard SAML implementations. This extension provides support 
for these extensions, by offering a custom IdP which can use this extended dialect.

# Installation 

Download the latest release jar from the releases page. Then deploy it in keycloak by copying it at location 
`KEYCLOAK_HOME/standalone/deployments/` folder. See the keycloak [documentation](https://www.keycloak.org/docs/latest/server_installation/index.html#distribution-directory-structure) for the directory structure of the keycloak server.

# Providers 

The extension provides the following components which are needed in order to connect to an eIDAS node using 
the extended definitions of the eIDAS technical specifications: 

  * Identity provider "eIDAS SAML v2.0" which is an extended version of the default "SAML v2.0" IdP.
  * Mapper "Attribute Importer" which can be used import additional attributes 
  * Mapper "Username Template Importer" which can be used to setup the ID or username for federated user lookup.
  * Authenticator "Citizen Country Selection" which can collect the citizen country before authentication. 

# Setup

  * Setup the keycloak realm key provider for signing requests according to the eIDAS specifications.
    Depending on the setup of the eIDAS node that you are trying to connect it might be important that the 
    certificate contains the correct country code.
  * Add the "eIDAS SAML v2.0" identity provider. 
  * Setup the "eIDAS SAML v2.0" identity provider by setting the classic "SAML v2.0" options and the 
    additional eIDAS specific options.
  * Add a "Username Template Importer" with template something like `${ALIAS}.${ATTRIBUTE.PersonIdentifier}` and target `BROKER_ID`. 
    You can also adjust the username in a similar fashion. 
  * Add "Attribute Importer" for the attributes you want to consume, e.g. "DateOfBirth".
  * Go to "Authentication" and copy the "Browser" flow. 
  * After the "Cookie" execution add an "eIDAS" flow which contains the following two executions: 
     - Citizen Country Selection. Use the "Actions" menu to configure this by adjusting the available country codes. These codes 
       are two letter names.
     - Identity Provider Redirector. Use the "Actions" menu to adjust the "Default Identity Provider" to "eidas-saml", in order for
       the redirection to happen automatically.
  * Set the new flow as default in the "Brower Flow" bindings.
  * Adjust depending on your use case the "First Broker Login" and additional properties.

# Build and Install

Build the project using maven

```
mvn package
```

You can find the jar under `target/`.

# License 

Apache License, Version 2.0

