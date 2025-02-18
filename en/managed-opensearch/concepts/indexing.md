# Indexes in {{ OS }}

When saving a document to {{ OS }}, it is indexed and placed in a user-specified _index_, making it available for search and analysis. An index is an analogue of a data table in a traditional DBMS.

In {{ OS }}, a document is a set of fields where each field is a `key: value` pair. The index stores optimized documents to enable quickly searching documents by field. Optimization is possible because each document field has a specific type. This is how the field data is effectively stored in the index. For more information about the optimization, see the [{{ OS }} documentation]({{ os.docs }}/opensearch/mappings/).

Unlike a traditional DBMS, to save the document in the index, {{ OS }} doesn't require the explicit specification of the schema (links between document fields and their types). Even though it's the recommended approach, you can save documents to the index without explicitly specifying the field types. {{ OS }} will try to determine the type automatically for each field in the document. As a result, you can quickly add documents to {{ OS }} storage and start working with them.

To learn more about how indexes work, see the [{{ OS }} documentation]({{ os.docs }}/opensearch/index-data/).

In multihost clusters, index [sharding and replication](scalability-and-resilience.md) are supported. This makes it easier to scale the cluster and improves fault tolerance.
