```mermaid
flowchart TD
  subgraph userLane ["Користувач"]
    start([Початок])
    sendA["Надіслати reserveItem(itemId)"]
    sendB["Надіслати reserveItem(itemId), запит B"]
    takeId["Прийняти reservationId"]
    takeNo["Прийняти rejectReservation()"]
    takeIdA["Прийняти reservationId запиту A"]
    takeNoB["Прийняти rejectReservation() запиту B"]
    endOk([Кінець: одне бронювання])
    endNo([Кінець: відмова, запису немає])
    endA([Кінець A: reservationId])
    endB([Кінець B: відмова])
  end

  subgraph serviceLane ["WishlistService"]
    takeReq["Прийняти reserveItem(itemId)"]
    takeReqB["Прийняти reserveItem(itemId), запит B"]
    read["Прочитати WishlistItem і Wishlist"]
    pub["isPublic()"]
    own{"isOwnedBy(currentUserId) = true?"}
    guard{"completed = true або existing існує?"}
    two{"два одночасні запити на цей itemId?"}
    createOne["createReservation(itemId, currentUserId)"]
    sendNo["Надіслати rejectReservation()"]
    sendId["Надіслати reservationId"]
    fork{{"fork"}}
    createA["createReservation(itemId, userA)"]
    createB["createReservation(itemId, userB)"]
    conflictB["conflict"]
    sendIdA["Надіслати reservationId запиту A"]
    sendNoB["Надіслати rejectReservation() запиту B"]
  end

  start --> sendA
  sendA -->|reserveItem| takeReq
  takeReq --> read --> own
  own -->|так| sendNo
  own -->|ні| pub
  pub --> guard
  guard -->|так| sendNo
  guard -->|ні| two
  sendNo -->|rejectReservation| takeNo --> endNo
  two -->|ні| createOne --> sendId
  sendId -->|reservationId| takeId --> endOk
  two -->|так| sendB
  sendB -->|reserveItem| takeReqB --> fork
  fork --> createA --> sendIdA
  fork --> createB --> conflictB --> sendNoB
  sendIdA -->|reservationId| takeIdA --> endA
  sendNoB -->|rejectReservation| takeNoB --> endB

```