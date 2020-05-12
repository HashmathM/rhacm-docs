# Troubleshooting a default storage class error when creating a cluster on bare metal

If you receive a storage class error when creating a cluster on bare metal, it is likely because the default storage class is not set. 

## Symptom

When you create a cluster in a bare metal environment using the default setting for the storage class, the installation fails with the following error:

```
"error":"failed to find default storageclass",
```

## Resolving the problem

Verify that your `StorageClass` setting is set to the following value:

```
  metadata:
    annotations:
      storageclass.kubernetes.io/is-default-class: "true"
```
