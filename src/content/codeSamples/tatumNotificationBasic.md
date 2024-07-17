---
---
```ts
import {AddressBasedNotification} from '@tatumio/tatum'

const subscription: ResponseDto<AddressBasedNotification> = await tatum.notification.subscribe.addressEvent({
  address: '0xF64E82131BE01618487Da5142fc9d289cbb60E9d', // replace with your address
  url: 'https://<YOUR_WEBHOOK_URL>' // replate with your URL handler
}) // Create
```
```ts
await tatum.notification.unsubscribe(subscription.data.id) // Delete
```