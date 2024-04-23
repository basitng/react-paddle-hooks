# usePaddle Hook

The React paddle hooks is a custom React hook that provides integration with the Paddle payment platform. It allows you to easily integrate majority of paddle api.

## Installation

Paddle installation

```bash
npm i react-paddle-hooks
```

Before using the usePaddle hook, make sure you have installed the required dependencies:

```bash
npm install @paddle/paddle-js react
```

### Usage

To use the usePaddle hook in your React component, import it and call it with the desired options:

```javascript
import { usePaddle } from "react-paddle-hooks";

const MyApp = () => {
  const { paddle, openCheckout, getPrices, productPrices, billingCycle } =
    usePaddle({
      environment: "sandbox",
      token: "your_paddle_token",
    });

  return <div>{/* Your component JSX */}</div>;
};
```

## Options

The `usePaddle` hook takes an object with the following options:

- **environment** (required): Specifies the Paddle environment to use. Choose between "sandbox" or "production".
- **token** (required): Your Paddle token for authentication.
- **eventCallback** (optional): A callback function triggered by Paddle events, receiving event data as a parameter.
- **checkoutSettings** (optional): Additional settings for checkout, like the success URL.

## Return Values

The `usePaddle` hook returns an object with:

- **paddle**: Initialized Paddle instance.
- **openCheckout**: Function to open Paddle checkout with checkout options.
- **getPrices**: Function to get prices of selected products based on the chosen billing cycle.
- **productPrices**: Object with product prices, using product IDs as keys and prices as values.
- **billingCycle**: Current billing cycle, either "month" or "year".

## Checkout Options

The `openCheckout` function accepts an object with:

- **items** (required): Array of item objects with a `priceId` and optional `quantity`.
- **customer** (optional): Customer details object including email and address.
- **settings** (optional): Additional checkout settings like display mode, theme, and locale.

## Price Preview

The `getPrices` function fetches prices for selected products based on billing cycle. It takes an array of `ProductDetails` objects and the billing cycle ("month" or "year"). `ProductDetails` has:

- **productId**: Product ID.
- **monthlyPriceId**: Price ID for monthly billing.
- **yearlyPriceId**: Price ID for yearly billing.

Prices are stored in `productPrices` with product IDs as keys and their prices as values.

## Handling Subscription

```javascript
import { usePaddle } from "react-paddle-hooks";

const SubscriptionComponent = () => {
  const { paddle, openCheckout, getPrices, productPrices, billingCycle } =
    usePaddle({
      environment: "sandbox",
      token: "your_paddle_api_token",
      eventCallback: (data) => {
        // Handle Paddle events
        console.log("Paddle event:", data);
      },
      checkoutSettings: {
        successUrl: "/success",
      },
    });

  const productDetails = [
    {
      productId: "product_1",
      monthlyPriceId: "price_monthly_1",
      yearlyPriceId: "price_yearly_1",
    },
    {
      productId: "product_2",
      monthlyPriceId: "price_monthly_2",
      yearlyPriceId: "price_yearly_2",
    },
  ];

  const handleSubscribe = () => {
    const checkoutOptions = {
      items: [
        {
          priceId:
            billingCycle === "month"
              ? productDetails[0].monthlyPriceId
              : productDetails[0].yearlyPriceId,
          quantity: 1,
        },
      ],
      customer: {
        email: "customer@example.com",
      },
      settings: {
        displayMode: "overlay",
        theme: "light",
        locale: "en",
      },
    };

    openCheckout(checkoutOptions);
  };

  return (
    <div>
      <h2>Subscription Plans</h2>
      <div>
        <label>
          <input
            type="radio"
            value="month"
            checked={billingCycle === "month"}
            onChange={() => getPrices(productDetails, "month")}
          />
          Monthly
        </label>
        <label>
          <input
            type="radio"
            value="year"
            checked={billingCycle === "year"}
            onChange={() => getPrices(productDetails, "year")}
          />
          Yearly
        </label>
      </div>
      <ul>
        {productDetails.map((product) => (
          <li key={product.productId}>
            {product.productId}:{" "}
            {productPrices[product.productId] || "Loading..."}
          </li>
        ))}
      </ul>
      <button onClick={handleSubscribe}>Subscribe</button>
    </div>
  );
};

export default SubscriptionComponent;
```

## SubscriptionComponent Explanation

### Importing the Hook

```javascript
import { usePaddle } from "react-paddle-hooks";
```

We're importing the usePaddle hook from the react-paddle-hooks package.

### Initializing the Hook

```javascript
const { paddle, openCheckout, getPrices, productPrices, billingCycle } =
  usePaddle({
    environment: "sandbox",
    token: "your_paddle_api_token",
    eventCallback: (data) => {
      // Handle Paddle events
      console.log("Paddle event:", data);
    },
    checkoutSettings: {
      successUrl: "/success",
    },
  });
```

1. usePaddle: Initializes the Paddle hook with specified options.
2. paddle: Initialized Paddle instance.
3. openCheckout: Function to open Paddle checkout.
4. getPrices: Function to fetch prices based on billing cycle.
5. productPrices: Object storing product prices.
6. billingCycle: Current billing cycle ("month" or "year").

### Product Details

```javascript
const productDetails = [
  {
    productId: "product_1",
    monthlyPriceId: "price_monthly_1",
    yearlyPriceId: "price_yearly_1",
  },
  {
    productId: "product_2",
    monthlyPriceId: "price_monthly_2",
    yearlyPriceId: "price_yearly_2",
  },
];
```

Array containing details of products and their price IDs for monthly and yearly billing.

### Subscription Handling

```javascript
const handleSubscribe = () => {
  const checkoutOptions = {
    items: [
      {
        priceId:
          billingCycle === "month"
            ? productDetails[0].monthlyPriceId
            : productDetails[0].yearlyPriceId,
        quantity: 1,
      },
    ],
    customer: {
      email: "customer@example.com",
    },
    settings: {
      displayMode: "overlay",
      theme: "light",
      locale: "en",
    },
  };

  openCheckout(checkoutOptions);
};
```

`handleSubscribe`: Function to handle subscription. It sets checkout options based on the selected billing cycle and opens the checkout.

## Error Handling

Errors during Paddle initialization or when calling `openCheckout` or `getPrices` are logged to the console.

## License

This code is under the MIT License.
