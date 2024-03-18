# Google Docs (Colloborative Editing)
- Edit Docs simulatenously
- Its not easy as consistency needs to be maintained in real-time
- 2 problems
  - Concurrency(multiple people edit on same doc)
  - Latency(users edit through internet)
- Old School Approach
  - Using locks
    - is it possible? No. As users should be able to edit concurrently and lock can b acquired by one atmost
- Need lock free approach
- Pessimistic Concurrent control: Using locks
- Optimistic Concurrent control: Being optimistic by thinking things work fine even when multiple people are working 
  - Taking versioning
  - conflicts resolution
- Sync Strategies:
  - Event Passing(Operational Transformation): Passing all events(character edit or insert,  change of font, bg color, basically any change to the doc) from user a to user b, c... 
  - Differential Sync: Line by line syncing like git diff
      - Get User a diff to user b and user b diff to user a and patch the diff and send to all clients

- GDoc uses Operational Transformation(event passing)

- Operational Transformation: 
    - optimistic concurrency control mechanism
    - allows 2 editors to modify the same section of document at the same time without conflict. 
    - Or rather, provides a mechanism for sanely resolving those conflitcs so that neither user intervention nor locking become necessary.
- Every modification from doc is repr as an event. and each event is sent from client to server and server to all other clients.
- Eg: insert('A', 1), delete('T', 10), update(x, y, 20), retain()



   





