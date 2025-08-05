# Complete Zapier + OpenAI Setup Guide

## Quick Setup (For Experienced Users)

1. Create Zap: Form/Email Trigger → OpenAI Score → Google Sheets Log → Gmail Response
2. OpenAI Prompt: "Score 1-10 for buying intent: [message]"
3. Response Time: <60 seconds
4. Test with real data
5. Charge client $299

## Detailed Setup Instructions

### Part 1: Pre-Setup (5 minutes)

#### What You Need:
- [ ] Client's Gmail address
- [ ] Access to their website form OR
- [ ] Their business email for parsing
- [ ] Industry and business type
- [ ] Their calendar/booking link

#### Accounts Required:
- Zapier (Free plan works for testing, Starter for production)
- OpenAI (API access with $20 credit)
- Google account (Sheets + Gmail)

### Part 2: Google Sheets Setup (10 minutes)

#### Create Master Lead Sheet

1. **Create new Google Sheet named:** "[Business Name] - AI Lead Tracker"

2. **Add these columns:**
```
A: Timestamp
B: Lead Name
C: Email
D: Phone
E: Original Message
F: AI Score (1-10)
G: Score Reasoning
H: Response Sent (Yes/No)
I: Response Time
J: Response Text
K: Follow-up Status
L: Converted (Yes/No)
M: Revenue Generated
```

3. **Format the sheet:**
- Freeze top row
- Add data validation for Yes/No columns
- Conditional formatting for scores:
  - 8-10: Green
  - 5-7: Yellow
  - 1-4: Red

4. **Share settings:**
- Share with client as "Viewer"
- Share with Zapier (will happen automatically)

### Part 3: OpenAI Configuration (5 minutes)

#### Get API Key

