# Assignment
**Node.js Hello World Application Deployment**

- Docker iamges: alphax123/hello-node:latest
- image url: https://hub.docker.com/r/alphax123/hello-node/tags

- repo:https://github.com/Mondeep1999/Assignment.git

**create 2 repository 1st for node application other for manifest and chat,value file**


**setting up git worklow for the node_image creation.**
- move the hello-node-image(contain code for node application) folder  to one repo
- go to action section in the repo and create a worflow.
- copy the content from the workflow.yml in the repo in to the newly created wokflow.
  configure the secrets :
    - go to setting section of the repo 
    - select the secret and variable option
    - select action and enter the docker login credentials
    - edit the variable set on the secret on the login section in the workflow file.
    
**setting up repo for manifest files for gitOps**
- move the hello-node (contain all the manifest files) folder to another repo
  
  **setting up kubernetes and Argo CD:**
  
**I am using AWS k8 cluster EKS (configuring it using awsCLI)**
  - install kubectl 

    ```sh
    curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin
    kubectl version --short --client
    ```
  - install AWS CLI 

    ```sh
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install
    aws --version
    ```
  - install eksctl

    ```sh
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    sudo mv /tmp/eksctl /usr/local/bin
    eksctl version
    ```
  - Create IAM user with below permissions
    - IAM - fullaccess
    - VPC - fullaccess
    - EC2 - fullaccess
    - CloudFomration - fullaccess
    - Administrator - acces
  - Create EKS Cluster using eksctl 

    ```sh
    eksctl create cluster--name bot-cluster4 --region us-east-1 --node-type t2.medium --zones us-east-1a,us-east-1b
    ```
  - curl the helm script and run the script to install helm

    ```sh
    curl -fsSl -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    helm version
    ```
    
**deploying argoCD on k8 using helm chart**
  - get the argoCD folder from the repo where helm is installed
  - now run commnad to install helm chart for argoCD (it will intall the argoCD in the argoCD namespace)
  - after copying it into the machine where helm is installed this use command to check if there is any error

    ```sh
    helm lint argoCD
    helm template argoCD 
    ```
    - it will show all the manifest file with values 
    - then run this command to validate

    ```sh
    helm install argocd --debug --dry-run argocd
    ```

    ```sh
    helm install deployargoCD argoCD
    ```
  - now since the svc is set to loadbalaner it will give you an external IP through which you can access the argoCD site
  - to login to argoCD we need to generate password
      - install argoCli

        ```sh
        curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
        sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
        rm argocd-linux-amd64

        ```        
      - get the initial login credential

        ```sh
        argocd admin initial-password -n argocd
        ```
  - now log into argoCD go to setting > repositories add your repo url and credencials.
  - now move to application and select new app enter all appropriate details eg. select the source repo, enter the destination cluster, namespace
  - as you will select you helm folder it will automatically fetch all the values
  - then you can create the application
  - it will create the application in the required cluster.
  - now the argoCD will look for any change in manifest file ,if anything get updated it will regenerate a new application.

