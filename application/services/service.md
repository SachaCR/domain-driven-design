# Service

> In some cases, the clearest and most pragmatic design includes operations that do not conceptually belong to any object. Rather than force the issue, we can follow the natural contours of the problem spave and include SERVICES explicitly in the model.
>
> Some concepts from the domain aren't natural to model as objects. Forcing the required domain finctionnality to be the reponsibility of an ENTITY or VALUE either distorts the definition of a model-based object or adds meaningless artificial objects.
>
> A service is an operation offered as an interface that stands alone in the >model, without encapsulating state. as ENTITIES and CALUE OBJECTS do.

```js
async function cartAddItemOperation(
  cart: Cart,
  itemId: string, // item-1354 for example
  quantity: number // 5 for example
): CartAddItemResult {
  const unitOfWork = buildDBTransaction(); // It's a DB connection abstraction
  await unitOfWork.open(); // Open a transaction with default isolation level

  try {
    // The service will pass the unitOfWork to the repository that will use it to perform his queries and lock the stock line.
    const itemStock: ItemStock = await stockRepository.getItemStock(
      itemId,
      unitOfWork
    );

    // Stock domain object will perform his reservation logic.
    const itemReservationResult = itemStock.reserve(quantity, cart.id);

    switch (itemReservationResult.outcome) {
      case "item-reserved":
        const { itemReservation } = itemReservationResult.data;

        // itemReservation = {
        //   itemId: "item-1354",
        //   cartId: "abc-123",
        //   quantity: 5,
        //   expiresAt: new Date() + "5min",
        // };

        // Cart domain object will perform the logic to add the reservation to his state.
        cart.put(itemReservation);

        await cartRepository.save(cart, unitOfWork);
        await stockRepository.save(itemStock, unitOfWork);

        await unitOfWork.commit(); // Commit the transaction and release the connection to the pool

        return {
          outcome: "item-added",
        };
        break;

      case "item-partially-reserved":
        const { itemReservation } = itemReservationResult.data;

        // itemReservation = {
        //   itemId: "item-1354",
        //   cartId: "abc-123",
        //   quantity: 2,
        //   expiresAt: new Date() + "5min",
        // };

        // Cart domain object will perform the logic to add the reservation to his state.
        cart.put(itemReservation);

        // Here is an alternative where domain object embbed the peristance logic through a save method that can take a unitOfWork.
        await cartRepository.save(cart, unitOfWork);
        await stockRepository.save(itemStock, unitOfWork);

        await unitOfWork.commit(); // Commit the transaction and release the connection to the pool

        return {
          outcome: "item-partially-added",
        };

        break;

      case "out-of-stock":
        // We still need to commit the unitOfWork otherwise the reservation attempts and associated event will be rollbacked after transaction timeout.

        // itemReservation = {
        //   itemId: "item-1354",
        //   cartId: "abc-123",
        //   quantity: 0,
        //   expiresAt: new Date(),
        // };

        await unitOfWork.commit();

        return {
          outcome: "item-out-of-stock",
        };
        break;

      default:
        throw new Error("Unknown...");
        break;
    }
  } catch (error) {
    // In error case we rollback the entire unitOfWork.
    await unitOfWork.rollback();

    return {
      outcome: "add-item-failed",
      data: error,
    };
  }
}
```
