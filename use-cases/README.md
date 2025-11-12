# EGAT AI Workshop Use Cases

This directory contains analyzed use cases from EGAT workshop participants, categorized by suitability for Microsoft Copilot Studio implementation.

## Use Case Analysis Summary

Total use cases submitted: **27**
- ✅ **Excellent for Copilot Studio**: 6 use cases
- ⚠️ **Possible with Copilot Studio** (with limitations): 5 use cases
- ❌ **Not suitable for Copilot Studio**: 13 use cases
- ❓ **Unclear/Needs clarification**: 3 use cases

---

## ✅ Excellent Use Cases for Copilot Studio

These use cases are ideal for Copilot Studio and will be implemented in this workshop:

### 1. Document Inquiry Chatbot (ธนิตศร ไชยวุฒิ)
**Status**: 🟢 BEST FIT
**Folder**: [`01-document-inquiry-chatbot/`](./01-document-inquiry-chatbot/)

**Description**: AI chatbot that EGAT staff can use to inquire and find sites or document references.

**Why it's perfect for Copilot Studio**:
- Core strength of Copilot Studio (knowledge sources)
- Built-in document indexing
- Natural language understanding
- Easy integration with SharePoint/OneDrive
- Generative answers from documents

**Implementation Complexity**: ⭐⭐ (Easy-Medium)

---

### 2. HR Recruitment Document Assistant (กฤตณณ์ คีรีทรัพย์)
**Status**: 🟢 EXCELLENT FIT
**Folder**: [`02-hr-recruitment-assistant/`](./02-hr-recruitment-assistant/)

**Description**: AI Agent to help recruiters summarize information from CVs/Resumes to find and match candidates.

**Why it's perfect for Copilot Studio**:
- Document processing and extraction
- Integration with SharePoint for CV storage
- Structured data extraction
- Matching logic with conditional flows
- Power Automate integration for workflow

**Implementation Complexity**: ⭐⭐⭐ (Medium)

---

### 3. Application Monitoring & Alert Agent (ศุภษา พสุกวรรณ)
**Status**: 🟢 EXCELLENT FIT
**Folder**: [`03-monitoring-alert-agent/`](./03-monitoring-alert-agent/)

**Description**: Automated agent to discover application health checks and alert staff via Microsoft Teams.

**Why it's perfect for Copilot Studio**:
- Proactive agent capabilities
- Teams integration (native)
- Power Automate for health check monitoring
- Adaptive cards for rich notifications
- Escalation logic for critical issues

**Implementation Complexity**: ⭐⭐⭐ (Medium)

---

### 4. Executive Assistant Calendar Agent (จิรภา พัฒนเพิ่มพูลศิริ)
**Status**: 🟢 EXCELLENT FIT
**Folder**: [`04-executive-calendar-assistant/`](./04-executive-calendar-assistant/)

**Description**: AI Agent to find available time slots in Outlook calendars for all attendees and generate bookings.

**Why it's perfect for Copilot Studio**:
- Conversational interface for calendar management
- Microsoft Graph API integration
- Power Automate for Outlook operations
- Multi-participant coordination
- Natural language date/time parsing

**Implementation Complexity**: ⭐⭐⭐⭐ (Medium-High)

---

### 5. Project Manager Assistant (ศุภณัฐ เจนเจนจบเมธา)
**Status**: 🟢 GOOD FIT
**Folder**: [`05-project-manager-assistant/`](./05-project-manager-assistant/)

**Description**: Automation to pull calendar bookings from Outlook for specific users, identify development/project tasks, and create timesheets in OpenProject.

**Why it's good for Copilot Studio**:
- Workflow orchestration
- Calendar integration
- OpenProject API integration via Power Automate
- Conversational approval flows
- Task categorization

**Implementation Complexity**: ⭐⭐⭐⭐ (High)

---

### 6. Monthly Timesheet Report Generator (วทัญญู แย้มสุขสุทธิ์)
**Status**: 🟢 GOOD FIT
**Folder**: [`06-timesheet-report-generator/`](./06-timesheet-report-generator/)

**Description**: Summarize timesheet data from OpenProject and generate visualizations for different metrics and dimensions.

**Why it's good for Copilot Studio**:
- Conversational data query interface
- Integration with OpenProject API
- Power Automate for data extraction
- Generate reports on-demand
- Export options (PDF, Excel)

**Implementation Complexity**: ⭐⭐⭐ (Medium-High)

---

## ⚠️ Possible with Copilot Studio (With Limitations)

