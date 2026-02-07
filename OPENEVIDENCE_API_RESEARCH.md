# OpenEvidence API Research Report 🔍

**Researched for:** Nityaa  
**Date:** 2026-02-06  
**Researcher:** Shishya

---

## ✅ YES - OpenEvidence HAS an API!

**Good news:** OpenEvidence does provide programmatic API access for developers and businesses, separate from the standard physician web/app interface.

---

## API Access Details

### Official API Endpoint
- **Base URL:** `https://api.openevidence.com/`
- **Documentation:** `https://docs.openevidence.com/` (exists but some pages may be private/gated)

### Authentication
- **Method:** Token-based authentication
- **Header format:** `Authorization: Token $OPENEVIDENCE_API_KEY`

### Example API Call (from documentation)
```bash
curl \
  -X POST \
  -H 'Accept: text/event-stream' \
  -H 'Content-Type: application/json' \
  -H "Authorization: Token $OPENEVIDENCE_API_KEY" \
  -d '{"text": "what is the treatment for psoriasis", "model": "oe-v2"}' \
  https://api.openevidence.com/streaming/analysis
```

---

## API Features

### Available Endpoints (confirmed)
1. **Blocking API** - Standard request/response
2. **Streaming API** - Server-sent events for real-time responses
   - Endpoint: `/streaming/analysis`
   - Returns: Text chunks as data messages

### Models
- **oe-v2** - Current model identifier

---

## Access Requirements

### Who Can Access
- Businesses and application developers
- Organizations building medical AI applications
- Healthcare tech companies

### Governed By
- **API Terms of Service:** https://www.openevidence.com/policies/api
- Separate from physician user terms
- Last updated: March 16, 2024

### Key Requirements (from Terms)
1. **No PHI/Protected Health Information** - Must not include patient identifiers
2. **Proper licensing** - For integration into applications
3. **Compliance** - With all medical AI regulations
4. **Account responsibility** - Must maintain secure account

---

## How to Get API Access

### Contact Information
- **Email:** help@openevidence.com
- **Purpose:** Request API key and developer access

### What to Mention
Since you already have physician credentials:
1. You're a licensed physician (NPI verified)
2. You want API/programmatic access for bot integration
3. You already use OpenEvidence via browser/app
4. Purpose: Integrate into workflow automation/AI assistant

### Expected Process
1. Contact help@openevidence.com
2. Explain use case (medical AI assistant integration)
3. They'll provide API key
4. Review API Terms of Service
5. Begin integration

---

## Important Limitations & Compliance

### HIPAA Compliance
⚠️ **Critical:** From their terms:
> "You agree not to use the Services to create, receive, maintain, transmit, or otherwise process any information that includes or constitutes 'Protected Health Information', as defined under the HIPAA Privacy Rule."

**What this means:**
- Cannot send patient-identifiable information via API
- Questions must be de-identified
- General medical queries only (no specific patient data)

### Medical Advice Disclaimer
- OpenEvidence is NOT a healthcare provider
- API responses are for educational purposes
- Not a substitute for clinical judgment
- Should not be used for emergency situations

### Use Restrictions
Cannot use API to:
- Develop competing AI models
- Extract/scrape data outside API
- Share API keys with third parties
- Process children's data without consent

---

## Pricing & Billing

### Payment Structure
- Fees charged according to Order Form
- Payable in USD
- 30-day notice for price changes
- Non-refundable (except per agreement)

**Note:** Specific pricing not publicly listed - must contact for quote.

---

## Technical Integration Options

### For Your Use Case (Bot Integration)

**Option 1: Direct API Integration**
- Get API key from OpenEvidence
- Make HTTP requests from your bot (me, Shishya)
- Parse JSON responses
- Present findings in Telegram

**Option 2: Skill/Wrapper**
- Create OpenClaw skill for OpenEvidence
- Abstract API calls
- Add to my capabilities
- Easy slash commands like `/medical_query <question>`

**Recommended:** Option 2 - Create a dedicated OpenEvidence skill

---

## Sample Integration Workflow

1. **You ask me:** "What's the latest treatment for condition X?"
2. **I call OpenEvidence API:** Send de-identified question
3. **Receive evidence-based response:** With citations from NEJM, JAMA, NCCN
4. **Present to you:** Formatted summary with sources
5. **Follow-up:** You can ask clarifying questions

---

## Comparison: Web/App vs. API

| Feature | Web/App (Your Current Access) | API (Programmatic) |
|---------|-------------------------------|-------------------|
| Access method | Browser, iOS/Android app | HTTP requests |
| Authentication | NPI verification | API key |
| Usage | Manual queries | Automated/bot integration |
| Response format | Visual interface | JSON/streaming text |
| Integration | Standalone | Can embed in workflows |
| Rate limits | Not specified | Likely has limits |
| Cost | Free for verified physicians | Contact for pricing |

---

## Next Steps

### Immediate Action
📧 **Email OpenEvidence:** help@openevidence.com

**Subject:** API Access Request - Licensed Physician Integration

**Body suggestion:**
```
Hello,

I am a licensed physician currently using OpenEvidence via the web and 
mobile applications (NPI verified).

I would like to request programmatic API access to integrate OpenEvidence 
into my clinical workflow automation system. Specifically, I want to query 
the service via an AI assistant (OpenClaw) for faster access to 
evidence-based medical information during patient care.

All queries will be properly de-identified and HIPAA-compliant. Could you 
please provide:
1. API key for my account
2. API documentation access
3. Pricing information for API usage
4. Any integration guidelines

Thank you,
[Your name]
[Your NPI if needed]
```

### After Receiving API Key

1. **Test basic query** - Verify API works
2. **Create OpenEvidence skill** - Add to my skillset
3. **Define query patterns** - What kinds of questions to support
4. **Set up workflows** - Integration with daily routines

---

## Resources

### Official Links
- **Main site:** https://www.openevidence.com/
- **API Terms:** https://www.openevidence.com/policies/api
- **Contact:** help@openevidence.com
- **Documentation:** https://docs.openevidence.com/ (may require API key to access)

### Academic References
- PMC Article on OpenEvidence use in primary care: https://pmc.ncbi.nlm.nih.gov/articles/PMC12033599/
- Study on AI-powered clinical decision making
- Comparison with physician performance

---

## Conclusion

✅ **OpenEvidence API exists and is accessible**
✅ **Designed for integration into applications**  
✅ **You're already qualified (licensed physician)**  
✅ **Next step: Email help@openevidence.com for API key**

Once you have the API key, I can integrate this into my capabilities and provide evidence-based medical information queries as part of our daily work together! 🙏🏾

---

*Research completed: 2026-02-06 21:40 UTC*  
*Ready to proceed when you receive API access*
