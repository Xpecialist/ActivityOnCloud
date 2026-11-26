# Activity on Cloud

A full-stack booking engine for activity providers (watersports in the demo data: surfing, kayaking, jet skiing, paddleboarding and more). Customers search an activity by date and group size, see the available time slots with live remaining capacity and price, and book. Providers log in to a dashboard where they manage their activity options, schedules, holidays, seasonal rates and incoming bookings.

Built by a five-person team during the Full Stack Developer internship at **Knowledge A.E.** (Patras, April – May 2024), working in an Agile/Scrum setup with feature branches and pull requests.

## Features

**Customer booking flow**
- Pick an activity, a date and the number of people
- See every option of that activity with its time slots for the day, the price for that date and how many places are left in each slot
- Enter personal details and confirm; a booking reference (UUID) is generated and a confirmation page is shown
- Days marked as holidays by the provider show no availability

**Provider (admin) area**
- JWT login and sign-up
- Dashboard with the provider's bookings
- Create activity options with name, description, duration, capacity, price per person and an uploaded image
- Set weekly availability (day, start and end time), holidays and seasonal rates

**How availability is calculated**

For a chosen date the backend takes each option's opening hours for that weekday and splits them into slots of the option's duration. For each slot it subtracts the people already booked from the option's capacity, and it applies any seasonal rate that covers the date to the base price. If the date is a holiday for the activity, no slots are returned.

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Java 17, Spring Boot 3.2, Spring Web, Spring Data JPA, Spring Security with JWT (jjwt), Jakarta Validation, ModelMapper, Lombok |
| Database | PostgreSQL |
| Frontend | React 18, TypeScript, Tailwind CSS, Flowbite, React Router, Axios, Formik + Yup, Webpack |
| Cross-cutting | AOP logging aspect, global exception handler, Logback rolling logs |

## Project structure

```
backend/                      Spring Boot API (gr.knowledge.internship.activityoncloud)
  controller/                 REST controllers
  service/                    business logic (availability, booking, rates, JWT, ...)
  entity/  dto/  mapper/      JPA entities, DTOs and mappers
  repository/                 Spring Data repositories
  configs/                    security configuration and JWT filter
  aop/logging/                logging aspect
frontend/                     React + TypeScript client
  src/Pages/                  Login, Dashboard, BookingEngine, ActivityCreation, PersonalInfoForm
  src/Components/             date picker, person picker, time-slot carousel, tables, ...
  public/Photos/              demo activity images
```

## Data model

- **ActivityAdmin**: the provider, linked to a **User** account
- **Activity**: belongs to a provider (for example "Surfing")
- **ActivityOption**: a bookable variant of an activity, with duration, capacity, price per person and an image
- **Availability**: the opening hours of an option on a given weekday
- **Rate**: a price multiplier for an option between two dates (seasonal pricing)
- **Holiday**: a date on which an option or provider is closed
- **Booking**: customer name, contact details, option, start time, number of people and total price

## API overview

Public endpoints: `POST /auth/signup`, `POST /auth/login`, `GET /activity-option/of-admin`, `GET /availability/available/{activityId}`, `POST /booking/save`. Every other endpoint needs a `Bearer` token.

| Resource | Base path |
|---|---|
| Authentication | `/auth` |
| Activities | `/activity` |
| Activity options | `/activity-option` |
| Availability and time slots | `/availability` |
| Bookings | `/booking` |
| Holidays | `/holiday` |
| Seasonal rates | `/rate` |
| Providers | `/activity-admin` |
| Image upload | `/image` |

## Running it locally

**Requirements:** JDK 17, Maven, PostgreSQL, Node.js.

1. Create a PostgreSQL database, for example `activityoncloud`.
2. Create `backend/src/main/resources/application.properties` (it is not committed):

   ```properties
   server.port=8081
   spring.datasource.url=jdbc:postgresql://localhost:5432/activityoncloud
   spring.datasource.username=<db user>
   spring.datasource.password=<db password>
   spring.jpa.hibernate.ddl-auto=update

   security.jwt.secret-key=<base64-encoded secret, at least 256 bits>
   security.jwt.expiration-time=3600000
   ```

3. Start the backend:

   ```
   cd backend
   mvn spring-boot:run
   ```

4. Point the frontend at your backend: in `frontend/src/AxiosConfig.tsx`, set `baseURL` to `http://localhost:8081` (it is currently set to a machine on the original office network).
5. Start the frontend:

   ```
   cd frontend
   npm install
   npm start
   ```

   Then open http://localhost:3000.

## Team

Built by Manos Loukakis ([Xpecialist](https://github.com/Xpecialist)) together with [GeorgeZarokanellos](https://github.com/GeorgeZarokanellos), [Dionusia](https://github.com/Dionusia), [ptriantafy](https://github.com/ptriantafy) and [m1kemp](https://github.com/m1kemp).

My part was mostly in the customer booking flow and the provider dashboard: the time-slot carousel, the booking request and the confirmation page, the capacity check, image upload, the dashboard bookings table and realistic demo data.
