<h1>Bulk Adding Subscriptions</h1>

To subscribe to multiple addresses at once, you can use an array of addresses and iterate through them. This allows you to create subscriptions for multiple addresses efficiently.

<h3>SDK</h3>

```ts
const addresses = [
  '0xF64E82131BE01618487Da5142fc9d289cbb60E9d',
  '0x123456789abcdef0123456789abcdef012345678',
  '0xabcdef0123456789abcdef0123456789abcdef01',
  // Add more addresses as needed
];

const webhookUrl = 'https://<YOUR_WEBHOOK_URL>';

const subscriptions = await Promise.all(addresses.map(address => 
  tatum.notification.subscribe.addressEvent({
    address: address,
    url: webhookUrl
  })
));
```
<h3>Curl</h3>

```ts
import axios from 'axios';

const apiUrl = 'https://api.tatum.io/v4/subscription?type=mainnet';

const headers = {
  'Content-Type': 'application/json',
  'x-api-key': '<YOUR_API_KEY>'
};

const addresses = [
  '0xF64E82131BE01618487Da5142fc9d289cbb60E9d',
  '0x123456789abcdef0123456789abcdef012345678',
  '0xabcdef0123456789abcdef0123456789abcdef01'
];

const webhookUrl = 'https://<YOUR_WEBHOOK_URL>';

const subscriptionPromises = addresses.map(address => 
  axios.post(apiUrl, {
    type: 'ADDRESS_EVENT',
    attr: {
      address: address,
      chain: 'ETH',
      url: webhookUrl
    }
  }, { headers })
);

const responses = await Promise.all(subscriptionPromises);
```

<h1>Bulk Removal of Subscriptions</h1>

To unsubscribe from all existing subscriptions, you can first retrieve all subscriptions and then unsubscribe from each one. Because each request to get existing subscriptions returns a finite number of subscriptions, we can use an iterative approach to retrieve all.

<h3>SDK</h3>

```ts
async function getAllSubscriptions() {
  const tatum = await TatumSDK.init<Ethereum>({ network: Network.ETHEREUM });

  let allSubscriptions = [];
  let offset = 0;
  const pageSize = 10;
  let hasMore = true;

  while (hasMore) {
    const response = await tatum.notification.getAll({
      pageSize,
      offset,
      type: 'mainnet'
    });

    allSubscriptions = allSubscriptions.concat(response.data);

    if (response.data.length < pageSize) {
      hasMore = false;
    } else {
      offset += pageSize;
    }
  }
  return allSubscriptions;
}

// Get all existing subscriptions
const subscriptions = await getAllSubscriptions();

// Unsubscribe from all subscriptions
await Promise.all(allSubscriptions.data.map(subscription => 
  tatum.notification.unsubscribe(subscription.id)
));
```

<h3>Curl</h3>

```ts
import axios from 'axios';

const apiUrl = 'https://api.tatum.io/v4/subscription';
const headers = {
  'Content-Type': 'application/json',
  'x-api-key': '<YOUR_API_KEY>'
};

async function getAllSubscriptions() {
  let allSubscriptions: NotificationSubscription[] = [];
  let offset = 0;
  const pageSize = 10;
  let hasMore = true;

  while (hasMore) {
    const response = await axios.get(apiUrl, {
      headers,
      params: {
        pageSize,
        offset,
        type: 'mainnet'
      }
    });

    const subscriptions: NotificationSubscription[] = response.data;
    allSubscriptions = allSubscriptions.concat(subscriptions);

    if (subscriptions.length < pageSize) {
      hasMore = false;
    } else {
      offset += pageSize;
    }
  }

  return allSubscriptions;
}

// First, get all subscriptions
const subscriptions = await getAllSubscriptions();

// Then, delete each subscription
const deletePromises = subscriptions.map(subscription => 
  axios.delete(`${apiUrl}/${subscription.id}`, { headers })
);

await Promise.all(deletePromises);
```