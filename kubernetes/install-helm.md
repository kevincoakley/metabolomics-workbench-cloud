# Install Helm

Download and install the Helm client to your workstation from https://helm.sh

Once you have the Helm client installed on your workstation, run the following 3 commands to install Helm:

    $ kubectl --namespace kube-system create serviceaccount tiller
    
    serviceaccount "tiller" created
    
    $ kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
    
    clusterrolebinding.rbac.authorization.k8s.io "tiller" created
    
    $ helm init --service-account tiller --upgrade
    $HELM_HOME has been configured at /Users/xxx/.helm.

    Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

    Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
    To prevent this, run `helm init` with the --tiller-tls-verify flag.
    For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
    Happy Helming!

You can use the three commands to verify that Helm was installed and is running:

    $ kubectl -n kube-system get deployment | grep tiller
    tiller-deploy   1         1         1            1           8m

    $ kubectl -n kube-system get po | grep tiller
    tiller-deploy-7b65c7bff9-482gq   1/1       Running   0          1m

    $ helm version
    Client: &version.Version{SemVer:"v2.13.1", GitCommit:"618447cbf203d147601b4b9bd7f8c37a5d39fbb4", GitTreeState:"clean"}
    Server: &version.Version{SemVer:"v2.13.1", GitCommit:"618447cbf203d147601b4b9bd7f8c37a5d39fbb4", GitTreeState:"clean"}
