概述
====

花10分钟上手Symfony：本文将向你介绍Symfony几个最重要的概念，并用一个简单的例子来演示如何基于Symfony进行Web开发。

如果你之前使用过其他的Web开发框架（译注：如RoR、Django、Spring等），那你对Symfony的开发风格一定不会感到陌生。

在阅读本文之前，请确保你的机器上安装了 **5.4及以上版本的PHP** 。如果你用的是WAMP、XAMP或MAMP之类的一键安装包，也请确认其捆绑的PHP版本是否足够新。你可以用下面的命令行指令来检查PHP的版本：

.. code-block:: bash

    $ php --version

.. _installing-symfony2:

安装Symfony
-----------

过去，你需要为每个项目分别手动安装一次Symfony；现在， **Symfony安装工具** 使安装过程变得更加轻松了。

在 **Linux** 或者 **Mac OS X** 系统上，你可以运行下面的命令行指令来获得Symfony安装工具：

.. code-block:: bash

    $ curl -LsS http://symfony.com/installer > symfony.phar
    $ sudo mv symfony.phar /usr/local/bin/symfony

.. note::

    如果你的系统上没有安装cURL，你可以改用下面的指令：

    .. code-block:: bash

        $ php -r "readfile('http://symfony.com/installer');" > symfony.phar
        $ sudo mv symfony.phar /usr/local/bin/symfony

上述指令成功运行后，你（可能）需要进入新打开的命令行窗口，以使 ``symfony`` 命令生效。

.. code-block:: bash

    $ symfony

在 **Windows** 系统上，运行下面的指令即可：

.. code-block:: bash

    c:\> php -r "readfile('http://symfony.com/installer');" > symfony.phar

上述指令将下载一个名为 ``symfony.phar`` 的文件，文件里包含了Symfony安装工具。把这个文件移动到你打算创建Symfony项目的目录，如果要在C盘根目录下使用这个工具，则调用方式为：

.. code-block:: bash

    c:\> php symfony.phar

创建你的第一个Symfony项目
-------------------------

确认能正常调用Symfony安装工具了，你就可以使用其提供的 ``new`` 选项来创建新的Symfony项目。我们下面把示例项目命名为 ``myproject`` ：

.. code-block:: bash

    # Linux 或 Mac OS X
    $ symfony new myproject

    # Windows
    c:\> php symfony.phar new myproject

上述指令将下载最新稳定版的Symfony，并在 ``myproject/`` 文件夹里初始化一个空的项目——好了，你可以开始写代码了！

.. _running-symfony2:

运行Symfony
-----------

本文介绍的，是使用PHP自带的Web服务器来运行Symfony。你只需要进入Symfony项目代码所在的目录，执行下面的指令：

.. code-block:: bash

    $ cd myproject/
    $ php app/console server:run

然后打开浏览器，访问 ``http://localhost:8000`` ，你将能看到Symfony的欢迎页：

.. image:: /images/quick_tour/welcome.png
   :align: center
   :alt:   Symfony Welcome Page

热烈祝贺！你的第一个Symfony项目运行起来了！

.. note::

    如果你看到的是空白页或错误信息页，而不是上图所示的欢迎页，很有可能是因为文件权限的问题。
    针对部分操作系统，我们提供了解决这个问题的办法： :ref:`Setting up Permissions <book-installation-permissions>`

关闭这个Web服务非常简单：

.. code-block:: bash

    $ php app/console server:stop

.. tip::

    如果你偏好通过Apache或Nginx这类专门的Web服务器来运行Symfony，你可以参考：
    :doc:`/cookbook/configuration/web_server_configuration` 。

了解基本概念
------------

使用开发框架的主要目的之一是使你的代码组织结构更好，在应用程序的开发、维护过程中，避免数据库操作、HTML和业务逻辑等等不同方面的代码混在一起。要通过使用Symfony来达成这个目标，你首先需要了解一些基本的概念。

开发一个Symfony项目，程序员所需要做的，是把用户的 *请求* （比如，欢迎页的地址： ``http://localhost:8000/`` ）指向与其相关的 *资源* （比如，欢迎页的内容）。

响应用户请求的目标代码，构成 **动作** 和 **控制器** 。而将代码与用户的请求对应起来的，是被称为 **路由规则** 的配置。最终显示在浏览器里的内容，一般是通过 **模板** 来输出的。

当你访问 ``http://localhost:8000/`` 时，Symfony执行了在 ``src/AppBundle/Controller/DefaultController.php`` 文件里定义的控制器，并输出了由 ``app/Resources/views/default/index.html.twig`` 模板渲染的内容。接下来，你将了解到更多关于Symfony控制器、路由、模板的内部细节。

动作与控制器
~~~~~~~~~~~~

打开 ``src/AppBundle/Controller/DefaultController.php`` 文件，你会看到如下的代码（暂时不必关心 ``@Route`` 这个配置，后面会再讲到）： ::

    namespace AppBundle\Controller;

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function indexAction()
        {
            return $this->render('default/index.html.twig');
        }
    }

在Symfony项目里， **控制器** 一般被编写为命名包含 ``Controller`` 后缀的PHP类。
在这个例子里，控制器的名字是 ``Default`` ，PHP类命名为 ``DefaultController`` 。

The methods defined in a controller are called **actions**, they are usually
associated with one URL of the application and their names are suffixed with
``Action``. In this example, the ``Default`` controller has only one action
called ``index`` and defined in the ``indexAction`` method.

Actions are usually very short - around 10-15 lines of code - because they just
call other parts of the application to get or generate the needed information and
then they render a template to show the results to the user.

In this example, the ``index`` action is practically empty because it doesn't
need to call any other method. The action just renders a template with the
*Welcome to Symfony!* content.

