# Part 2 demo video script (about 9 minutes)

Speak naturally and use this as a guide rather than reading it word for word. Times are approximate.

*Before recording:* trust the certificate so the address bar shows a padlock, start the backend and frontend, and use fresh demo accounts. Keep .env, the Atlas connection string and full tokens off screen.

Suggested demo data: freelancer freelancer.demo@example.com, client client.demo@example.com, gig "Logo design" at R19.99.

---

## 1. Introduction (0:00 – 0:45)

Screen: the marketplace home page.

"Hi, this is our INSY7314 Part 2 demonstration of HustleHub+, a freelance marketplace. Freelancers advertise services as gigs, and clients browse and book them. There are three roles: clients, freelancers and an administrator. In this video I'll show registration and login, creating a gig, booking it, the transaction record, the freelancer's income, and the security controls behind it. The team is Matthew Jackson, Nicholas Farquhar and Jaco Smit."

## 2. Architecture and contributions (0:45 – 2:00)

Screen: the architecture diagram from the README.

"The app uses the MERN stack. The React frontend talks to an Express API over HTTPS, and the API uses Mongoose to store data in MongoDB Atlas. The database credentials and JWT secret only exist on the backend, so the browser never sees them. Every request is validated on the server, because someone could skip the React form and call the API directly."

"In terms of contributions: Matthew built the backend structure, HTTPS, the database configuration, the User and Gig models, gig management and the admin setup. Nicholas built registration, authentication, validation, password hashing and the registration tests. Jaco built the React frontend, bookings, transactions and income, the extra security work, the API and frontend tests, the diagrams and the README."

## 3. Startup (2:00 – 2:30)

Screen: two terminals.

"The backend starts with npm run dev and runs on https://localhost:3000. The frontend starts with npm run dev in the frontend folder and runs on https://localhost:5173. Both use HTTPS with a local certificate, and the frontend proxies its API calls to the backend."

## 4. Authentication (2:30 – 3:45)

Screen: register a freelancer, then log in.

"I'll register a freelancer. Passwords need at least 12 characters with upper and lower case, a number and a symbol. They're hashed with bcrypt before they're saved, so no plain text password is stored. Emails are lowercased and must be unique."

Log in, then open the profile page.

"On login the server returns a signed JWT. I'm not showing the full token, but it carries the user's id, email and role and it expires. On every protected request the middleware checks the signature, the expiry and the claims, and then loads the current user from the database so the role is always up to date. Wrong email and wrong password give the same error message, so an attacker can't tell which one was wrong."

## 5. Gig creation (3:45 – 4:45)

Screen: create the "Logo design" gig, then view it and edit it.

"Only freelancers can create gigs. I'll add a title, description, category and a price of 19.99. Prices are stored as integer cents, so this is saved as 1999, which avoids rounding errors. Here it is in the marketplace, and I can edit it because I own it. A different freelancer couldn't edit or delete it; the API returns a 404 for gigs that aren't theirs. Gig text is also sanitised, so HTML or script tags are stripped."

## 6. Client booking (4:45 – 6:00)

Screen: log out, register and log in as a client, open the gig, confirm the booking.

"Now I'll log in as a client. Clients can browse gigs and book them, but the create and edit buttons aren't shown, and the API would reject them anyway. I'll open the gig and confirm the booking. Payment is simulated, and the page says so. The client only sends the gig id. The price and the freelancer come from the saved gig on the server, so a user can't change them."

Show the booking confirmation, then the My bookings page.

"The booking and a transaction are written together in a MongoDB transaction, so either both are saved or neither is. A retry key means that pressing confirm twice can't create a duplicate booking. Here are the saved booking and its transaction in the client's history."

## 7. Freelancer bookings and income (6:00 – 6:45)

Screen: log in as the freelancer, open received bookings and income.

"Back as the freelancer, the booking now appears under received bookings with its transaction, and the income page shows R19.99 for one booking. Income is the sum of the confirmed transactions for this freelancer, so it's attributed to the right person. Another freelancer would see their own separate total."

## 8. Security controls (6:45 – 8:15)

Screen: Postman, with a few quick requests.

"Here are some of the security checks in Postman. A request with no token, or an altered token, returns 401. A client trying to create a gig gets 403. Invalid input, such as a bad email or a price that isn't a number, returns a controlled 400 with no stack trace. A request that tries to set its own price or owner is rejected."

Show the 429 response and the response headers.

"Registration, login and booking are rate limited to 30 requests per 15 minutes per IP address, and going over returns a 429 with retry information. Helmet adds security headers, and we set a Content Security Policy that restricts scripts, frames and objects. The API's policy is default-src 'none' because it only returns JSON."

## 9. Testing and database evidence (8:15 – 9:15)

Screen: Newman report, frontend test output, then the Atlas collection.

"For testing, our Postman collection is run with Newman: 98 requests and 350 assertions with no failures, covering success cases, invalid input, JWT problems, roles, bookings, transactions and income. There are also backend tests and frontend tests, which check component rendering and interactions like booking confirmation and role restrictions. In total 59 unique automated tests pass."

Show the Atlas transactions collection with credentials hidden.

"And here in MongoDB Atlas is the transaction record for the booking, linked to the client, the freelancer and the booking, with the amount in cents."

## 10. Wrap-up (9:15 – 9:30)

"That covers the full flow: authentication, gig creation, booking, the transaction record and the freelancer's income, with role-based access control, validation, rate limiting and security headers applied throughout. Thanks for watching."

---

## Checklist before uploading

- Address bar shows a padlock (certificate trusted)
- No .env, connection string, full JWT, private key or password hash visible
- Total length under 10 minutes
- Upload as unlisted or public, then paste the link into the README at the <!-- Part 2 video link goes here --> comment
