# Command Handlers :

Command handler responsibilities are :

- Loads entities/aggregates from repositories.
- Use their functions to mutate their internal state and resolve the command.
- Persist updated entities with repositories.
- Return a result outcome.

OR

- Loads entities from repositories.
- Call a service to coordinate the operation. (It's the service that will persist the entities).
- Return a result outcome.
