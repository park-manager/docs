Package Architecture
====================

If you already have some experience with Symfony or other frameworks
the structure of the Park-Manager may seem a little strange at first.
But, don't worry once you understand the basics it should be all clear.

In short, Park-Manager is build using a `Hexagonal architecture`_.

.. ::

  A Hexagonal architecture (sometimes reverted to as ports and adapters)
  creates a strict separation between the core Domain (business logic) and
  the UI of an application.

  The main reason: it's much easier to run tests without having to rely
  infrastructure details like a database, filesystem or web server.

  Every form of interaction can be mocked and every communication is explicit
  and can traced throughout the application.

The most important things to know about a Hexagonal architecture are:

* Each layer doesn't know anything about it's upper layers,
  and only minimum details about it's lower layer.

* A port receives information, an adapter transforms the information before
  passing it to the port of the lower layer.

* Communication is inbound, a lower layer returns a response
  and doesn't depend on details of higher layers.

  Instead a lower layer provides an interface that higher layers
  layers implement and use for communicating with lower layers.

To ensure a proper separation, each Module is split into 4 layers.
Who in turn are each kept within a separate package and repository.

.. note::

  A ``-module`` repository/package contains the ``Domain`` layer of a module,
  so ``webhosting-module`` contains the Domain layer of the webhosting module.

Domain layer
------------

The Domain layer provides the foundation upon which all other layers are build.
It's the primary source of "knowledge", also know as the business logic.

The Domain doesn't know anything about it's upper layers, it receives
requests and gives responses.

Application layer
-----------------

Also know as: ApplicationServices or Service layer.

The Application layer communicates between the Domain and the UI layer.
But this layer doesn't know anything about a UI layer's details, instead
the layer defines interfaces which can be can be used by higher layers.

The upper layers must provide information in a normalized format that the
Application can understand (like a Data Transfer Object (DTO)).

Infrastructure layer
--------------------

The Infrastructure layer provides implementations for interfaces
that were defined in lower layers (Domain and Application)
and keeps files that are shared between UI implementations (but are
not specific to one UI implementation).

The sole purpose of this layer is an adapter between the UI layer
and Application. Otherwise the UI layer would have to duplicate to much
logic for each implementation.

.. note::

  Framework specific adapter implementations are kept within
  the same Infrastructure package and the ``Bridge`` namespace.

  Each implementation is component first, and Framework second.

  Providing an adapter implementation for a UrlGenerator uses
  the Symfony Routing Component and later an ``InfrastructureBundle``
  (in the same package) registers the service definition for actual usage.

UI Layer
--------

The user interface (UI) also know as the delivery mechanism
is the final layer of the architecture.

Unlike all deeper layers the UI layer can have multiple implementations
at the same time, eg. web (traditional website), API (REST API),
RabbitMQ CommandHandler, and/or Command line (CLI).

The UI layer MUST NEVER communicate with the Domain direct directly!
Instead it can only communicate with the Application and Infrastructure
layers.

Each UI implementation is "standalone", it may not communicate
or depend on other UI implementations directly.

.. _`Hexagonal architecture`: http://alistair.cockburn.us/Hexagonal+architecture
