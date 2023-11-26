# helm-oci

Examples of how to store Helm Charts in OCI-compatible container registries

## Package a self-made chart from local to ECR - Example

Taken from [here](https://docs.aws.amazon.com/AmazonECR/latest/userguide/push-oci-artifact.html)

Create the `helm-test-chart` ECR Repository first, then run:

```
helm create helm-test-chart
cd helm-test-chart/
rm -rf templates/*
cd templates/
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: helm-test-chart-configmap
data:
  myvalue: "Hello World"
EOF
cd ../..
helm package helm-test-chart
ECR_ACCOUNT="FILL_IN_ACCOUNT_ID_HERE"
ECR_REGION="FILL_IN_REGION_HERE"
aws ecr get-login-password --region $ECR_REGION | helm registry login --username AWS --password-stdin $ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com
helm push helmtest-0.1.0.tgz oci://ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com
helm install test oci://$ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com/helm-test-chart --version 0.1.0
```

## Package an open-source chart from repo to ECR - Example

Create the `wordpress` ECR Repository first, then run:

```
helm pull bitnami/wordpress
ECR_ACCOUNT="FILL_IN_ACCOUNT_ID_HERE"
ECR_REGION="FILL_IN_REGION_HERE"
aws ecr get-login-password --region $ECR_REGION | helm registry login --username AWS --password-stdin $ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com
helm push wordpress-13.0.19.tgz oci://ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com
helm install wordpress oci://$ECR_ACCOUNT.dkr.ecr.$ECR_REGION.amazonaws.com/wordpress --version 13.0.19
```
