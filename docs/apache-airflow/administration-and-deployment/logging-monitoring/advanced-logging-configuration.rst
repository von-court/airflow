 .. Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

 ..   http://www.apache.org/licenses/LICENSE-2.0

 .. Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.



Advanced logging configuration
------------------------------

Not all configuration options are available from the ``airflow.cfg`` file. The config file describes
how to configure logging for tasks, because the logs generated by tasks are not only logged in separate
files by default but has to be also accessible via the webserver.

By default standard airflow component logs are written to the ``$AIRFLOW_HOME/logs`` directory, but you
can also customize it and configure it as you want by overriding Python logger configuration that can
be configured by providing custom logging configuration object. Some configuration options require
that the logging config class be overwritten. You can do it by copying the default
configuration of Airflow and modifying it to suit your needs. The default configuration can be seen in the
`airflow_local_settings.py template <https://github.com/apache/airflow/blob/|airflow_version|/airflow/config_templates/airflow_local_settings.py>`_
and you can see the loggers and handlers used there. Except the custom loggers and handlers configurable there
via the ``airflow.cfg``, the logging methods in Airflow follow the usual Python logging convention,
that Python objects log to loggers that follow naming convention of ``<package>.<module_name>``.

You can read more about standard python logging classes (Loggers, Handlers, Formatters) in the
`Python logging documentation <https://docs.python.org/library/logging.html>`_.

Configuring your logging classes can be done via the ``logging_config_class`` option in ``airflow.cfg`` file.
This configuration should specify the import path to a configuration compatible with
:func:`logging.config.dictConfig`. If your file is a standard import location, then you should set a
:envvar:`PYTHONPATH` environment variable.

Follow the steps below to enable custom logging config class:

#. Start by setting environment variable to known directory e.g. ``~/airflow/``

    .. code-block:: bash

        export PYTHONPATH=~/airflow/

#. Create a directory to store the config file e.g. ``~/airflow/config``
#. Create file called ``~/airflow/config/log_config.py`` with following the contents:

    .. code-block:: python

      from copy import deepcopy
      from airflow.config_templates.airflow_local_settings import DEFAULT_LOGGING_CONFIG

      LOGGING_CONFIG = deepcopy(DEFAULT_LOGGING_CONFIG)

#.  At the end of the file, add code to modify the default dictionary configuration.
#. Update ``$AIRFLOW_HOME/airflow.cfg`` to contain:

    .. code-block:: ini

        [logging]
        logging_config_class = log_config.LOGGING_CONFIG

You can also use the ``logging_config_class`` together with remote logging if you plan to just extend/update
the configuration with remote logging enabled. Then the deep-copied dictionary will contain the remote logging
configuration generated for you and your modification will apply after remote logging configuration has
been added:

    .. code-block:: ini

        [logging]
        remote_logging = True
        logging_config_class = log_config.LOGGING_CONFIG


#. Restart the application.

See :doc:`../modules_management` for details on how Python and Airflow manage modules.


.. note::

   You can override the way both standard logs of the components and "task" logs are handled.
