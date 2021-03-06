.. _projects:

Projects
========

Projects in Portia consist of one or more :ref:`spiders <spiders>` and can be deployed to any `scrapyd`_ instance.

Versioning
----------

.. _project-deployment:

Portia provides project versioning via Git, but this isn't enabled by default.

Git versioning can be enabled by creating a `local_settings.py` file in the `slyd/slyd` directory and adding the following:

.. code-block:: python

    import os
    
    SPEC_FACTORY = {
        'PROJECT_SPEC': 'slyd.gitstorage.projectspec.ProjectSpec',
        'PROJECT_MANAGER': 'slyd.gitstorage.projects.ProjectsManager',
        'PARAMS': {
            'storage_backend': 'dulwich.repo.Repo',
            'location': os.environ.get('PORTIA_DATA_DIR', SPEC_DATA_DIR)
        },
        'CAPABILITIES': {
            'version_control': True,
            'create_projects': True,
            'delete_projects': True,
            'rename_projects': True
        }
    }

You can also use MySQL to store your project files in combination with Git:

.. code-block:: python

    import os

    SPEC_FACTORY = {
        'PROJECT_SPEC': 'slyd.gitstorage.projectspec.ProjectSpec',
        'PROJECT_MANAGER': 'slyd.gitstorage.projects.ProjectsManager',
        'PARAMS': {
            'storage_backend': 'slyd.gitstorage.repo.MysqlRepo',
            'location': os.environ.get('DB_URL'),
        },
        'CAPABILITIES': {
            'version_control': True,
            'create_projects': True,
            'delete_projects': True,
            'rename_projects': True
        }
    }

This will store versioned projects as blobs within the MySQL database that you specify by setting the environment variable below::

    DB_URL = mysql://<USERNAME>:<PASSWORD>@<HOST>:<PORT>/<DB>

When this env variable is set the database can be initialized by running the bin/init_mysqldb script.

.. note:: The MySQL backend only stores project data. Data generated during crawl is still stored locally.

Deployment
----------

Portia projects can be deployed using `scrapyd`_. You can deploy a Portia project by going into ``slyd/data/projects/PROJECT_NAME`` and adding your target to ``scrapy.cfg``. You can then run ``scrapyd-deploy`` to deploy your project using the default deploy target, or specify a target and project using the following::

    scrapyd-deploy your_scrapyd_target -p project_name

and then schedule your spider with::

    curl http://your_scrapyd_host:6800/schedule.json -d project=your_project_name -d spider=your_spider_name

.. warning:: Running scrapyd from your project directory will cause deployment to fail.

.. _scrapyd: https://scrapyd.readthedocs.org/en/latest/
