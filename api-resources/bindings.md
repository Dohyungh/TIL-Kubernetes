## bindings

```html
KIND: Binding VERSION: v1 DESCRIPTION: Binding ties one object to another; for
example, a pod is bound to a node by a scheduler. Deprecated in 1.7, please use
the bindings subresource of pods instead. FIELDS: apiVersion
<string>
  APIVersion defines the versioned schema of this representation of an object.
  Servers should convert recognized schemas to the latest internal value, and
  may reject unrecognized values. More info:
  https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources
  kind
  <string>
    Kind is a string value representing the REST resource this object
    represents. Servers may infer this from the endpoint the client submits
    requests to. Cannot be updated. In CamelCase. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds
    metadata
    <ObjectMeta>
      Standard object's metadata. More info:
      https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
      target
      <ObjectReference>
        -required- The target object that you want to bind to the standard
        object.</ObjectReference
      ></ObjectMeta
    ></string
  ></string
>
```

오브젝트들끼리 연결해주는 역할을 한다. 대표적인 예로, 파드를 노드에 바인딩한다. 이는 스케쥴러에 의해 자동으로 실행되기 때문에 사용자는 이 자원을 직접 사용하는 경우가 드물다.