Routing
~~~~~~~

Symfony routes each request to the action that handles it by matching the
requested URL against the paths configured by the application. Open again the
``src/AppBundle/Controller/DefaultController.php`` file and take a look at the
three lines of code above the ``indexAction`` method:

.. code-block:: php

    // src/AppBundle/Controller/DefaultController.php
    namespace AppBundle\Controller;

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function indexAction()
        {
            return $this->render('default/index.html.twig');
        }
    }

These three lines define the routing configuration via the ``@Route()`` annotation.
A **PHP annotation** is a convenient way to configure a method without having to
write regular PHP code. Beware that annotation blocks start with ``/**``, whereas
regular PHP comments start with ``/*``.

The first value of ``@Route()`` defines the URL that will trigger the execution
of the action. As you don't have to add the host of your application to the URL
(e.g. ```http://example.com``), these URLs are always relative and they are usually
called *paths*. In this case, the ``/`` path refers to the application homepage.
The second value of ``@Route()`` (e.g. ``name="homepage"``) is optional and sets
the name of this route. For now this name is not needed, but later it'll be useful
for linking pages.

Considering all this, the ``@Route("/", name="homepage")`` annotation creates a
new route called ``homepage`` which makes Symfony execute the ``index`` action
of the ``Default`` controller when the user browses the ``/`` path of the application.

.. tip::

    In addition to PHP annotations, routes can be configured in YAML, XML or
    PHP files, as explained in `the Routing chapter of the Symfony book`_ .
    This flexibility is one of the main features of Symfony, a framework that
    never imposes a particular configuration format on you.

Templates
~~~~~~~~~

The only content of the ``index`` action is this PHP instruction:

.. code-block:: php

    return $this->render('default/index.html.twig');

The ``$this->render()`` method is a convenient shortcut to render a template.
Symfony provides some useful shortcuts to any controller extending from the
``Controller`` class.

By default, application templates are stored in the ``app/Resources/views/``
directory. Therefore, the ``default/index.html.twig`` template corresponds to the
``app/Resources/views/default/index.html.twig``. Open that file and you'll see
the following code:

.. code-block:: html+jinja

    {# app/Resources/views/default/index.html.twig #}
    {% extends 'base.html.twig' %}

    {% block body %}
        <h1>Welcome to Symfony!</h1>
    {% endblock %}

This template is created with `Twig`_, a new template engine created for modern
PHP applications. The :doc:`second part of this tutorial </quick_tour/the_view>`
will introduce how templates work in Symfony.

.. _quick-tour-big-picture-environments:

Working with Environments
-------------------------

Now that you have a better understanding of how Symfony works, take a closer
look at the bottom of any Symfony rendered page. You should notice a small
bar with the Symfony logo. This is the "Web Debug Toolbar", and it is a
Symfony developer's best friend!

.. image:: /images/quick_tour/web_debug_toolbar.png
   :align: center

But what you see initially is only the tip of the iceberg; click on any of the
bar sections to open the profiler and get much more detailed information about
the request, the query parameters, security details, and database queries:

.. image:: /images/quick_tour/profiler.png
   :align: center

This tool provides so much internal information about your application that you
may be worried about your visitors accessing sensible information. Symfony is
aware of this issue and for that reason, it won't display this bar when your
application is running in the production server.

How does Symfony know  whether your application is running locally or on a
production server? Keep reading to discover the concept of **execution environments**.

.. _quick-tour-big-picture-environments-intro:

What is an Environment?
~~~~~~~~~~~~~~~~~~~~~~~

An :term:`Environment` represents a group of configurations that's used to run
your application. Symfony defines two environments by default: ``dev``
(suited for when developing the application locally) and ``prod`` (optimized
for when executing the application on production).

When you visit the ``http://localhost:8000`` URL in your browser, you're executing
your Symfony application in the ``dev`` environment. To visit your application
in the ``prod`` environment, visit the ``http://localhost:8000/app.php`` URL instead.
If you prefer to always show the ``dev`` environment in the URL, you can visit
``http://localhost:8000/app_dev.php`` URL.

The main difference between environments is that ``dev`` is optimized to provide
lots of information to the developer, which means worse application performance.
Meanwhile, ``prod`` is optimized to get the best performance, which means that
debug information is disabled, as well as the Web Debug Toolbar.

The other difference between environments is the configuration options used to
execute the application. When you access the ``dev`` environment, Symfony loads
the ``app/config/config_dev.yml`` configuration file. When you access the ``prod``
environment, Symfony loads ``app/config/config_prod.yml`` file.

Typically, the environments share a large amount of configuration options. For
that reason, you put your common configuration in ``config.yml`` and override
the specific configuration file for each environment where necessary:

.. code-block:: yaml

    # app/config/config_dev.yml
    imports:
        - { resource: config.yml }

    web_profiler:
        toolbar: true
        intercept_redirects: false

In this example, the ``config_dev.yml`` configuration file imports the common
``config.yml`` file and then overrides any existing web debug toolbar configuration
with its own options.

For more details on environments, see
":ref:`Environments & Front Controllers <page-creation-environments>`" article.

Final Thoughts
--------------

Congratulations! You've had your first taste of Symfony code. That wasn't so
hard, was it? There's a lot more to explore, but you should already see how
Symfony makes it really easy to implement web sites better and faster. If you
are eager to learn more about Symfony, dive into the next section:
":doc:`The View <the_view>`".

.. _Composer: https://getcomposer.org/
.. _executable installer: http://getcomposer.org/download
.. _Twig: http://twig.sensiolabs.org/
.. _the Routing chapter of the Symfony book: http://symfony.com/doc/current/book/routing.html
