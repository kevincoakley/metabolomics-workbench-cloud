# Configure kubectl

Once the EKS cluster's status is *Active* configure kubectl on your local workstation:

    $ aws eks --region us-west-2 update-kubeconfig --name workbench-prod
