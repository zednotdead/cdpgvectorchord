# CrunchyData PGO with VectorChord Extension

This repository contains container images for [CrunchyData PGO](https://github.com/CrunchyData/postgres-operator) that include the [VectorChord](https://github.com/tensorchord/VectorChord) extension.

## Important Configuration Note

> :warning: **If you are deploying this image on an existing database:** The postgres configuration must be updated to enable the vchord.so extension. 

To enable the extension, you need to set the `shared_preload_libraries` and `search_path` in your Cluster spec. Add the following configuration to your `PostgresCluster` yaml file:

```yaml
apiVersion: postgres-operator.crunchydata.com/v1beta1
kind: PostgresCluster
spec:
  ...
  patroni:
    dynamicConfiguration:
      postgresql:
        synchronous_commit: "on"
        parameters:
          shared_preload_libraries: "vchord.so"
          search_path: '"$user", public, vectors'
```

> :warning: If you want to do a major Postgres version upgrade like described in the [official docs](https://access.crunchydata.com/documentation/postgres-operator/latest/guides/major-postgres-version-upgrade), make sure you do the following steps or the upgrade will fail:

1. Connect to the database where you have enabled the `vchord` extension and disable it with `DROP EXTENSION vchord;`.
2. Remove the `dynamicConfiguration` block you added to `PostgresCluster` from above.
3. Follow the upgrade instruction from the official doc.
4. Add back the `dynamicConfiguration` block and enable the extension in the database again with `CREATE EXTENSION vchord CASCADE;`
