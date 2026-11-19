Kubernetes
==========

For running in a Kubernetes cluster, the *gView.Server* image is available
(``ghcr.io/jugstalt/gview-server``). *gView.WebApps* is intentionally not covered here — a
Kubernetes deployment is typically not the usual way to run a web application.

.. note::

    As described in :doc:`run-docker`, the images have recently moved from Docker Hub
    (``docker.io/gstalt/...``) to the GitHub Container Registry under
    ``ghcr.io/jugstalt/...``.

A simple Deployment with an accompanying Service can look like this:

.. code-block:: yaml

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: gview-server
      namespace: gview
      labels:
        app: gview-server
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: gview-server
      strategy:
        type: RollingUpdate
        rollingUpdate:
          maxSurge: 50%
          maxUnavailable: 50%
      template:
        metadata:
          labels:
            app: gview-server
        spec:
          containers:
          - name: gview-server
            image: ghcr.io/jugstalt/gview-server:latest
            volumeMounts:
            - name: gview-storage
              mountPath: /etc/storage
              readOnly: false
            ports:
            - name: http
              containerPort: 8080
            env:
            - name: GV_REPOSITORY_PATH
              value: /etc/storage/gview
            - name: GV_ONLINERESOURCE_URL
              value: https://gview.example.com
          #
          # use persistent storage (recommended)
          #
          volumes:
          - name: gview-storage
            persistentVolumeClaim:
              claimName: gview-premium-storage
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: gview-server-svc
      namespace: gview
    spec:
      ports:
      - port: 80
        targetPort: 8080
        protocol: TCP
        name: http
      selector:
        app: gview-server

.. note::

    The container listens internally on port ``8080`` (not ``80``) — ``containerPort`` and
    ``targetPort`` must be set accordingly.

The two environment variables ``GV_REPOSITORY_PATH`` and ``GV_ONLINERESOURCE_URL`` are the same
ones used with a plain ``docker run`` (see :doc:`run-docker`):

* ``GV_REPOSITORY_PATH`` should point to a directory inside a mounted volume, so that services,
  client data and output files survive a pod restart.
* ``GV_ONLINERESOURCE_URL`` must point to the externally reachable URL of the service (e.g. the
  URL of an upstream Ingress).

.. note::

    It is strongly recommended to use *persistent* storage (``PersistentVolumeClaim``) for
    ``GV_REPOSITORY_PATH``. Without persistent storage, all published services and settings are
    lost on every pod restart.

Depending on the cluster environment, an Ingress (or an equivalent LoadBalancer service) is
additionally required to make the service reachable from the outside under the URL given in
``GV_ONLINERESOURCE_URL``.
