# Solr

**Add authentification to config**
Uncomment and add you credential to file /opt/bitnami/solr/bin/solr.in.sh - for linux

```bash
SOLR_AUTH_TYPE="basic"
SOLR_AUTHENTICATION_OPTS="-Dbasicauth=admin:password"
```

**Instruction to create Solr core (new_core):**

```bash
#Create core folder
sudo mkdir /bitnami/solr/server/solr/new_core
#Copy default configuration sample
sudo cp -r /bitnami/solr/server/solr/configsets/_default/conf/* /bitnami/solr/server/solr/new_core/
#Change rights
sudo chown -R solr:solr /bitnami/solr/server/solr/new_core
#Create core "new_core" in terminal
sudo -u solr ./bin/solr create -c new_core
```
