# Amazon Q Business vs Amazon Q Developer

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **[[Amazon Q Business]]**: Enterprise knowledge assistant cho nhân viên
> - **[[Amazon Q Developer]]**: Coding assistant cho developers

---

## Tổng quan nhanh

| Tiêu chí | Q Business | Q Developer |
|----------|------------|-------------|
| **Target user** | Business users, employees | Developers, engineers |
| **Purpose** | Enterprise knowledge assistant | Coding assistant |
| **Data source** | Company docs, systems | Code, documentation |
| **Output** | Answers, insights | Code, explanations |
| **Integration** | 40+ enterprise connectors | IDEs, CLI, console |

---

## Amazon Q Business

### Mục đích
AI assistant cho **nhân viên doanh nghiệp** - trả lời câu hỏi từ dữ liệu nội bộ.

### Use cases
✅ **HR questions** - "Chính sách nghỉ phép?"  
✅ **Policy lookup** - "Quy trình expense report?"  
✅ **Knowledge search** - tìm kiếm across systems  
✅ **Document Q&A** - hỏi về nội dung tài liệu  
✅ **Summarization** - tóm tắt reports, meetings  

### Data Sources (40+)
```
┌─────────────────────────────────────────────────────────────┐
│  Q Business Connectors                                       │
│                                                              │
│  Documents:     S3, SharePoint, Confluence, Google Drive    │
│  Databases:     RDS, Aurora, Salesforce, ServiceNow         │
│  Communication: Slack, Teams, Gmail                          │
│  Ticketing:     Jira, Zendesk                               │
│  Custom:        Web crawler, APIs                            │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Features
- **Guardrails** - topic blocking, response control
- **Q Apps** - no-code app builder
- **Plugins** - actions (create ticket, send email)
- **Identity** - IAM Identity Center integration
- **ACL** - document-level permissions

---

## Amazon Q Developer

### Mục đích
AI assistant cho **developers** - code generation, debugging, learning.

### Use cases
✅ **Code generation** - viết code từ comments  
✅ **Code explanation** - giải thích code  
✅ **Debugging** - tìm và fix bugs  
✅ **AWS guidance** - best practices, architecture  
✅ **Code transformation** - upgrade Java, .NET  
✅ **Security scanning** - vulnerability detection  

### Where it works
```
┌─────────────────────────────────────────────────────────────┐
│  Q Developer Locations                                       │
│                                                              │
│  IDEs:      VS Code, JetBrains, Visual Studio               │
│  AWS:       Console, CLI, CloudShell                        │
│  Chat:      Slack, Teams (via AWS Chatbot)                  │
│  Docs:      AWS Documentation                                │
│  GitLab:    Integration                                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Features
- **Inline suggestions** - autocomplete while typing
- **Chat** - conversational coding help
- **/dev** - generate code from natural language
- **/transform** - upgrade legacy code
- **/review** - security and code review
- **@workspace** - context from current project

---

## So sánh chi tiết

### Target Audience

| Q Business | Q Developer |
|------------|-------------|
| All employees | Developers |
| Non-technical | Technical |
| Knowledge workers | Engineers |
| Business analysts | DevOps, SRE |

### Data & Context

| Aspect | Q Business | Q Developer |
|--------|------------|-------------|
| Primary data | Enterprise docs | Code, AWS docs |
| Custom data | ✅ 40+ connectors | ✅ Workspace context |
| Real-time | ❌ Indexed | ✅ Current file |
| Permissions | ACL-based | IAM |

### Capabilities

| Feature | Q Business | Q Developer |
|---------|------------|-------------|
| Q&A | ✅ Enterprise knowledge | ✅ AWS/coding |
| Summarization | ✅ Documents | ❌ |
| Code generation | ❌ | ✅ |
| Code explanation | ❌ | ✅ |
| Security scanning | ❌ | ✅ |
| Code transformation | ❌ | ✅ |
| No-code apps | ✅ Q Apps | ❌ |
| Actions/Plugins | ✅ | ❌ |

---

## Architecture

### Q Business
```
┌─────────────────────────────────────────────────────────────┐
│                     Q BUSINESS                               │
│                                                              │
│  Data Sources ──► Connector ──► Index ──► Q Business        │
│  (SharePoint,      (Sync)       (Vector)   │                │
│   Confluence...)                           │                 │
│                                            ▼                 │
│                                    Web UI / Slack / Teams    │
│                                            │                 │
│                                            ▼                 │
│                               "What's the PTO policy?"       │
│                               "Summarize last board meeting" │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Q Developer
```
┌─────────────────────────────────────────────────────────────┐
│                     Q DEVELOPER                              │
│                                                              │
│  Workspace ──► @workspace context ──► Q Developer           │
│  (Code files)                           │                    │
│                                         │                    │
│  AWS Docs ──────────────────────────────┤                   │
│                                         │                    │
│  Current File ──────────────────────────┤                   │
│                                         ▼                    │
│                               IDE / Console / CLI            │
│                                         │                    │
│                                         ▼                    │
│                               "Generate Lambda function"     │
│                               "/transform Java 8 to 17"      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Pricing Model

| Q Business | Q Developer |
|------------|-------------|
| Per user/month | Free tier + Pro tier |
| ~$20/user/month (Lite) | Free: limited |
| ~$50/user/month (Pro) | Pro: ~$19/user/month |
| Index storage costs | No storage costs |

---

## Decision Matrix

| Scenario | Choice |
|----------|--------|
| Answer HR policy questions | Q Business |
| Search company documents | Q Business |
| Generate code | Q Developer |
| Debug production issue | Q Developer |
| Summarize meeting notes | Q Business |
| Code review | Q Developer |
| Build no-code app from data | Q Business (Q Apps) |
| Upgrade Java version | Q Developer (/transform) |
| AWS architecture guidance | Q Developer |
| Create Jira ticket from chat | Q Business (Plugins) |

---

## Can use both?

```
✅ Yes! Different purposes

Developer workflow:
1. Q Developer: Write/debug code
2. Q Business: Look up internal docs
3. Q Developer: Get AWS best practices
4. Q Business: Check compliance policies

Business analyst:
1. Q Business: Search knowledge base
2. Q Business: Create Q App
3. Q Developer: (probably not needed)
```

---

## Xem thêm
- [[Amazon Q Business]]
- [[Amazon Q Developer]]
- [[Amazon Q Apps]]