These can be partially implemented with Copilot Studio but may require significant custom development:

### 7. Test Case Generator from Requirements (ปริศนา ชัยยา, นิศากรณ์ ศิลป์อภิรมย์)
**Alternative Recommendation**: ⚠️ Use Azure OpenAI + Custom Solution

**Why limited in Copilot Studio**:
- Requires deep understanding of code/requirements
- Better suited for specialized AI models
- May need custom prompt engineering beyond Copilot Studio

**Recommended Alternative**:
- Azure OpenAI Service with GPT-4
- GitHub Copilot for code-based test generation
- Custom Power Platform solution with AI Builder

**If implementing in Copilot Studio**:
- Use generative AI with uploaded requirements documents
- Create structured prompts for test case format
- Limited compared to specialized tools

---

### 8. Vendor Master Data Management (ชนนีทิภา กิ่งพัฒน์)
**Alternative Recommendation**: ⚠️ Power Apps + Power Automate preferred

**Why limited in Copilot Studio**:
- Primarily a data management task, not conversational
- Better suited for forms-based interface
- Complex data validation rules

**Recommended Alternative**:
- Power Apps for data entry interface
- Power Automate for workflow
- Dataverse for data storage

**If implementing in Copilot Studio**:
- Conversational data collection
- Power Automate integration for data updates
- Limited UI compared to Power Apps

---

### 9. Summarize Regulations/Rules (ออจิรา วิสาลรัตน์)
**Status**: ⚠️ Needs more information

**Why unclear**:
- No detailed explanation provided
- Could be good if it's document-based Q&A
- Might overlap with Document Inquiry Chatbot

**Recommended Approach**:
- If document-based: Use Copilot Studio with knowledge sources
- If summarization: Use Power Automate + Azure OpenAI
- Clarify exact requirements first

---

### 10. Service Modeling for Third-Party Projects (พรพรรณ มนูญศรีโรจน์)
**Status**: ❓ Unclear requirements

**Why unclear**:
- No detailed explanation
- Unclear what "modeling" means in this context

**Recommended Approach**:
- Clarify requirements before implementation
- May be better suited for Power Apps or specialized tools

---

### 11. Infographic/PR Assistant (ณัฏฐา เนาวรัตนประเสริฐ)
**Status**: ❓ Unclear requirements

**Why unclear**:
- No detailed explanation
- Infographic creation is visual design, not Copilot Studio strength

---

## ❌ Not Suitable for Copilot Studio

These use cases require specialized tools or different approaches:

### 12. Code Generation & Development Tools

**Use Cases**:
- ผู้ช่วยเขียนโค้ด TDD (นภัสกร บุญชู)
- Refactor Code (ทักษิณาภา หนูทอง)
- Generate Software Specifications from Code (รุ่งตะกูล, ปริญญา, สุทธิน์)

**Why not Copilot Studio**: Code generation requires specialized models

**Recommended Alternatives**:
- ✅ **GitHub Copilot** - Best for inline code generation
- ✅ **GitHub Copilot Chat** - Conversational coding assistance
- ✅ **Azure OpenAI with Code Models** - Custom solutions
- ✅ **Cursor AI** - AI-powered IDE
- ✅ **Claude Code** - Agentic coding assistant
- ✅ **Coderabbit** - AI code reviewer

**Implementation Path**:
- Subscribe to GitHub Copilot for EGAT developers
- Integrate with VS Code or preferred IDE
- Train team on effective prompt engineering for code

---

### 13. UI/UX Design Assistant (วิศรุต กิจสกุล)

**Why not Copilot Studio**: Visual design requires specialized design tools

**Recommended Alternatives**:
- ✅ **Figma AI** - AI-powered design assistance
- ✅ **Uizard** - AI UI design generator
- ✅ **Adobe Firefly** - Generative AI for design
- ✅ **Galileo AI** - Text-to-UI design

**Implementation Path**:
- Adopt Figma with AI plugins
- Train designers on AI-assisted workflows

---

### 14. Image Generation (ศรลีพัชร แสงสุวรรณ)

**Why not Copilot Studio**: Image generation requires specialized AI models

**Recommended Alternatives**:
- ✅ **DALL-E 3** (via Azure OpenAI or ChatGPT)
- ✅ **Midjourney** - High-quality image generation
- ✅ **Adobe Firefly** - Enterprise-safe image generation
- ✅ **Stable Diffusion** - Open-source alternative

**Implementation Path**:
- Azure OpenAI Service with DALL-E 3
- Integration with existing workflows via API
- Content moderation for enterprise use

