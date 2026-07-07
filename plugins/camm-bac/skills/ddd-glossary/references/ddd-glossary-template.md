# DDD Glossary

## Project

- **Domain:**
- **Bounded Context:**
- **Version:**
- **Last Updated:**

## Terms

### Order

- **Definition:** A formal request made by a customer to purchase one or more products or services.
- **Alias to avoid:** : Purchase Order
- **Related Terms:** Cart, Quote, Payment, Invoice
- **Business Rule:** An order must be confirmed before shipment.
- **Example:** "Order has been confirmed."
- **Bounded Context:** Sales

### Customer

- **Definition:** A person or organization that purchases products or services from the company.
- **Alias to avoid:** : Client, Buyer
- **Related Terms:** Order, Account, Contact
- **Business Rule:** A customer can place multiple orders.
- **Example:** "The customer created a new order."
- **Bounded Context:** Sales

## Relationships

- An **Invoice** belongs to exactly one **Customer**.
- An **Order** produces one or more **Invoices**.

## Flagged ambiguities

- "account" is being used to mean both **Customer** and **User**. Prefer **Customer** for the business entity and **User** for authentication.

## Change Log

- [current date] Initial version created.
