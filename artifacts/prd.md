## Problem & Persona

Mid-sized SaaS companies face significant challenges managing their subscription renewal workflows. Customer Success and Renewal Managers currently rely on manual, fragmented processes—spreadsheets, generic project tools, and CRM reminders—that lead to missed renewals, revenue leakage, and inaccurate forecasting. These inefficiencies increase operational overhead and reduce customer lifetime value, threatening predictable revenue streams critical for business growth.

The target persona is a Customer Success Manager, Renewal Manager, or SaaS Operations Manager at a mid-sized SaaS company (50-500 employees), typically aged 25-45. They struggle with error-prone manual renewal tracking, lack consolidated renewal risk data and actionable playbooks, and find forecasting renewal revenue difficult due to scattered information. They seek a simple, affordable, self-serve tool that delivers immediate value without enterprise complexity or sales cycles, fitting within SMB SaaS budgets of $19-$49/month. They frequent SaaS and customer success subreddits, LinkedIn groups, Product Hunt, SaaS Slack communities, and Hacker News.

## Features

- **Renewal Opportunity Dashboard**: A centralized dashboard displaying all renewal opportunities with status, risk level, and next action.
  - *User Story*: As a Renewal Manager, I want to see all renewal opportunities in one place so that I can efficiently track and prioritize renewals.
  - *Acceptance Criteria*:
    - Dashboard lists all active renewal opportunities with key fields: customer name, renewal date, status, risk level, and next action.
    - Opportunities can be filtered and sorted by renewal date, risk, and status.
    - Dashboard updates in real-time as opportunities are added or modified.
    - Visual indicators highlight high-risk or overdue renewals.
  - *Complexity*: Medium

- **Renewal Risk & Playbook Notes**: Ability to add, view, and edit risk factors and renewal playbook steps per opportunity.
  - *User Story*: As a Customer Success Manager, I want to document risk factors and playbook actions for each renewal so that I can follow repeatable, actionable steps to improve renewal outcomes.
  - *Acceptance Criteria*:
    - Users can add multiple risk notes tagged by category (e.g., pricing, product usage, support issues).
    - Playbook steps can be created, edited, and marked as complete per opportunity.
    - Notes and playbook steps are visible on the opportunity detail page.
    - Risk level is summarized and reflected on the dashboard.
  - *Complexity*: Low

- **Automated Renewal Reminders & Notifications**: Configurable email alerts for upcoming renewals and changes in risk status.
  - *User Story*: As a Renewal Manager, I want to receive timely reminders about upcoming renewals and risk changes so that I never miss critical renewal deadlines.
  - *Acceptance Criteria*:
    - Users can set reminder schedules relative to renewal dates (e.g., 30, 14, 3 days before).
    - Email notifications are sent automatically via Resend.
    - Notifications include opportunity details and recommended next actions.
    - Users can enable/disable notifications per opportunity.
  - *Complexity*: Medium

- **User Authentication & Signup**: Secure user registration, login, and account management.
  - *User Story*: As a new user, I want to securely sign up and log in so that my renewal data is protected and personalized.
  - *Acceptance Criteria*:
    - Email/password signup with email verification.
    - Secure login with session management.
    - Password reset functionality.
    - User profile management (name, email).
  - *Complexity*: Low

- **Renewal Opportunity CRUD**: Create, read, update, and delete renewal opportunities with key details.
  - *User Story*: As a Renewal Manager, I want to manage renewal opportunities by adding new ones, updating details, and removing obsolete entries so that my renewal pipeline stays accurate.
  - *Acceptance Criteria*:
    - Form to create new renewal opportunity capturing: customer name, renewal date, contract value, status (e.g., pending, in negotiation, renewed, lost), and assigned user.
    - Ability to edit and delete existing opportunities.
    - Validation on required fields and date formats.
    - Changes reflected immediately on the dashboard.
  - *Complexity*: Low

