Purpose
-------

Store CouchDB records efficiently in order to minimize changes for Riot.

The default comparison function compares the document IDs.

    compare_ids = (a,b) -> a._id.localeCompare b._id

    class InfamousTerritory

The first parameter is the array where we will be storing data.
The second parameter is a comparison function `compare(doc_a,doc_b)` which returns <0 if doc_a < doc_b, 0 if doc_a = doc_b, >0 if doc_a > doc_b.
Note: the comparison function must not return 0 for documents with different IDs (= different documents). If different documents are considered "identical" they should be sorted on their IDs using `InfamousTerritory.compare()`.

      constructor: (@array,@compare = compare_ids) ->

      _dicho: (doc) ->
        start = 0
        end = @array.length
        while start < end
          middle = (start+end) // 2
          cmp = @compare doc, @array[middle]

The comparison function might decide two elements are the same even though they do not have the same id. Just deal with it.

          switch

            when cmp is 0 # meaning doc = @array[middle]
              start = end = middle

            when cmp < 0 # meaning doc < @array[middle]
              end = middle-1

            when cmp > 0 # meaning doc > @array[middle]
              start = middle+1

        start

      add: (doc) ->
        pos = @_dicho doc
        old = @array[pos]
        @_update old, doc, pos

      _update: (old,doc,pos) ->
        if old?._id is doc._id
          if old._rev? and doc._rev? and old._rev is doc._rev
            return null
          else
            @array[pos] = doc
            old
        else
          @array.splice pos, 0, doc
          null

      delete: (doc) ->
        pos = @_dicho doc
        old = @array[pos]
        if old?._id is doc._id
          @array.splice pos, 1
        else
          []

Given a list of documents, ensure the underlying object matches the new list, with minimum changes.

      set: (docs,sorted = false) ->
        docs.sort @compare unless sorted

        our = 0
        their = 0

        docs_length = docs.length

        while our < @array.length and their < docs_length


Delete any existing element missing in the new set.

          start = our
          their_doc = docs[their]
          while our < @array.length and their < docs_length and (@compare @array[our], their_doc) < 0
            our++

          if start < our
            @array.splice start, our-start
            our = start

Update any identical elements

          while our < @array.length and their < docs_length and (@compare our_doc = @array[our], their_doc = docs[their]) is 0
            @_update our_doc, their_doc, our
            their++

Either we kept the existing document (and we should move forward in our list), or we inserted a new one and we need to re-test the same element (in which case it is now at the next position since we inserted a new one), so in both cases we need to increment our position.

            our++

Insert any new elements

          start = their
          our_doc = @array[our]
          while our < @array.length and their < docs_length and (@compare our_doc, docs[their]) > 0
            their++

          if start < their
            @array.splice our, 0, docs[start...their]...
            our += their-start

        # end while

Delete any existing elements at the end.

        if our < @array.length
          @array.splice our, @array.length-our

Insert any new elements at the end.

        if their < docs_length
          @array.splice @array.length, 0, docs[their...]...

        return

    module.exports = InfamousTerritory
    module.exports.compare = compare_ids
