# Development Journey

I wanted this project to be more than a collection of booking pages. As the application grew, I ended up dealing with authentication, different user roles, database rules, transactions, refunds and real-time updates.

I didn't try to hide that complexity behind the UI. A big part of the project was figuring out which responsibilities should live in React and which ones should be handled by the backend and database.

## From UI to a full application

The frontend is built with React and Vite, with React Router handling the application routes.

The application currently has separate flows for guests and administrative users. There is also a Hotel Manager role in the protected routes, so access to the dashboards is not handled as a simple "logged in / logged out" check.

The main user-facing flow covers browsing accommodations, viewing their details, authentication and making a reservation.

From there, the project became less about individual pages and more about how the different parts of the system interact.

## Authentication and protected routes

Authentication is handled through Supabase Auth.

There are separate flows for registration, email confirmation, login, password recovery and password reset.

On the frontend, protected routes are handled through a reusable ProtectedRoute component. The route configuration defines which roles can access which parts of the application.

I preferred keeping the role checks close to the routing layer rather than duplicating them inside every dashboard component.

## Designing the database around the application

The backend is based on Supabase and PostgreSQL.

The database isn't only used as a place to store form data. The application has relationships between users, accommodations, reservations and transactions, so the database structure has to reflect those relationships.

Foreign keys and cascading behaviour are used where appropriate.

This also means that some application rules are enforced at the database level instead of relying entirely on the React application to behave correctly.

## Row Level Security

One of the more important backend parts of the project is PostgreSQL Row Level Security.

The reason for using RLS was straightforward: hiding something in the UI isn't the same as preventing access to it.

The application therefore uses database policies to control which records a user can access or modify.

This was particularly important for user-specific data such as reservations and transactions, where access should depend on the authenticated user and their role.

## Keeping authentication data in sync

Supabase keeps authentication records in auth.users, while the application also needs its own public user profile data.

For this, the project uses a PostgreSQL function and trigger to create the corresponding record in the public users table when a new user registers.

I liked this approach because the consistency rule lives close to the data itself rather than depending on every frontend registration flow to remember to perform a second operation.

## Reservations and transactions

The booking flow is connected to a transaction system rather than treating a reservation as an isolated record.

The admin dashboard provides transaction management, filtering and export functionality, together with revenue-related charts.

This part of the application also introduced different states that have to remain consistent with each other.

## Refund workflow

Refunds are handled as a separate workflow.

A guest can request a refund, which creates a pending transaction state. The request can then be reviewed by an administrator.

When the request is approved, the related transaction and reservation status are updated accordingly.

I wanted this to be an explicit workflow rather than letting a user directly change the state of a completed transaction.

## Real-time updates

Some parts of the application need to reflect changes without a manual page refresh.

Supabase Realtime subscriptions are used for new reservations and relevant status changes.

This is especially useful on the administration side, where new activity should become visible while the dashboard is open.

## Managing server state

As the amount of data handled by the frontend increased, I used TanStack React Query for server state, caching and mutations.

This keeps server data separate from normal component/UI state and gives the application a consistent way to handle fetching and mutations.

The project also uses optimistic updates in parts of the UI where an immediate response makes sense.

## Admin side

The admin dashboard has grown alongside the rest of the application.

It includes transaction management, filtering and export, revenue analytics, reservation-related management and a private message inbox.

There is also a contact/messaging flow where users can send private messages to the administration.

So the admin area isn't just another set of CRUD screens; it is where several of the application's workflows come together.

## What I took away from the project

The most useful part of building this project was having to think beyond the component level.

A booking application looks fairly simple from the outside, but once authentication, roles, transactions, refunds and database permissions are involved, the boundaries between frontend, backend and database become important.

Working on HotelYar gave me practical experience with those boundaries:

- React application architecture
- protected and role-based routes
- Supabase Auth
- PostgreSQL relational data
- Row Level Security
- database functions and triggers
- server-state management with React Query
- transactions and state-based workflows
- Supabase Realtime
- admin-oriented data management

There are still areas I would improve if I continued developing the project. That's also part of why I keep the project public: the current code represents an actual development process rather than an attempt to present a perfectly designed system that was never changed along the way.