## Dashboard Pages

- **Renewal Opportunity Dashboard**
  - *Route*: /dashboard/opportunities
  - *Description*: Displays a sortable and filterable list of all renewal opportunities with key status and risk indicators. Users can quickly identify high-risk renewals and next actions.
  - *Key Data Fields*:
    - Customer Name (string): identifies the account.
    - Renewal Date (date): upcoming renewal deadline.
    - Status (enum): pending, in negotiation, renewed, lost.
    - Risk Level (enum): low, medium, high.
    - Next Action (string): next step to take.
  - *Stats*:
    - Total active renewal opportunities.
    - Number of high-risk renewals.
    - Renewals due this month.
    - Renewal success rate (renewed vs. lost).

- **Opportunity Detail Page**
  - *Route*: /dashboard/opportunities/[id]
  - *Description*: Shows detailed information for a single renewal opportunity, including risk notes, playbook steps, and reminder settings.
  - *Key Data Fields*:
    - Risk Notes (text list with categories).
    - Playbook Steps (checklist with completion status).
    - Reminder Settings (dates and toggle).
    - Contract Value (currency).
    - Assigned User (user reference).
  - *Stats*:
    - Risk summary score.
    - Playbook completion percentage.

## Pricing

- **Starter**
  - Price: $19/month or $15.20/month billed annually (20% discount)
  - Features Included:
    - Up to 5 users
    - Up to 100 renewal opportunities per month
    - Renewal Opportunity Dashboard
    - Risk & Playbook Notes
    - Automated Renewal Reminders
    - Basic email support
  - Target Customer Segment: Small SaaS teams or solopreneurs testing renewal workflow automation.
  - Path to $1K MRR: 53 customers at $19/month = $1007 MRR

- **Growth**
  - Price: $39/month or $31.20/month billed annually
  - Features Included:
    - Up to 15 users
    - Up to 500 renewal opportunities per month
    - All Starter features
    - Priority support
    - Integrations (Slack notifications, enhanced email workflows)
  - Target Customer Segment: Growing SaaS teams needing scale and integrations.
  - Path to $1K MRR: 26 customers at $39/month = $1014 MRR

- **Pro**
  - Price: $49/month or $39.20/month billed annually
  - Features Included:
    - Unlimited users
    - Unlimited renewal opportunities
    - All Growth features
    - Advanced analytics dashboard
    - AI-powered renewal risk scoring and recommended next actions (post-MVP)
  - Target Customer Segment: Larger SMB SaaS teams requiring full automation and insights.
  - Path to $1K MRR: 21 customers at $49/month = $1029 MRR

## Success Metrics

- **Week 1**
  - Number of waitlist signups
  - Homepage and signup page views
  - Signup conversion rate (visitor to registered user)

- **Month 1**
  - Number of active users (logged in and created at least one renewal opportunity)
  - Activation rate (users who created first opportunity within 24 hours of signup)
  - Daily active users (DAU) / Monthly active users (MAU) ratio

- **Month 3**
  - Monthly Recurring Revenue (MRR)
  - Customer churn rate (monthly)
  - Net Promoter Score (NPS) from user surveys
  - Average renewal opportunities created per user
  - Email reminder open and click-through rates

## Out of Scope (MVP)

- AI-powered renewal risk prediction and recommended next best actions (planned post-MVP after sufficient data)
- Deep CRM integrations (e.g., Salesforce, HubSpot)
- Multi-channel notifications beyond email (e.g., SMS, push)
- Collaborative team features such as comments, mentions, and shared playbooks
- Advanced analytics beyond basic renewal success and risk summaries
- Mobile app or native client (web-only MVP)
- Marketplace or partner integrations
- Custom branding or white-labeling
- Bulk import/export of renewal opportunities (CSV upload planned post-MVP)
- Multi-language support

These features will be considered for future releases once the MVP achieves product-market fit and initial revenue targets.