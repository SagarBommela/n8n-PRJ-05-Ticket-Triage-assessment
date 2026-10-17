# N8N Automation Engineering – Assessment Project 
## Customer Support Ticket Triage, Routing & Auto-Responder

### 1. Project Overview

The goal of this project is to automate how customer support tickets are received, tracked, and handled using n8n.

When a customer submits a support request, the workflow should automatically create a ticket, identify its priority, save the details in Google Sheets, and send the required emails.

For urgent issues, it should also notify the internal support team so they can take action quickly.

### 2. Tools Used

- **n8n:** To build and run the automation.
- **Webhook:** To receive support tickets.
- **Edit Fields:** To clean the data and generate a ticket ID.
- **Switch:** To route tickets based on priority.
- **Google Sheets:** To store ticket details.
- **Gmail:** To send customer emails and internal notifications.
- **Postman:** To test the workflow.

### 3. Priority and SLA Rules

Each ticket should receive a target response time based on its priority.

| Priority | Target Response Time | What Should Happen? |
|---|---|---|
| Critical | 2 hours | Send a customer confirmation and an urgent alert to the support lead. |
| High | 4 hours | Send a customer confirmation and notify the high-priority support queue. |
| Standard | 24 hours | Send a customer confirmation without an escalation alert. |

Here, SLA means the target time for responding to a customer's issue. It does not necessarily mean the issue must be fully resolved within that time.

### 4. Receiving Support Tickets

The workflow should receive ticket details through a Webhook using an HTTP POST request.

The request should include the following information:

- Customer name
- Email address
- Issue category
- Priority
- Subject
- Description

Example request:

```json
{
  "customer_name": "Johnathan Doe",
  "email": "YOUR_TEST_EMAIL@gmail.com",
  "category": "Infrastructure",
  "priority": "Critical",
  "subject": "Production Database Connection Timeout",
  "description": "The production database is not accepting SSL connections."
}
```

Replace the sample email address with one you can access so you can verify that the confirmation email arrives.

### 5. Setting Up Google Sheets

Create a Google spreadsheet named `N8N_PRJ_05_Support_Ticket_Register`.

Inside it, create a worksheet named `Support_Ticket_Register`.

Add these nine columns:

`Ticket_ID, Created_At, Customer_Name, Customer_Email, Category, Priority, Subject, Target_SLA_Hrs, Status`

Whenever a ticket is received and validated, the workflow should add a new row with the ticket details, creation time, assigned SLA, and initial status of `Open`.

### 6. How the Workflow Should Work

Connect the nodes in the following order:

**Webhook → Edit Fields → Priority Validation → Switch**

After validating the ticket, the Switch node should send it down the correct path based on its priority.

Each priority branch should:

1. Set the correct SLA value.
2. Return an HTTP 200 response to the requester without waiting for the emails to finish.
3. Save the ticket in Google Sheets.
4. Send a confirmation email to the customer.
5. Send an internal notification when required.

The internal notification should go to the support lead for Critical tickets and the high-priority queue for High tickets. Standard tickets only need the customer confirmation.

Make sure a failure in the customer email step does not prevent a Critical ticket from being escalated.

### 7. Ticket ID Format

Every ticket should have a reference number in this format:

`TCK-YYYYMMDD-XXXX`

Example:

`TCK-20261017-5120`

The ID contains the date and a randomly generated four-digit number. For this assessment, you can use the required format. In a production workflow, use a reliable uniqueness check or a persistent unique identifier to prevent duplicate ticket IDs.

### 8. Steps to Build the Workflow

1. Create the Google Sheets register with the required columns.
2. Open n8n and create a new workflow.
3. Add a Webhook node with the POST method and the path `v1/support-ticket`.
4. Add an Edit Fields node to clean the input data and generate the Ticket ID.
5. Validate the required fields and make sure the priority is Critical, High, or Standard.
6. Add a Switch node to route tickets based on priority.
7. Set the correct SLA in each branch: 2, 4, or 24 hours.
8. Configure the Respond to Webhook node to return an HTTP 200 JSON response.
9. Add a Google Sheets node and map all nine columns.
10. Connect Gmail using OAuth credentials.
11. Create the customer confirmation email and the required internal notifications.
12. Test all three priority levels and verify the results.
13. Export the finished workflow as `N8N_PRJ_05_Ticket_Triage.json`.

For detailed node settings, refer to `docs/implementation-guide.md`.

### 9. Testing the Workflow

Run the following tests to make sure everything works correctly.

| Test ID | Test Scenario | Expected Result |
|---|---|---|
| TC-001 | Critical ticket | HTTP 200 response, 2-hour SLA, Google Sheets entry, customer email, and urgent lead alert. |
| TC-002 | Standard ticket | HTTP 200 response, 24-hour SLA, Google Sheets entry, and customer email without escalation. |
| TC-003 | High ticket | HTTP 200 response, 4-hour SLA, Google Sheets entry, customer email, and queue notification. |


Git Url:

https://github.com/SagarBommela/n8n-PRJ-05-Ticket-Triage-assessment

 Loom Video:


https://www.loom.com/share/7e15c8f4ae4a4f51a88f5f127f5518b3




