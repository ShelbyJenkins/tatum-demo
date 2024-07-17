---
---
```ts
// Bulk add subscriptions iteratively
const addresses = [
  '0xF64E82131BE01618487Da5142fc9d289cbb60E9d',
  '0x123456789abcdef0123456789abcdef012345678',
  // ... add more addresses as needed
];
for (const address of addresses) {
  const subscription: ResponseDto<AddressBasedNotification> = await tatum.notification.subscribe.addressEvent({
  address: address,
  url: 'https://<YOUR_WEBHOOK_URL>' // replate with your URL handler
  });
}
```
```ts
// Bulk remove subscriptions iteratively
const allSubscriptions = await tatum.notification.getAll();
for (const subscription of allSubscriptions.data) {
  await tatum.notification.unsubscribe(subscription.id);
};
```