1. Go to [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. Click "Create new secret key"
3. Name it: "[Client Name] Lead Scoring"
4. Copy key immediately (won't show again)
5. Store securely

#### Set Usage Limits

1. Go to Settings → Billing
2. Set monthly limit: $50 (handles ~2,500 leads)
3. Set alert at $40

### Part 4: Zapier Automation (20 minutes)

#### Step 1: Create Your Zap

**Name:** "[Client Name] - AI Lead Response System"

#### Step 2: Set Up Trigger

**Option A: Web Form**
- App: Webhooks by Zapier
- Trigger: Catch Hook
- Copy webhook URL
- Add to client's form

**Option B: Email Parser**
- App: Email Parser by Zapier
- Set up parsing template
- Forward leads to parser address

**Option C: Direct Integration**
- Choose: TypeForm, Google Forms, etc.
- Connect account
- Select specific form

#### Step 3: OpenAI Lead Scoring

**App:** OpenAI (ChatGPT)
**Action:** Send Prompt

**Configuration:**
```javascript
Model: gpt-4
Max Tokens: 150
Temperature: 0.3

Prompt:
You are an AI lead scoring assistant for [CLIENT BUSINESS], a [INDUSTRY] company in [LOCATION].

Analyze this lead and provide:
1. A score from 1-10 (10 being ready to buy immediately)
2. A brief reason for the score
3. Key intent indicators

Lead Information:
Name: {{Step 1: Name}}
Email: {{Step 1: Email}}
Message: {{Step 1: Message}}

Consider these factors:
- Urgency indicators (ASAP, urgent, immediately, today)
- Budget mentions or pre-approval
- Specific service requests vs general inquiries
- Timeline mentions
- Contact preference stated

Format your response exactly like this:
SCORE: [number]
REASON: [one sentence explanation]
INTENT: [buy-now/high-interest/exploring/low-interest]
URGENCY: [immediate/this-week/this-month/future]
```

#### Step 4: Parse OpenAI Response

**App:** Formatter by Zapier
**Action:** Text → Extract Pattern

**Pattern:** `SCORE: (.*)`
**Output:** Score Number

Repeat for REASON, INTENT, and URGENCY

#### Step 5: Log to Google Sheets

**App:** Google Sheets
**Action:** Create Spreadsheet Row

**Mapping:**
- Timestamp: {{zap_meta_human_now}}
- Lead Name: {{Step 1: Name}}
- Email: {{Step 1: Email}}
- Phone: {{Step 1: Phone}}
- Message: {{Step 1: Message}}
- Score: {{Step 4: Score}}
- Reasoning: {{Step 4: Reason}}
- Response Sent: Pending

#### Step 6: Intelligent Email Response

**App:** Gmail
**Action:** Send Email

**Configuration:**

**To:** {{Step 1: Email}}
**From:** Client's email
**Subject:** Re: Your inquiry - [Business Name]

**Body (with dynamic content):**
```html
Hi {{Step 1: Name|default:there}},

Thank you for reaching out to [Business Name]!

{{if Score >= 8}}
I can see this is urgent for you, and I want to help immediately. I have availability today for a quick call.

Would you prefer:
- A call right now? Here's my direct line: [PHONE]
- Schedule a time that works: [CALENDAR LINK]

I'll also send you some quick information that might help in the meantime.
{{else if Score >= 5}}
I'd love to learn more about your needs and show you how we can help.

I have a few openings this week:
- Tomorrow at 2 PM
- Thursday at 10 AM
- Friday at 3 PM

Or you can pick any time that works for you: [CALENDAR LINK]
{{else}}
Thanks for your interest! I'd be happy to provide more information.

Here's a quick overview of our services: [LINK TO SERVICES]

When you're ready to discuss further, you can:
- Reply to this email with any questions
- Schedule a no-obligation consultation: [CALENDAR LINK]
- Call us at: [PHONE]
{{endif}}

{{if Message contains "price" or "cost"}}
Regarding pricing, we offer several options depending on your specific needs. I'd be happy to provide a detailed quote after a brief conversation about your requirements.
{{endif}}

Looking forward to speaking with you!

Best regards,
[CLIENT NAME]
[BUSINESS NAME]
[PHONE]

P.S. - This email was sent within {{Step 5: Response Time}} seconds of your inquiry because we value your time and business.
```

#### Step 7: Update Sheet with Response

**App:** Google Sheets
**Action:** Update Spreadsheet Row

Update the row to show:
- Response Sent: Yes
- Response Time: Calculate from timestamp
- Response Text: Copy of email sent

#### Step 8: Smart Follow-Up Sequence

**Add Path:**

**Path A: High Score (8-10) No Response**
- Delay: 2 hours
- Send SMS (if phone provided)
- Send follow-up email
- Alert client immediately

**Path B: Medium Score (5-7) No Response**
- Delay: 24 hours
- Send follow-up email
- Add to nurture sequence

**Path C: Low Score (1-4) No Response**
- Delay: 3 days
- Send resources email
- Add to newsletter

### Part 5: Industry-Specific Templates

#### Real Estate Template
```
High Score Response Focus:
- Mention MLS access
- Offer immediate property list
- Schedule showing ASAP
- Include market analysis

Keywords to detect:
- Pre-approved
- Cash buyer
- Relocating
- Specific neighborhood
```

#### Law Firm Template
```
High Score Response Focus:
- Emphasize confidentiality
- Offer free consultation
- Mention urgent availability
- Include intake form

Keywords to detect:
- Urgent matter
- Court date
- Deadline
- Legal emergency
```

#### HVAC/Plumbing Template
```
High Score Response Focus:
- Same-day service availability
- Emergency contact number
- Upfront pricing mention
- Service area confirmation

Keywords to detect:
- Not working
- Emergency
- No heat/AC
- Water leak
```

#### Medical/Spa Template
```
High Score Response Focus:
- Available appointments
- New patient specials
- Specific treatment info
- Online booking link

Keywords to detect:
- Consultation
- Specific procedure
- Available this week
- Pricing question
```

### Part 6: Advanced Configurations

#### Multi-Channel Integration

**SMS for High-Priority Leads:**
```javascript
If Score >= 9:
  Send SMS via Twilio:
  "Hi {{Name}}, just got your message about {{extract_service}}.
   This sounds urgent - I can help today. Call me directly at
   [PHONE] or reply CALLBACK for immediate assistance."
```

**Slack Notifications:**
```javascript
If Score >= 8:
  Post to Slack #hot-leads:
  "🔥 HOT LEAD: {{Name}} - Score: {{Score}}/10
   Message: {{Message}}
   Already responded via email.
   Call them NOW: {{Phone}}"
```

#### Custom Scoring by Industry

**Real Estate Scoring Boost:**
- +2 if message contains "pre-approved"
- +3 if message contains "cash"
- +2 if message contains "this weekend"
- +1 for each bedroom number mentioned

**Service Business Scoring Boost:**
- +3 if message contains "not working"
- +2 if message contains "today"
- +2 if message contains "emergency"
- +1 if includes phone number

### Part 7: Testing Protocol

#### Test Scenarios

1. **High Intent Test:**
   - Message: "AC broken, need service today, 85 degrees in house"
   - Expected Score: 9-10
   - Response time: <60 seconds

2. **Medium Intent Test:**
   - Message: "Looking for quote on new system"
   - Expected Score: 5-7
   - Response time: <2 minutes

3. **Low Intent Test:**
   - Message: "Just browsing options"
   - Expected Score: 1-4
   - Response time: <5 minutes

#### Quality Checks

- [ ] Email sends from correct address
- [ ] Personalization tokens work
- [ ] Links are clickable
- [ ] Phone numbers are correct
- [ ] Score reasoning makes sense
- [ ] Sheet updates properly
- [ ] No duplicate sends

### Part 8: Client Handoff

#### Training Documentation

**Create simple PDF with:**
1. How to access the lead sheet
2. What the scores mean
3. How to handle hot leads
4. Where responses go
5. Monthly report access

#### Set Expectations

**Performance Metrics:**
- Response time: 47 seconds average
- Coverage: 100% of leads
- Accuracy: 85% score reliability
- Uptime: 99.9%

**What Client Still Needs to Do:**
- Respond to replies
- Close the deals
- Update sheet with outcomes
- Provide feedback on scoring

### Part 9: Troubleshooting

#### Common Issues & Fixes

**Emails not sending:**
- Check Gmail authentication
- Verify email limits (500/day)
- Check spam folder

**Scores seem off:**
- Refine prompt with examples
- Add industry context
- Adjust temperature setting

**Duplicate sends:**
- Add deduplication step
- Check trigger settings
- Add email log check

**Slow response:**
- Upgrade Zapier plan
- Reduce prompt complexity
- Check trigger frequency

### Part 10: Optimization After Launch

#### Week 1 Adjustments
- Review all scores vs actual outcomes
- Adjust prompt based on patterns
- Refine email templates
- Add missing scenarios

#### Month 1 Report Should Include
- Total leads processed
- Average response time
- Score distribution
- Conversion by score
- Revenue generated
- Client feedback

#### Scaling Considerations
- Zapier task limits
- OpenAI token usage
- Gmail sending limits
- Sheet row limits (5 million)

## Billing & Maintenance

### Initial Setup Costs
- Your time: 3 hours @ $100/hr = $300 value
- Zapier: $20/month (client pays)
- OpenAI: $20/month (client pays)
- Your fee: $299 (one-time)

### Monthly Maintenance (Optional)
- Check performance weekly
- Adjust scoring monthly
- Add new automations
- Provide reports
- Fee: $49/month

## Final Setup Checklist

- [ ] Google Sheet created and shared
- [ ] OpenAI API key configured
- [ ] Zapier workflow built
- [ ] All test scenarios passed
- [ ] Client trained on system
- [ ] Documentation delivered
- [ ] Payment received
- [ ] Follow-up scheduled

## Support Resources

- Zapier Help: zapier.com/help
- OpenAI Docs: platform.openai.com/docs
- Google Sheets: support.google.com/sheets
- Your support: clayton@claytonaistack.com

---

**Remember:** You're not just setting up automation. You're delivering a revenue-generating system that works 24/7/365. Price accordingly.
