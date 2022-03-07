# Primary key

The primary key is a **mandatory attribute linked to a unique value** the [document id](/learn/core_concepts/documents.md#document-id). It is part of the [primary field](/learn/core_concepts/documents.md#primary-field).

Each index recognizes **only one** primary key attribute. Once a primary key has been set for an index, it **cannot be changed anymore**. If no primary key is found in a document, **no documents will be stored.**

The primary key ensures that there are no identical documents in the same index.

## Setting the primary key

There are several ways to set the primary key for an index:

### Setting the primary key on index creation

The code below creates an index called `movies` with `reference_number` as primary key:

<CodeSamples id="document_guide_create_index_primary_key" />

```json
{
    "uid":1,
    "indexUid":"movies",
    "status":"succeeded",
    "type":"indexCreation",
    "details":
    {
        "primaryKey":"reference_number"
        },
    "duration":"PT0.006751S",
    "enqueuedAt":"2021-12-30T10:19:00.318884Z",
    "startedAt":"2021-12-30T10:19:00.320177Z",
    "finishedAt":"2021-12-30T10:19:00.325635Z"
}    
```

### Setting the primary key on document addition

The code below adds a document and sets `reference_number` as the index's primary key:

<CodeSamples id="document_guide_add_document_primary_key" />

```json
{
    "uid":1,
    "indexUid":"movies",
    "status":"succeeded",
    "type":"documentAddition",
    "details":
    {
        "receivedDocuments":1,
        "indexedDocuments":1
        },
    "duration":"PT0.010418S",
    "enqueuedAt":"2021-12-30T10:38:22.331502Z",
    "startedAt":"2021-12-30T10:38:22.332686Z",
    "finishedAt":"2021-12-30T10:38:22.341920Z"
}
```

### Meilisearch guesses your primary key

If the primary key has neither been set at index creation nor as a parameter of the [add documents]((/reference/api/documents.md#add-or-replace-documents)) route, Meilisearch will search your first document for an attribute that contains the string `id` in a case-insensitive manner (e.g., `uid`, `MovieId`, `ID`, `123id123`) and set it as that index's primary key.

If no corresponding attribute is found, the index will have no known primary key, and therefore, **no documents will be added**.

## Primary key errors

This section will cover some primary key errors and how to resolve them.

### `primary_key_inference_failed`

This happens when you add documents for the first time and none of them have a primary key attribute.

```json
{
    "uid":1,
    "indexUid":"books",
    "status":"failed",
    "type":"documentAddition",
    "details":
    {
        "receivedDocuments":5,
        "indexedDocuments":null
        },
    "error":
    {
        "message":"The primary key inference process failed because the engine did not find any fields containing `id` substring in their name. If your document identifier does not contain any `id` substring, you can set the primary key of the index.",
        "code":"primary_key_inference_failed",
        "type":"invalid_request",
        "link":"https://docs.meilisearch.com/errors#primary_key_inference_failed"
        },
    "duration":"PT0.007479S",
    "enqueuedAt":"2021-12-30T11:17:49.708824Z",
    "startedAt":"2021-12-30T11:17:49.709934Z",
    "finishedAt":"2021-12-30T11:17:49.716303Z"
}
```

### `missing_document_id`

This happens when your index already has a primary key but one of the documents you are currently trying to add is missing this attribute.

```json
{
    "uid":1,
    "indexUid":"books",
    "status":"failed",
    "type":"documentAddition",
    "details":
    {
        "receivedDocuments":1,
        "indexedDocuments":null
        },
    "error":
    {
        "message":"Document doesn't have a `id` attribute: `{\"title\":\"Solaris\",\"author\":\"Stanislaw Lem\",\"genres\":[\"science fiction\"],\"price\":5.0,\"priority\":10}`.",
        "code":"missing_document_id",
        "type":"invalid_request",
        "link":"https://docs.meilisearch.com/errors#missing_document_id"
        },
    "duration":"PT0.007899S",
    "enqueuedAt":"2021-12-30T11:23:52.304689Z",
    "startedAt":"2021-12-30T11:23:52.307632Z",
    "finishedAt":"2021-12-30T11:23:52.312588Z"
}
```

### `invalid_document_id`

This happens when your primary key does not have the correct format. The primary key can only be of type `integer` or `string`, composed of alphanumeric characters `a-z A-Z 0-9`, hyphens `-` and underscores `_`.

```json
{
    "uid":1,
    "indexUid":"books",
    "status":"failed",
    "type":"documentAddition",
    "details":
    {
        "receivedDocuments":5,
        "indexedDocuments":null
        },
    "error":{
        "message":"Document identifier `1@` is invalid. A document identifier can be of type integer or string, only composed of alphanumeric characters (a-z A-Z 0-9), hyphens (-) and underscores (_).","code":"invalid_document_id",
        "type":"invalid_request",
        "link":"https://docs.meilisearch.com/errors#invalid_document_id"
        },
    "duration":"PT0.009738S",
    "enqueuedAt":"2021-12-30T11:28:59.075065Z",
    "startedAt":"2021-12-30T11:28:59.076144Z",
    "finishedAt":"2021-12-30T11:28:59.084803Z"
}
```

Manually adding the primary key can be accomplished by using its name as a parameter for [the add document route](/reference/api/documents.md#add-or-replace-documents) or [the update index route](/reference/api/indexes.md#create-an-index).
