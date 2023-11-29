# Monstache

Connector to synchronize MongoDB and ElasticSearch (OpenSearch)

## Steps to set up

1. Download binary file from official repository [Releases · rwynn/monstache ·  GitHub](https://github.com/rwynn/monstache/releases)

    ```bash
    curl -O <https://github.com/rwynn/monstache/releases/download/v6.7.7/monstache-2d437b2.zip>
    unzip monstache-2d437b2.zip
    ```

1. Move bin file to you folder (or to Linux bin folder)

    ```bash
    mv build/linux-amd64/monstache /usr/local/bin/
    ```

1. Configure MongoDB to use opLog [MongoDB Cluster (Replica Set) - Tera-fic Team - Confluence (atlassian.net)](https://hellotera.atlassian.net/wiki/spaces/TT/pages/71139332/MongoDB+Cluster+Replica+Set)
1. Create config file monstache.toml:

    ```bash
    touch monstache.toml
    monstache.toml example:
    mongo-url = "mongodb://<mdb_user>:<mdb_password>@<ip>:27017/<database>"

    elasticsearch-urls = ["https://<url>:443"]
    elasticsearch-max-conns = 4
    elasticsearch-user = "<es_user>"
    elasticsearch-password = "<es_password>"

    namespace-regex = '^<mongo_db>\.<collection>$'
    direct-read-namespaces = ["<mongo_db>.<collection>"]

    replay = true
    resume = true
    resume-name = "monstache-resume"
    resume-write-unsafe = false
    resume-strategy = 0
    gzip = true
    index-stats = true
    dropped-collections = true
    dropped-databases = true
    index-as-update = true
    verbose = false
    exit-after-direct-reads = false

    [log-rotate]
    max-age = 30

    [[mapping]]
    namespace = "<mongo_db>.<collection>"
    index = "<index_name>"

    # The script allows to choose only necessary fields
    [[script]]
    # this script does not declare a namespace
    # it is global to all collections

    script = """
    module.exports = function(doc, ns) {
        // the doc namespace e.g. test.test is passed as the 2nd arg
        return _.pick(doc, "name", "description", "reviews");
    }
    """
    ```

1. Start monstache:

    ```bash
    monstache -f <path-to-file>/monstache.toml
    ```

### Articles

- [Getting Started - Monstache (rwynn.github.io)](https://rwynn.github.io/monstache-site/start/)
- [Configuration - Monstache (rwynn.github.io)](https://rwynn.github.io/monstache-site/config/)
- [GitHub - rwynn/monstache: a go daemon that syncs MongoDB to Elasticsearch in realtime. you know, for search.](https://github.com/rwynn/monstache)
- [How to sync some specific fields of documents from mongo collection to elasticsearch · Issue #149 · rwynn/monstache · GitHub](https://github.com/rwynn/monstache/issues/149)
