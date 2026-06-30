# Engineering Design Doc

## Database Schema

```sql
-- ENGINEERING_DOC_SCHEMA_START

CREATE TABLE PREFIX_users (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  email text UNIQUE NOT NULL,
  hashed_password text,
  full_name text,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE PREFIX_renewal_opportunities (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_name text NOT NULL,
  renewal_date date NOT NULL,
  contract_value numeric(12,2) NOT NULL,
  status text NOT NULL CHECK (status IN ('pending', 'in negotiation', 'renewed', 'lost')),
  risk_level text NOT NULL CHECK (risk_level IN ('low', 'medium', 'high')),
  next_action text,
  assigned_user_id uuid NOT NULL REFERENCES PREFIX_users(id) ON DELETE CASCADE,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE PREFIX_risk_notes (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  opportunity_id uuid NOT NULL REFERENCES PREFIX_renewal_opportunities(id) ON DELETE CASCADE,
  category text NOT NULL CHECK (category IN ('pricing', 'product usage', 'support issues', 'other')),
  note text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE PREFIX_playbook_steps (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  opportunity_id uuid NOT NULL REFERENCES PREFIX_renewal_opportunities(id) ON DELETE CASCADE,
  step_text text NOT NULL,
  is_complete boolean NOT NULL DEFAULT false,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE PREFIX_reminder_settings (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  opportunity_id uuid NOT NULL REFERENCES PREFIX_renewal_opportunities(id) ON DELETE CASCADE,
  days_before_30 boolean NOT NULL DEFAULT false,
  days_before_14 boolean NOT NULL DEFAULT false,
  days_before_3 boolean NOT NULL DEFAULT false,
  enabled boolean NOT NULL DEFAULT true,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

-- ENGINEERING_DOC_SCHEMA_END
```

## Page Specs

#### PAGE: RenewalOpportunityDashboard
- **Route**: /dashboard/opportunities
- **Table**: PREFIX_renewal_opportunities
- **Fields**:

| Field          | Type     | Description                          |
|----------------|----------|------------------------------------|
| customer_name  | string   | Customer account name               |
| renewal_date   | date     | Upcoming renewal deadline           |
| status        | enum     | Renewal status (pending, in negotiation, renewed, lost) |
| risk_level    | enum     | Renewal risk level (low, medium, high) |
| next_action   | string   | Next step to take                   |
| assigned_user_id | uuid   | User assigned to this opportunity  |

- **Stats Cards**:
  - Total active renewal opportunities
  - Number of high-risk renewals
  - Renewals due this month
  - Renewal success rate (renewed vs. lost)
- **CRUD**: Create, Read, Update, Delete renewal opportunities
- **Icon**: LayoutDashboard

#### PAGE: OpportunityDetail
- **Route**: /dashboard/opportunities/[id]
- **Table**: PREFIX_renewal_opportunities (with related tables PREFIX_risk_notes, PREFIX_playbook_steps, PREFIX_reminder_settings)
- **Fields**:

| Field           | Type     | Description                          |
|-----------------|----------|------------------------------------|
| customer_name   | string   | Customer account name               |
| renewal_date    | date     | Upcoming renewal deadline           |
| contract_value  | numeric  | Contract value                     |
| status         | enum     | Renewal status                     |
| risk_level     | enum     | Renewal risk level                 |
| next_action    | string   | Next step to take                  |
| assigned_user_id | uuid    | User assigned                     |
| risk_notes     | list     | List of risk notes with categories |
| playbook_steps | list     | Checklist of playbook steps with completion status |
| reminder_settings | object | Reminder toggles and schedules     |

- **Stats Cards**:
  - Risk summary score (aggregated from risk notes)
  - Playbook completion percentage
- **CRUD**: Read, Update risk notes, playbook steps, reminder settings, and opportunity details
- **Icon**: FileText

#### PAGE: Signup
- **Route**: /signup
- **Table**: PREFIX_users
- **Fields**:

| Field          | Type     | Description                          |
|----------------|----------|------------------------------------|
| email          | string   | User email (unique)                 |
| password       | string   | User password (hashed)              |
| full_name      | string   | User full name                     |

- **CRUD**: Create user account
- **Icon**: UserPlus

#### PAGE: Login
- **Route**: /login
- **Table**: PREFIX_users
- **Fields**:

| Field          | Type     | Description                          |
|----------------|----------|------------------------------------|
| email          | string   | User email                         |
| password       | string   | User password                     |

- **CRUD**: Authenticate user session
- **Icon**: LogIn

#### PAGE: UserProfile
- **Route**: /dashboard/settings/profile
- **Table**: PREFIX_users
- **Fields**:

| Field          | Type     | Description                          |
|----------------|----------|------------------------------------|
| full_name      | string   | User full name                     |
| email          | string   | User email (read-only or editable) |

- **CRUD**: Read, Update user profile
- **Icon**: Settings

## Authentication & Authorization

- Use Supabase Auth with email/password signup, magic link login, and optional OAuth.
- Email verification required on signup.
- Password reset via Supabase Auth flows.
- Session management handled by Supabase client.
- User data scoped to authenticated user; users only access their own renewal opportunities.

## Notifications & Email

- Automated renewal reminders sent via Resend email service.
- Reminder schedules configurable per opportunity (30, 14, 3 days before renewal).
- Emails include opportunity details and recommended next actions.
- Users can enable/disable notifications per opportunity.
- Webhooks from Stripe used for subscription and payment events.

## Payments

