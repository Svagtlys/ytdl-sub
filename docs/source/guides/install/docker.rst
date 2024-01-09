======
Docker
======

For automating ``subscriptions.yaml`` downloads to pull new media, see :ref:`this page <guides/getting_started/automating_downloads:docker and unraid>` on how to set up a cron job in any of the docker containers.

The ``ytdl-sub`` Docker images use :lsio:`LSIO-based images <\ >` and install ytdl-sub on top. There are two flavors to choose from.

.. margin:: 

  .. tip:: 

    The recommended docker image is the GUI image.

    :ref:`Docker Compose <guides/install/docker:install with docker compose>` is the recommended way of setting up a ``ytdl-sub`` docker container.

:ref:`Troubleshooting your Docker install <guides/install/docker:troubleshooting>`

GUI Image
---------

The GUI image uses LSIO's :lsio-gh:`docker-code-server` for its base image. More info on other code-server environment variables can be found within its documentation.

After starting, the code-server will be running at http://localhost:8443. Open this page in a browser to access and interact with ``ytdl-sub``.
After starting, the code-server will be running at http://localhost:8443. Open this page in a browser to access and interact with ``ytdl-sub``.

Headless Image
--------------

The headless image uses LSIO's :lsio-gh:`docker-baseimage-alpine` for its base image. Execute the following command to access and interact with ``ytdl-sub``:

.. code-block:: bash

  docker exec -u abc -it ytdl-sub /bin/bash

Install with Docker Compose
---------------------------

Docker Compose is an easy "set it and forget it" install method. Follow the instructions below to create a ``compose.yaml`` file for your chosen ``ytdl-sub`` image.

.. margin:: 

  .. important:: 

    Set the PUID and PGID to the UID and GID associated with the user you want to own the downloaded files. Setting these values to root UID and GID may create issues with your media managers.

.. tab-set:: 

  .. tab-item:: GUI Image

    .. code-block:: yaml
      :caption: compose.yaml

      services:
        ytdl-sub:
          image: ghcr.io/jmbannon/ytdl-sub-gui:latest
          container_name: ytdl-sub
          environment:
            - PUID=1000
            - PGID=1000
            - TZ=America/Los_Angeles
          volumes:
            - path/to/ytdl-sub/config:/config
            - path/to/tv_shows:/tv_shows  # optional
            - path/to/movies:/movies  # optional
            - path/to/music_videos:/music_videos  # optional
            - path/to/music:/music  # optional
          ports:
            - 8443:8443
          restart: unless-stopped

  .. tab-item:: Headless Image

    .. code-block:: yaml
      :caption: compose.yaml

      services:
        ytdl-sub:
          image: ghcr.io/jmbannon/ytdl-sub:latest
          container_name: ytdl-sub
          environment:
            - PUID=1000
            - PGID=1000
            - TZ=America/Los_Angeles
            - DOCKER_MODS=linuxserver/mods:universal-cron
          volumes:
            - path/to/ytdl-sub/config:/config
            - path/to/tv_shows:/tv_shows  # optional
            - path/to/movies:/movies  # optional
            - path/to/music_videos:/music_videos  # optional
            - path/to/music:/music  # optional
          restart: unless-stopped

Device Passthrough
~~~~~~~~~~~~~~~~~~~
For CPU or GPU passthrough, you must use either the GUI image or the headless Ubuntu image
``ghcr.io/jmbannon/ytdl-sub:ubuntu-latest``.

The docker-compose examples use the GUI image.

CPU Passthrough
^^^^^^^^^^^^^^^

.. code-block:: yaml
  :emphasize-lines: 5-6
  :caption: compose.yaml

  services:
    ytdl-sub:
      image: ghcr.io/jmbannon/ytdl-sub-gui:latest
      container_name: ytdl-sub
      devices:
        - /dev/dri:/dev/dri  # CPU passthrough
      restart: unless-stopped

