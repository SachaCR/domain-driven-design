# Command Handler :

Command handler responsibilities are :

- Loads entities from repositories.
- Use their functions to resolve the command.
- Persist updated entities with repositories.
- Return a result outcome.

OR

- Loads entities from repositories.
- Call a service to coordinate the operation. (It's the service that will persist the entities).
- Return a result outcome.