- Stripe Checkout for subscription plans: Starter ($19/mo), Growth ($39/mo), Pro ($49/mo).
- Customer Portal enabled for subscription management.
- Billing limits enforced by backend (user count, opportunity count).
- Stripe webhooks to sync subscription status and enforce access.

## Deployment

- Deploy on Vercel using Next.js 15 App Router with TypeScript.
- Tailwind CSS v4 + shadcn/ui for styling and UI components.
- Supabase as backend database and auth provider.
- Resend for transactional emails.
- Stripe for payments and subscription management.

## Security

- Use Supabase Row Level Security (RLS) policies to restrict data access to authenticated users only.
- Passwords hashed and managed by Supabase Auth.
- Validate all user inputs on server and client.
- Use HTTPS everywhere (Vercel default).
- Secure environment variables for API keys (Stripe, Resend, Supabase).
- Rate limiting on API routes to prevent abuse.

## venture.config.json

```json VENTURE_CONFIG
{
  "name": "RenewalFlow Tracker",
  "tagline": "Simplify and automate your SaaS renewal workflows",
  "description": "A self-serve renewal opportunity tracker for mid-sized SaaS teams to reduce revenue leakage and improve forecasting without enterprise complexity.",
  "domain": "renewalflowtracker.com",
  "flags": {
    "waitlistMode": false,
    "mvpReady": true
  },
  "brand": {},
  "landing": {
    "headline": "Never Miss a Renewal Again",
    "subheadline": "Centralize your renewal pipeline, track risk, and automate reminders to maximize SaaS revenue retention.",
    "painStats": [
      {
        "stat": "30%",
        "label": "of SaaS revenue lost to missed renewals"
      }
    ],
    "steps": [
      {
        "title": "Add Renewal Opportunities",
        "desc": "Quickly enter your customers and renewal details."
      },
      {
        "title": "Track Risk & Playbooks",
        "desc": "Document risk factors and actionable steps per renewal."
      },
      {
        "title": "Get Automated Reminders",
        "desc": "Receive timely email alerts so nothing slips through."
      }
    ],
    "painPoints": [
      "Manual renewal tracking causes missed revenue",
      "Scattered data makes forecasting impossible",
      "No affordable, simple tool built for mid-sized SaaS teams"
    ],
    "pricing": [
      {
        "plan": "Starter",
        "price": "$19",
        "period": "/mo",
        "desc": "For small SaaS teams and solopreneurs starting out",
        "features": [
          "Up to 5 users",
          "Up to 100 renewal opportunities per month",
          "Renewal Opportunity Dashboard",
          "Risk & Playbook Notes",
          "Automated Renewal Reminders",
          "Basic email support"
        ],
        "highlighted": true
      },
      {
        "plan": "Growth",
        "price": "$39",
        "period": "/mo",
        "desc": "For growing SaaS teams needing scale and integrations",
        "features": [
          "Up to 15 users",
          "Up to 500 renewal opportunities per month",
          "All Starter features",
          "Priority support",
          "Integrations (Slack notifications, enhanced email workflows)"
        ],
        "highlighted": false
      },
      {
        "plan": "Pro",
        "price": "$49",
        "period": "/mo",
        "desc": "For larger SMB SaaS teams requiring full automation and insights",
        "features": [
          "Unlimited users",
          "Unlimited renewal opportunities",
          "All Growth features",
          "Advanced analytics dashboard",
          "AI-powered renewal risk scoring and recommended next actions (post-MVP)"
        ],
        "highlighted": false
      }
    ],
    "finalCta": {
      "headline": "Start tracking your renewals today",
      "subheadline": "Sign up now and get immediate value with our simple, affordable tool."
    }
  },
  "dashboard": {
    "navItems": [
      {
        "label": "Dashboard",
        "href": "/dashboard",
        "icon": "LayoutDashboard"
      },
      {
        "label": "Renewal Opportunities",
        "href": "/dashboard/opportunities",
        "icon": "Search"
      },
      {
        "label": "Settings",
        "href": "/dashboard/settings/profile",
        "icon": "Settings"
      }
    ],
    "features": [
      {
        "name": "Renewal Opportunities",
        "slug": "opportunities",
        "description": "Manage and track all your renewal opportunities with risk and playbook details.",
        "table": "PREFIX_renewal_opportunities",
        "fields": [
          {
            "name": "customer_name",
            "type": "text",
            "label": "Customer Name",
            "required": true
          },
          {
            "name": "renewal_date",
            "type": "date",
            "label": "Renewal Date",
            "required": true
          },
          {
            "name": "contract_value",
            "type": "numeric",
            "label": "Contract Value",
            "required": true
          },
          {
            "name": "status",
            "type": "enum",
            "label": "Status",
            "required": true
          },
          {
            "name": "risk_level",
            "type": "enum",
            "label": "Risk Level",
            "required": true
          },
          {
            "name": "next_action",
            "type": "text",
            "label": "Next Action",
            "required": false
          },
          {
            "name": "assigned_user_id",
            "type": "uuid",
            "label": "Assigned User",
            "required": true
          }
        ],
        "crud": {
          "create": true,
          "read": true,
          "update": true,
          "delete": true
        },
        "stats": [
          {
            "label": "Total",
            "field": "count"
          },
          {
            "label": "High Risk",
            "field": "high_risk_count"
          },
          {
            "label": "Due This Month",
            "field": "due_this_month_count"
          },
          {
            "label": "Renewal Success Rate",
            "field": "renewal_success_rate"
          }
        ],
        "defaultSort": "renewal_date",
        "searchable": ["customer_name"]
      }
    ]
  }
}
```

# End of Engineering Design Doc