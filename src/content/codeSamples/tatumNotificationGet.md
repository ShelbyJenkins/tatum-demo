---
---
```ts
import {NotificationSubscription, ResponseDto} from '@tatumio/tatum'
const {status, data}: ResponseDto<NotificationSubscription[]> = await tatum.notification.getAll()
```
