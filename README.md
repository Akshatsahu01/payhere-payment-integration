# PayHere Payment Integration

A simple PayHere sandbox payment integration using a React frontend and an Express.js backend. The backend generates the PayHere payment hash and verifies server-to-server payment notifications.

## Project Structure

```text
.
├── backend/
│   ├── app.js
│   ├── package.json
│   └── routes/payment.js
└── frontend/
		├── public/index.html
		├── package.json
		└── src/
				├── App.js
				└── components/PaymentButton.js
```

## How It Works

1. The user clicks **PayHere Pay** in the React application.
2. The frontend sends the order amount and currency to `POST /payment/start`.
3. The Express backend generates the PayHere MD5 payment hash.
4. The frontend starts the PayHere sandbox checkout using the returned hash.
5. PayHere sends the payment result to `POST /payment/notify`.
6. The backend verifies the notification signature and accepts successful payments with status code `2`.

The PayHere JavaScript SDK is loaded in `frontend/public/index.html`.

## Requirements

- Node.js and npm
- A PayHere sandbox merchant account for testing
- A publicly reachable notification URL for PayHere callbacks

## Installation

Install dependencies in both applications:

```bash
cd backend
npm install

cd ../frontend
npm install
```

## Running Locally

Open two terminals from the repository root.

Start the backend:

```bash
cd backend
npm start
```

The backend runs on `http://localhost:5001` by default. Set the `PORT` environment variable to use another port.

Start the frontend:

```bash
cd frontend
npm start
```

The React application opens at `http://localhost:3000`.

## API Endpoints

### `POST /payment/start`

Generates a PayHere payment hash.

Example request:

```json
{
  "order_id": "ItemNo12345",
  "amount": "1005.00",
  "currency": "LKR"
}
```

Example response:

```json
{
  "hash": "UPPERCASE_MD5_HASH",
  "merchant_id": "12345684"
}
```

### `POST /payment/notify`

Receives PayHere URL-encoded payment notifications. The backend recomputes `md5sig` using the merchant secret and responds with:

- `200` when the signature is valid and `status_code` is `2`.
- `400` when verification fails or the payment is not successful.

PayHere must be able to reach this endpoint over the public internet. `localhost` cannot be used as the notification URL.

## Current Demo Configuration

The current frontend uses these values:

- PayHere sandbox mode: enabled
- Demo order: `ItemNo12345`
- Amount: `1005.00 LKR`
- Backend hash URL: `https://sea-lion-app-qfh5d.ondigitalocean.app/payment/start`
- Backend notification URL: `https://sea-lion-app-qfh5d.ondigitalocean.app/payment/notify`
- Local return URL: `http://localhost:3000/payment/success`
- Local cancel URL: `http://localhost:3000/payment/cancel`

Update `frontend/src/components/PaymentButton.js` when using a different backend or order.

## Security Notes

The current demo contains merchant credentials in `backend/routes/payment.js`. Before deploying or sharing this project:

- Rotate any exposed merchant credentials.
- Store `merchant_id` and `merchant_secret` in backend environment variables or a secret manager.
- Never send the merchant secret to the browser or commit it to source control.
- Validate order IDs, amounts, currencies, and customer data on the backend.
- Use the verified PayHere notification as the source of truth for fulfillment.
- Add persistent order storage and idempotent notification handling before production use.
- Use HTTPS and restrict CORS to the deployed frontend origin.

## Documentation

- [High-Level Design](HHL.md)
- [Low-Level Design](LLD.md)
- [Product Requirements Document](PRD.md)

## License

This project is provided for demonstration and educational purposes.
