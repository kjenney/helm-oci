# helm-oci

Packaging helm charts to OCI

https://docs.aws.amazon.com/AmazonECR/latest/userguide/push-oci-artifact.html


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
