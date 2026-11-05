# SC-12. Одночасне бронювання одного товару

```mermaid
sequenceDiagram
    actor A as Користувач A
    actor B as Користувач B
    participant S as WishlistService
    participant W as Wishlist
    participant Item as WishlistItem
    participant R as Reservation

    A->>S: reserveItem(itemId)
    activate S
    B->>S: reserveItem(itemId)

    par перевірка запиту A
        S->>Item: findById(itemId)
        activate Item
        Item-->>S: item
        deactivate Item
        S->>W: findById(wishlistId)
        activate W
        W-->>S: wishlist
        deactivate W
        S->>W: isPublic()
        activate W
        W-->>S: true
        deactivate W
        S->>W: isOwnedBy(userA)
        activate W
        W-->>S: false
        deactivate W
        S->>Item: isCompleted()
        activate Item
        Item-->>S: false
        deactivate Item
        S->>R: findByItemId(itemId)
        activate R
        R-->>S: empty
        deactivate R
    and перевірка запиту B
        S->>Item: findById(itemId)
        activate Item
        Item-->>S: item
        deactivate Item
        S->>W: findById(wishlistId)
        activate W
        W-->>S: wishlist
        deactivate W
        S->>W: isPublic()
        activate W
        W-->>S: true
        deactivate W
        S->>W: isOwnedBy(userB)
        activate W
        W-->>S: false
        deactivate W
        S->>Item: isCompleted()
        activate Item
        Item-->>S: false
        deactivate Item
        S->>R: findByItemId(itemId)
        activate R
        R-->>S: empty
        deactivate R
    end

    alt першим фіксується запит A
        S->>R: createReservation(itemId, userA)
        activate R
        R-->>S: reservationId
        deactivate R
        S-->>A: reservationId
        S->>R: createReservation(itemId, userB)
        activate R
        R-->>S: conflict
        deactivate R
        S-->>B: rejectReservation()
    else першим фіксується запит B
        S->>R: createReservation(itemId, userB)
        activate R
        R-->>S: reservationId
        deactivate R
        S-->>B: reservationId
        S->>R: createReservation(itemId, userA)
        activate R
        R-->>S: conflict
        deactivate R
        S-->>A: rejectReservation()
    end
    deactivate S
```
