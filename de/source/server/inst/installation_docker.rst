Docker Container (Linux)
========================

Im folgenden Abschnitt werden fundierte Kenntnisse von Docker vorausgesetzt.

gView MapServer Image verwenden
-------------------------------

Unter https://hub.docker.com/r/gstalt/gview5-server wird ein aktuelles Image für den *gView MapServer* angeboten.
In diesem sind bereits alle notwendigen Abhängigkeiten enthalten. Über Umgebungsvariablen kann der Speicherort 
für die Konfiguration (``GV_REPOSITORY_PATH``) und die Url mit der *gView MapServer* nach außen sichtbar ist (``GV_ONLINERESOURCE_URL``)
gesteuert werden.

Benutzerdefinierte Container erstellen
--------------------------------------

Reicht die Standardkonfiguration nicht aus kann aus der bestehenden Inatallation eine Image erzeugt werden.

Als erstes muss ein Dockerfile erstellt werden. Hier ein Beispiel, in dem auch gezeigt wird,
wie die notwendigen Libraries *GDI+* und *proj4* installiert werden.

.. code::

   FROM mcr.microsoft.com/dotnet/core/aspnet:3.1.17-buster-slim

   #
   # Install GDI +
   #
   RUN apt-get update
   RUN apt-get install -y libgdiplus lsof
   RUN apt-get install -y libc6-dev
   RUN ln -s /user/lib/libgdiplus.so.0.0.0 /usr/lib/gdiplus.dll

   #
   # Install Proj4 Lib
   #
   RUN apt-get install -y proj-bin
   RUN ln -s /usr/lib/x86_64-linux-gnu/libproj.so.13 /usr/lib/proj.dll

   #
   # Copy app (optional: copy from override for example _config...)
   #
   WORKDIR /app
   EXPOSE 80
   COPY /app .
   COPY /override .

   #
   # Install Fonts Example (optional)
   #
   run mkdir ~/.fonts
   COPY /fonts /fonts
   RUN cp /fonts/* ~/.fonts
   RUN apt-get install fontconfig
   RUN fc-cache -f -v

   ENTRYPOINT ["dotnet", "gView.Server.dll"]


Ein Container kann mit folgendem Befehl erzeugt werden:

.. code::
     
    docker build -t gview_server:5.0.1 .
    docker tag gview_server:5.0.1 gview_server:latest

Der kann mit folgendem Befehl gestartet werden:

.. code::

   docker run -d -p 8085:80 --name=gview-server gview_server:latest
   


