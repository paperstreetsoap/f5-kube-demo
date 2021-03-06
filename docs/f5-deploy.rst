Deploy the F5 / Kubernetes Container Connector
=================================================
All of the following commands and yaml files are contained within the "f5-kube-demo" github repo.
    .. code:: bash

        $ git clone https://github.com/vtog/f5-kube-demo.git

    .. note:: For a more thorough explanation see http://clouddocs.f5.com/containers/v2/kubernetes/index.html

**Basic f5 container connector deployment commands:**

#. Create bigip login secret
    .. code:: bash

        $ kubectl create secret generic bigip-login -n kube-system --from-literal=username=admin --from-literal=password=admin

#. Create kubernetes service account for bigip controller
    .. code:: bash

        $ kubectl create serviceaccount k8s-bigip-ctlr -n kube-system

#. Create cluster role for bigip service account (admin rights, but can be modified for your environment)
    .. code:: bash

        $ kubectl create clusterrolebinding k8s-bigip-ctlr-clusteradmin --clusterrole=cluster-admin --serviceaccount=kube-system:k8s-bigip-ctlr

.. note:: Use one or the other of the following commands depending on deployment type. **nodeport vs. cluster** (see http://clouddocs.f5.com/containers/v2/kubernetes/kctlr-modes.html)

#. **NodePort** example
    .. code:: bash

        $ kubectl create -f  f5-nodeport-deployment.yaml

    .. include:: ../f5-nodeport-deployment.yaml
        :literal:

#. **ClusterIP** example
    .. code:: bash

        $ kubectl create -f f5-cluster-deployment.yaml

    .. include:: ../f5-cluster-deployment.yaml
        :literal:

    .. code:: bash

        $ kubectl create -f f5-bigip-node.yaml

    .. include:: ../f5-bigip-node.yaml
        :literal:

    .. note:: Be sure to find and update the "VtepMaC" with the correct address
        from the f5 CLI

    .. code:: bash

        $ tmsh show net tunnels tunnel fl-vxlan all-properties

#. Verify f5 container connector is up and running
    .. code:: bash

        $ kubectl get pods -n kube-system -o wide
