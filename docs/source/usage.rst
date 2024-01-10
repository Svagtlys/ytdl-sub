=====
Usage
=====

.. code-block::

  ytdl-sub [GENERAL OPTIONS] {sub,dl,view} [COMMAND OPTIONS]

For Windows users, it would be ``ytdl-sub.exe``

General Options
---------------

General options must be specified before the command (i.e. ``sub``).

-c, --config config_path  path to the config yaml, uses config.yaml if not provided
-d, --dry-run   preview what a download would output, does not perform any video downloads or writes to output directories
-h, --help            show this help message and exit
-l, --log-level log_level  Set the logging level (``quiet``, ``info``, ``verbose``, or ``debug``), uses ``info`` if not provided
-m, --match match_sub    match subscription names in a later defined subscription file to one or more strings, and only run those subscriptions
-st, --suppress-transaction-log   do not output transaction logs to console or file
-t, --transaction-log transaction_path   path to store the transaction log output of all files added, modified, deleted
-v, --version         show program's version number and exit

Sub Options
-----------

Download all subscriptions specified in each ``SUBPATH``.

.. code-block::

   ytdl-sub [GENERAL OPTIONS] sub [SUBPATH ...]

``SUBPATH`` is one or more paths to subscription files, uses ``subscriptions.yaml`` if not provided.
It will use the config specified by ``--config``, or ``config.yaml`` if not provided.

-o, --dl-override dl_override   override all subscription config values using `dl` syntax, i.e. ``--dl-override='--ytdl_options.max_downloads 3'``
-u, --update-with-info-json  update all subscriptions with the current config using info.json files

Download Options
-----------------

Download a single subscription in the form of CLI arguments.

.. code-block::

  ytdl-sub [GENERAL OPTIONS] dl [SUBSCRIPTION ARGUMENTS ...]

``SUBSCRIPTION ARGUMENTS`` are exactly the same as YAML arguments, but use periods (``.``) instead
of indents for specifying YAML from the CLI. For example, you can represent this subscription:

.. code-block:: yaml

  rick_a:
    preset:
      - "tv_show"
    overrides:
      tv_show_name: "Rick A"
      url: "https://www.youtube.com/channel/UCuAXFkgsw1L7xaCfnd5JJOw"

Using the command:

.. code-block:: bash

  ytdl-sub dl \
      --preset "tv_show" \
      --overrides.tv_show_name "Rick A" \
      --overrides.url: "https://www.youtube.com/channel/UCuAXFkgsw1L7xaCfnd5JJOw"

See how to shorten commands using :ref:`download aliases <config_reference/config_yaml:configuration>`.

View Options
-----------------

Preview the source variables for a given URL. Helps when creating new configs.

.. code-block::

   ytdl-sub view [-sc] [URL]

-sc, --split-chapters   View source variables after splitting by chapters


