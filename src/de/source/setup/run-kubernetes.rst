Kubernetes
==========

Für den Betrieb in einem Kubernetes-Cluster steht das *gView.Server* Image zur Verfügung
(``ghcr.io/jugstalt/gview-server``). *gView.WebApps* wird hier bewusst nicht behandelt — für
Web-Anwendungen ist ein Kubernetes-Deployment in der Regel nicht der typische Betriebsfall.

.. note::

    Wie unter :doc:`run-docker` beschrieben, werden die Images seit Kurzem nicht mehr auf
    Docker Hub (``docker.io/gstalt/...``), sondern auf der GitHub Container Registry unter
    ``ghcr.io/jugstalt/...`` veröffentlicht.

Ein einfaches Deployment mit zugehörigem Service kann etwa so aussehen:

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
          # Persistenten Storage verwenden (empfohlen)
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

    Der Container lauscht intern auf Port ``8080`` (nicht ``80``) — ``containerPort`` und
    ``targetPort`` müssen entsprechend gesetzt werden.

Die beiden Umgebungsvariablen ``GV_REPOSITORY_PATH`` und ``GV_ONLINERESOURCE_URL`` entsprechen
denen, die auch beim einfachen ``docker run`` verwendet werden (siehe :doc:`run-docker`):

* ``GV_REPOSITORY_PATH`` sollte auf ein Verzeichnis innerhalb eines gemounteten Volumes zeigen,
  damit Services, Client-Daten und Output-Dateien einen Pod-Neustart überstehen.
* ``GV_ONLINERESOURCE_URL`` muss auf die von außen erreichbare URL des Dienstes zeigen (z. B. die
  URL eines vorgeschalteten Ingress).

.. note::

    Es wird dringend empfohlen, für ``GV_REPOSITORY_PATH`` einen *persistenten* Storage
    (``PersistentVolumeClaim``) zu verwenden. Ohne persistenten Storage gehen alle publizierten
    Dienste und Einstellungen bei jedem Neustart des Pods verloren.

Ein Ingress (bzw. ein entsprechender LoadBalancer-Service) wird je nach Cluster-Umgebung
zusätzlich benötigt, um den Service unter der in ``GV_ONLINERESOURCE_URL`` angegebenen URL von
außen erreichbar zu machen.
