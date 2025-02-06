# spicedb_rbac_example
experimenting with k8s rbac modeled in spicedb


start local spicedb
```
docker run --rm -p 50051:50051 authzed/spicedb serve --grpc-preshared-key "key"
```

register zed context
```
zed context set dev localhost:50051 "key" --insecure
```

import schema using zed cli
```
zed import rbac_relation.yaml
```

test permission checks

user1 has admin binding
user2 has edit binding
user3 has view binding 

Retrieve all namespaces that `user3` can view
```
$ zed permission lookup-resources namespace list user:user3
namespace1
namespace2
namespace3
```

Retrieve all namespaces that `user3` can modify (no results because only viewer)
```
$ zed permission lookup-resources namespace update user:user3
$ zed permission lookup-resources namespace patch user:user3
```

Retrieve all namespaces that `user2` can edit
```
$ zed permission lookup-resources namespace list user:user2
namespace1
namespace2
namespace3
```

Retrive all namespaces that `user2` can batch delete in (should be none since user2 is edit only)
```
zed permission lookup-resources namespace deleteall user:user2
```

Retrive all namespaces that `user1` can execute delete all in (user1 is admin)
```
$ zed permission lookup-resources namespace deleteall user:user1
namespace1
namespace2
namespace3
namespace4
```


