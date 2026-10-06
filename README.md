<div align="center">
<img width="1200" height="475" alt="GHBanner" src="https://ai.google.dev/static/site-assets/images/share-ais-513315318.png" />
</div>

# ARPE Store

React/Vite storefront and Express API. MySQL is authoritative; Razorpay handles payments, Shiprocket handles fulfillment, and Google Sheets is a reporting ledger.

## Run Locally

**Prerequisites:** Node.js and a configured MySQL database.

1. Install dependencies with `npm install`.
2. Create the MySQL database and a dedicated application user. For a local MySQL server, connect as a MySQL administrator and run the following after replacing the sample password with a strong password:

   ```sql
   CREATE DATABASE IF NOT EXISTS maison_verde
     CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   CREATE USER IF NOT EXISTS 'arpe_app'@'localhost'
     IDENTIFIED BY 'replace_with_a_strong_local_password';
   ALTER USER 'arpe_app'@'localhost'
     IDENTIFIED BY 'replace_with_a_strong_local_password';
   GRANT ALL PRIVILEGES ON maison_verde.* TO 'arpe_app'@'localhost';
   ```

3. Copy `.env.example` to `.env` and set `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, and `DB_NAME` to match the database and user you created. Do not use the example password or a MySQL administrator account for the application. Also configure the Razorpay values.
4. To enable Shiprocket, set `SHIPROCKET_EMAIL`, `SHIPROCKET_PASSWORD`, `SHIPROCKET_PICKUP_LOCATION`, and `SHIPROCKET_PICKUP_POSTCODE`. These variables are server-only and must not use a `VITE_` prefix.
5. Configure `GOOGLE_MAPS_DISTANCE_MATRIX_API_KEY` for verified road distance and package dimensions either on each product or with `SHIPROCKET_PACKAGE_LENGTH`, `SHIPROCKET_PACKAGE_BREADTH`, and `SHIPROCKET_PACKAGE_HEIGHT`.
6. Run the app with `npm run dev`.

Checkout is unavailable when MySQL, distance configuration, or Shiprocket serviceability cannot be verified. Payment is marked paid only after backend Razorpay signature and captured-payment verification. Shiprocket orders are created from stored MySQL order snapshots after payment verification; failures remain retryable from the protected admin Shipments page.

The development server may use its in-memory fallback for storefront data when MySQL is unavailable; admin authentication always requires MySQL and never accepts in-memory credentials. Production startup requires a successful MySQL connection and does not serve API requests from in-memory data. Check `/api/health` after startup to confirm the active database.

The Express process is intended to run behind infrastructure-level load balancing. Do not add load-balancer behavior to React or the admin dashboard. Configure shared production dependencies (including session persistence) before running multiple app instances.
