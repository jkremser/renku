.. _graph_service:

Graph service
=============

In Renku, the dependencies of research artifacts are recorded into a knowledge
graph. Each project's local knowledge graph is recorded in its repository; the
creation of the global knowledge graph is possible via the graph service. When
a project's repository is pushed to the server, a webhook is triggered that
causes the changes represented by the commits and all of the captured
dependencies to be rendered as RDF triples and pushed to the triple store.

The graph service is made up of three micro-services: the webhook-service,
triples-generator, and the triple store (currently Apache Jena). The basic
architecture is illustrated below.

.. _fig-graph-service-architecture:

.. graphviz:: /_static/graphviz/graph_service_architecture.dot


Sequence diagram of Graph Services APIs and processes.
""""""""""""""""""""""""""""""""""""""""""""""""""""""
**POST <webhook-service>/projects/:id/webhooks**

An endpoint to create a Graph Services webhook for a project in GitLab.

.. uml:: ../../_static/uml/graph-create-hook-sequence.uml

**POST <webhook-service>/projects/:id/webhooks/validation**

An endpoint to validate project's webhook. It checks if a relevant
Graph Services webhook exists on the repository in GitLab and
if Graph Services have an Access Token associated with the project
so they can use it for finding project specific information in GitLab.

.. uml:: ../../_static/uml/graph-validate-hook-sequence.uml

**POST <webhook-service>/webhooks/events**

An endpoint to send Push Events containing information about commits pushed to the GitLab.

.. uml:: ../../_static/uml/graph-push-event-sequence.uml

**GET <webhook-service>/projects/:id/events/status**

An endpoint that returns information about processing progress of events for a specific project.

.. uml:: ../../_static/uml/graph-events-status-sequence.uml

**Commit Events to RDF Triples**

A process responsible for translating unprocessed Commit Events from the Event Log
to RDF Triples in the RDF Store. This process runs continuously
by polling the Event Log for Commit Events.

.. uml:: ../../_static/uml/graph-commit-event-sequence.uml

**Missed commits synchronization job**

A scheduled job which synchronizes state between the Event Log and GitLab
and generates Commit Events missing from the Event Log.
It runs periodically with a configured interval.

.. uml:: ../../_static/uml/graph-sync-events-sequence.uml
