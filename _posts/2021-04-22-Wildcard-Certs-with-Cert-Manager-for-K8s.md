---
layout: post
title: Generate and use Wild card certs for K8s with Cert-Manager and Istio
date: 2021-04-22 20:19
category: k8s
author: 
tags: 
 - k8s
 - Cert-Manager
 - wildcard Cert
 - Istio
 - Istio Gateway
summary: Wild Card Certificates with Cert-Manager on K8s and Istio Gateway
---
I love the simplicity that [Cert-Manager](https://cert-manager.io/docs/) brings to the certificate management for k8s. 
- Install Cert-Manager
- Create a Issuer ( ClusterIssuer Resource)
- Create any Certificate using the Issuer. 

Just Following the above steps, gets cert-manager to do all the negotiations on your behalf with letEncrypt API and creates a valid certificate which can then be used by the IngressGateways. 

The need for me was to extend this ease of use to wildcard certs as well.  I saw this Amazing Blog which when followed verbatim ,  got me up and running in just a few mins. I wanted to quickly put my own example using those instructions.  

I had a need to create wild card certs to be used for the apps and sys domain for Tanzu Application Service for K8s. I went with cert-manager instead of using the certbot/acme cli. 

The Key Components of this model are: 
- The Cert-Manager and all its resources 
- The Certificate Issuer ( Cluster Issuer)
- The Certificate resource
- The Secret where the Certificate will be stored


On can install cert-manager on a K8s cluster in one of many ways as described on the docs. I have ued the helm chart approach to install it. 

```bash
$ helm repo add jetstack https://charts.jetstack.io
# Helm v3+
$ helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.1.0 \
  --set installCRDs=true
```

After the Cert-Manager is installed,  we need to create a Certificate Issuer Resource. The Example below uses DNS01 Challenge method to get Letsencrypt to verify you and provide the certificate. 
As you can see , you can create multiple issuers based on the DNS provider or the Challenge type you use. I have seen folks create separate issuers for different purposes as well. 

#### **`cluster-issuer.yml`**
```yml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
  namespace: ${namespace}
spec:
  acme:
    email: ${acme_email}
    privateKeySecretRef:
      name: acme-account-key
    server: https://acme-v02.api.letsencrypt.org/directory
    solvers:
    - dns01:
        route53:
          region: ${aws_region}
          accessKeyID: ${aws_access_key_id}
          secretAccessKeySecretRef:
            name: ${secret_key_name}
            key: aws_secret
          hostedZoneID: ${hosted_zone_id}

```

Now that yaml file, let us create the issuer resource
```bash
$ kapp deploy -a issuer -f cluster-issuer.yml
```

Lets prepare a `yaml` defining the certificate. Here we create a k8s resource called `Certificate`. The other key information we need to provide here are the Common Name and the DNS name that we are creating this certificate for. The Main difference when it comes to regular single DNS name cert vs Wild card is the fact that we prepend an `*.` to the domain name and the fact that we provide the base and the wild card on the `dnsNames`. You also see in the example that we are using the issuer we created in the prior step and providing the name of the secret we want the issuer to store the certificate in. 
#### **`certificates.yml`**
```yml
---
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: training.eduk8s.io-tls
  namespace: eduk8s
spec:
  commonName: '*.training.e2e.aws.clue2solve.com'
  dnsNames:
  - training.e2e.aws.clue2solve.com
  - "*.training.e2e.aws.clue2solve.com"
  issuerRef:
    kind: ClusterIssuer
    name: letsencrypt-prod
  secretName: training.eduk8s.io-tls
```

Its now time to create the certificate and see the magic happen. 
```bash
$ kapp deploy -a certs -f certificates.yml
```

You can now provide the secret as the certificate store to any subdomain of the dnsName(s) that are included in the DNS Names list above. 

Good luck securing  your apps ! 


