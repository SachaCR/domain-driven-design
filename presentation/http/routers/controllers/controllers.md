# Controllers

Controllers responsibilities are :

- Validate payload (DTO) :

  ```javascript
  const isValid = validate(payload, createUserSchema);
  ```

- Instanciate a command :

  ```javascript
  const command = buildCreateUserCommand(DTO);
  ```

- Call the associated command handler :

  ```js
  const result = await createUserCommandHandler(command);
  ```

- Determine the status code and return a response :

  ```javascript
  const status = result.outcome === "user-created" ? 201 : 400;
  ctx.status = status;
  ctx.body = "OK";
  return;
  ```