GPU Passthrough
^^^^^^^^^^^^^^^

.. Awe

.. code-block:: yaml
  :caption: compose.yaml
  :emphasize-lines: 5-13

  services:
  ytdl-sub:
    image: ghcr.io/jmbannon/ytdl-sub-gui:latest
    container_name: ytdl-sub
    environment:
    - ..
    - NVIDIA_DRIVER_CAPABILITIES=all  # Nvidia ENV args
    - NVIDIA_VISIBLE_DEVICES=all
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: ["gpu"]  # GPU passthrough
    restart: unless-stopped

Docker CLI
----------

If you prefer to only run the container once, you can use the CLI command instead. The following command is for the gui image, and will not restart if it comes down for any reason. See `the Docker reference <https://docs.docker.com/engine/reference/run/>`_ for further information on the parameters and other options you can use.

.. code-block:: bash

  docker run -d \
    --name=ytdl-sub \
    -e PUID=1000 \
    -e PGID=1000 \
    -e TZ=America/Los_Angeles \
    -p 8443:8443 \
    -v path/to/ytdl-sub/config:/config \
    -v OPTIONAL/path/to/tv_shows:/tv_shows \
    -v OPTIONAL/path/to/movies:/movies \
    -v OPTIONAL/path/to/music_videos:/music_videos \
    -v OPTIONAL/path/to/music:/music \
    ghcr.io/jmbannon/ytdl-sub-gui:latest


Troubleshooting
---------------

Find your error message below to troubleshoot your issue with the Docker install.

``PermissionError: [Errno 13] Permission denied``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Directory Name
^^^^^^^^^^^^^^

From the computer that you've installed the container on, but not within the container, do the mapped folders exist?

Using the folder paths from the install instructions, the paths to check would be:

.. code-block:: shell

  cd path/to/ytdl-sub/config
  cd path/to/tv_shows
  cd path/to/movies
  cd path/to/music_videos
  cd path/to/music

If you're able to ``cd`` into all of these, then continue to the next step. If not, run the following command for each folder that does not exist:

.. code-block:: shell

  mkdir -p path/to/folder

Directory Access
^^^^^^^^^^^^^^^^

From the computer that you've installed the container on, but not within the container, does your user have access to the folders?

Using the folder paths from the install instructions, the commands to check would be:

.. code-block:: shell

  ls -ld path/to/ytdl-sub/config
  ls -ld path/to/tv_shows
  ls -ld path/to/movies
  ls -ld path/to/music_videos
  ls -ld path/to/music

Verify that the owner and group (columns 3 and 4) are the same as the user you're logged in as. To confirm which user and group you're using:

.. code-block:: shell

  whoami
  groups

If the owner does not match your user, or the group is not in your list of groups, you will need to change the permissions, where ``user`` is your user from the above command, and ``group`` is the first group in the list from the above command. You will need to run this command for each folder that is having permission issues.

.. code-block:: shell

  chown -R user:group path/to/folder

PGID and PUID
^^^^^^^^^^^^^

From the computer that you've installed the container on, but not within the container, what is the correct PUID and PGID to access the folders?

You need to check the PUID and PGID, where ``user`` is your user from the above step, and ``group`` is the first group in the list from the above step. 

.. code-block:: shell

  id 

The output should look like:

``uid=####(user) gid=####(group) ...``

The first number, between ``uid=`` and ``(user)`` is your PUID. The second number, between ``gid=`` and ``(group)`` is your PGID.

In your compose file, confirm that the highlighted lines have the correct numbers for these variables:

.. code-block:: yaml
  :caption: compose.yaml
  :emphasize-lines: 6, 7

  services:
    ytdl-sub:
      image: ghcr.io/jmbannon/ytdl-sub:latest
      container_name: ytdl-sub
      environment:
        - PUID=####
        - PGID=####

If you had to update these values, run the following command to restart your docker container:

.. code-block:: shell

  docker compose restart