---

### 15. Power BI Automation & Development

**Use Cases**:
- Power BI Update Data Automate (วรุตม์ บุญกากาฬ)
- ผู้ช่วยพัฒนา Power BI Dashboard (สุภกร ชูชุม, กฤษฎิ์ ธารพัฒนะสิริกุล)

**Why not Copilot Studio**: Power BI has its own AI capabilities

**Recommended Alternatives**:
- ✅ **Power BI Copilot** (built-in)
- ✅ **Power BI Quick Insights**
- ✅ **Power Automate** for data refresh automation
- ✅ **Azure Logic Apps** for complex data pipelines

**Implementation Path**:
- Enable Power BI Copilot in tenant
- Use Power Automate for data refresh scheduling
- Natural language queries in Power BI

---

### 16. Predictive Maintenance Data Quality (ณภัทร ช่างตาสุข)

**Why not Copilot Studio**: This is a data engineering and ETL problem

**Recommended Alternatives**:
- ✅ **Azure Data Factory** - Data pipeline orchestration
- ✅ **SQL Server Data Quality Services (DQS)**
- ✅ **Power Automate** - Workflow automation
- ✅ **Azure Synapse Analytics** - Data integration
- ✅ **Great Expectations** - Data quality framework

**Implementation Path**:
1. Create data quality rules in Azure Data Factory
2. Implement deduplication logic in SQL
3. Use Power Automate to validate before sending to IBM Maximo
4. Monitor with Azure Monitor

---

### 17. Payment Document Re-keying (นีลลินีภา นันต๊ะกา)

**Why not Copilot Studio**: RPA (Robotic Process Automation) task

**Recommended Alternatives**:
- ✅ **Power Automate Desktop** - Desktop automation
- ✅ **UiPath** - Enterprise RPA
- ✅ **Blue Prism** - RPA platform
- ✅ **AI Builder Document Processing** - Form recognition

**Implementation Path**:
- Use Power Automate Desktop for automated data entry
- AI Builder for document text extraction
- Integration with SAP via connectors

---

## Implementation Priority Recommendations

Based on business value, complexity, and Copilot Studio suitability:

### Phase 1: Quick Wins (Start Here) 🚀
1. **Document Inquiry Chatbot** - Highest ROI, easiest implementation
2. **Monitoring & Alert Agent** - High value for operations
3. **Timesheet Report Generator** - Clear user need, measurable value

### Phase 2: High-Value Complex (Next) 📈
4. **HR Recruitment Assistant** - High business value, moderate complexity
5. **Executive Calendar Assistant** - High impact for leadership
6. **Project Manager Assistant** - Good automation opportunity

### Phase 3: Alternatives for Non-Copilot Cases 🔧
7. Implement GitHub Copilot for developers
8. Deploy Power Automate Desktop for RPA needs
9. Enable Power BI Copilot for BI team
10. Evaluate Azure OpenAI for specialized AI needs

---

## Technical Requirements Summary

### Common Infrastructure Needed

✅ **Microsoft 365**
- Copilot Studio licenses
- Power Automate licenses (Premium connectors)
- SharePoint Online
- Teams

✅ **Azure Services**
- Azure Active Directory
- Azure OpenAI (for advanced cases)
- Azure Storage (for documents)

✅ **Power Platform**
- Dataverse environment
- Power Automate flows
- Premium connectors

✅ **Third-Party Integrations**
- OpenProject API (for PM assistant)
- IBM Maximo (if needed)
- SAP connectors (if needed)

---

## Next Steps

1. ✅ Review categorization with stakeholders
2. ✅ Prioritize Phase 1 implementations
3. ✅ Set up development environment
4. ✅ Begin with Document Inquiry Chatbot
5. ⏳ Gather detailed requirements for Phase 2
6. ⏳ Evaluate alternative tools for non-Copilot cases

---

## Use Case Folders

Each implemented use case has its own folder with:
- 📄 **README.md** - Overview and requirements
- 📄 **implementation-guide.md** - Step-by-step instructions
- 📄 **connections-required.md** - Technical connections needed
- 📄 **testing-guide.md** - How to test the solution
- 📁 **assets/** - Screenshots, templates, sample data

---

## Contact & Support

For questions about specific use cases:
- Document your questions in the use case folder
- Reference the participant name from the CSV
- Tag @workshop-facilitators for clarification

---

**Last Updated**: 2025-11-12
**Status**: Initial Analysis Complete
