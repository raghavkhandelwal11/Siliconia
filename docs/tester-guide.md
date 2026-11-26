# Siliconics prototype: tester guide

Siliconics is an electronics marketplace for India. This prototype runs in **TEST MODE**: payments,
shipping, bank payouts, KYC checks and SMS/WhatsApp messages are simulated, so **no real money moves
and no real messages are sent**. A red banner at the top of every page reminds you.

## 1. Getting in

- Open the prototype address you were given (for example `https://<name>.duckdns.org`).
- Sign-in uses your mobile number and a 6-digit code. Use any 10-digit number starting with 6–9;
  you do not need to own it.
- The code arrives in the **test inbox**: the "Open the test inbox" link in the banner. Ask the
  Siliconics team for the inbox password. Each number can ask for 3 codes per 15 minutes.
- One number can hold one account per role, so the same number can be a buyer and a seller.

| Role | How to get an account |
|---|---|
| Buyer | Choose **Buyer** on the sign-in page with any new number. |
| Seller | Choose **Seller** with a new number and complete onboarding; an admin approves it. To test selling straight away, sign in as the demo seller **9999900099** (Siliconics Demo Store). |
| Creator | Choose **Creator**, fill in the application (5,000+ followers), submit; an admin approves it. |
| Promoter | Choose **Promoter**, add a display name, submit; an admin approves it. |
| Admin | Created by the team only, with an authenticator app. Ask the team if you need one. |

## 2. Test values

| What | Use | Result |
|---|---|---|
| UPI ID | `success@sim` | Payment succeeds |
| UPI ID | `failure@sim` | Payment fails |
| UPI ID | `pending@sim` | Stays pending about a minute, then succeeds |
| UPI ID | `timeout@sim` | Never completes (the order expires after 10 minutes) |
| Card | `4111 1111 1111 1111` | Succeeds |
| Card | `4000 0000 0000 0002` | Declined |
| Card | `4000 0000 0000 9995` | Never completes |
| Net banking | SIM Bank (succeeds / declines / slow) | As named |
| Delivery PIN | `999999` | Delivery fails twice, parcel returns to the seller |
| Delivery PIN | `999998` | One failed attempt, then delivered |
| Delivery PIN | `999997` | Nobody delivers there |
| Delivery PIN | `999996` | Delivers, but no cash on delivery |
| Any other valid PIN | e.g. `560001` | Delivered normally |
| Creator or seller bank account | `000000000000` | Bank transfer fails (Electrons or payout return automatically) |

Parcels move one tracking step every few minutes, so a shipped order reaches "Delivered" in under
half an hour. The return window then lasts 7 days.

## 3. What to try

### Buyer
1. Search for "power bank", "tv" or "phone"; filter and sort the results.
2. Open a product, check delivery to your PIN, add to cart, add to wishlist.
3. Check out: add an address, pay by UPI or card, and see the order confirmed.
4. Follow the order to "Delivered", download the invoice, write a review.
5. Request a return, then watch the refund arrive.
6. Join **Electra membership** (₹650 a year, test payment). Members earn more Electrons, can pay
   cash on delivery and can buy launch deals.
7. Open **Electrons**: pending rewards become available once the return window closes.
8. On a launch day, join the waiting room and buy a deal unit (members only).
9. Raise an issue on an order and chat with support.

### Seller (demo seller 9999900099)
1. Open **Orders**: pack an order, book the courier, print the label and invoice.
2. Add a product with photos and variants, and publish it.
3. Approve or reject a return; see payouts and download a statement.
4. Under **Creators and promoters**: approve demo-unit requests and confirm promoter investments.

### Creator
1. Apply, then (after approval) verify PAN and bank details.
2. Request a demo unit, post a creator review with a video link after it arrives.
3. See earnings and cash out Electrons (minimum 250).

### Promoter
1. Apply, then add an investment in a seller and promote products.
2. See rewards from sales of promoted products.

### Admin
1. Approve sellers, creators and promoters; moderate reported reviews.
2. Create and schedule a launch deal.
3. Run a weekly payout batch (approve, then release).
4. Change settings, publish a new version of the terms (everyone is asked to accept again).
5. Request an Electrons adjustment and have a **second** admin apply it.
6. Watch the health page and the audit log.

## 4. Reporting a problem

Please send:
- what you did, step by step, and what you expected;
- what happened instead (a screenshot helps);
- the page address, the time, and the role and phone number you used;
- the order or return number if there is one (for example `SX-261126-ABCDE`).

Report problems to the Siliconics team. Data in the prototype may be reset between test rounds.
