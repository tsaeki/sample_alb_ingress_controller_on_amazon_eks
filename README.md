# Sample ALB Ingress Controller on Amazon EKS
Setup the alb ingress controller on Amazon EKS with Fargete.  
I change the procefure https://docs.aws.amazon.com/en_pv/eks/latest/userguide/alb-ingress.html a litte.

# Sample Run 
## 1. Create Kubernetes Cluster
```
$ eksctl create cluster --name demo-newsblog --region ap-northeast-1 --fargate
```
## 2. Setup ALB Ingress Controller 
- Set up No.1 - No. 5 this site(https://docs.aws.amazon.com/en_pv/eks/latest/userguide/alb-ingress.html)

## 3. Update alb-ingress-controller.yaml file and deploy
- The alb-ingress-controller.yaml file is base on https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/alb-ingress-controller.yaml
```
$ vim alb-ingress-controller.yaml
    spec:
      containers:
        - name: alb-ingress-controller
          args:
            - --ingress-class=alb
            - --cluster-name=demo-newsblog
            - --aws-vpc-id=vpc-xxxxxxxxxxxxxxxx # your VPC ID
            - --aws-region=ap-northeast-1 # your region
            - --aws-api-debug=true
            - --feature-gates=waf=false
          env:
            - name: AWS_ACCESS_KEY_ID
              value: <aws access key>
            - name: AWS_SECRET_ACCESS_KEY
              value: <aws secret key>


$ kubectl apply -f alb-ingress-controller.yaml
```

## 4. Deploy a sample application
```
$ kubectl apply -f sample-deployment.yaml
$ kubectl apply -f sample-service.yaml
$ kubectl apply -f sample-ingress.yaml

# check
$ kubectl get ingress/sample-ingress
NAME             HOSTS   ADDRESS                                                                       PORTS   AGE
sample-ingress   *       ec36f496-default-sampleing-de8b-xxxxxxxx.ap-northeast-1.elb.amazonaws.com   80      52m

```

## 5. Access 
- open `http://ec36f496-default-sampleing-de8b-xxxxxxxx.ap-northeast-1.elb.amazonaws.com`
- You will show nginx default page.

# Reference
- https://aws.amazon.com/blogs/aws/amazon-eks-on-aws-fargate-now-generally-available/
- https://docs.aws.amazon.com/en_pv/eks/latest/userguide/alb-ingress.html
- https://839.hateblo.jp/entry/2019/12/08/172020
