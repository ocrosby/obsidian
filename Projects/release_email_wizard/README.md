## 🧠 LLM Training Doc: Building a Re-entrant React Wizard UI for Release Emails

## 📌 Use Case Overview

This application assists teams in constructing **release emails** via a **multi-step UI wizard**. The process requires integration with:

- **Jira** (for issue tracking)
    
- **ServiceNow** (for incident history)
    
- **Google Docs** (for auto-generating test plans)
    

The wizard must support:

- Multiple steps
    
- Saving and resuming (re-entrant)
    
- External system integration
    
- React-focused implementation
    

---

## 🔧 Technology Stack

|Layer|Recommendation|
|---|---|
|**UI Framework**|[ShadCN UI](https://ui.shadcn.dev/) + [Tailwind CSS](https://tailwindcss.com/)|
|**Routing**|[React Router](https://reactrouter.com/) (for step-based navigation)|
|**State Mgmt**|[Zustand](https://github.com/pmndrs/zustand) or Redux Toolkit|
|**Persistence**|localStorage / IndexedDB (client-side) or backend API (server-side)|
|**Authentication**|OAuth2 / SSO via Auth0, Okta, etc.|
|**Backend**|FastAPI, Express.js, or Firebase Functions for 3rd-party API calls|

---

## 🧹 Wizard Step Flow

|Step|Description|
|---|---|
|1|Start Draft (name, version)|
|2|Fetch Issues from Jira|
|3|Review Incidents from ServiceNow|
|4|Generate/Import Google Docs Test Plan|
|5|Review + Generate Email Draft|
|6|Finalize/Save/Send|

Each step:

- Has its own route (e.g., `/release/new/step-2`)
    
- Should persist data on change
    
- Must support validation and draft saving
    

---

## 🔁 Re-entrant Behavior Design

### Persistence Strategy

- Store drafts per user + release version
    
- Save data after each step
    
- Reload and navigate to last completed step
    

### Sample API

```http
GET /drafts/:release_id
POST /drafts/:release_id
PUT /drafts/:release_id/step/:step_id
```

### Wizard Resume Example (in React)

```tsx
useEffect(() => {
  const fetchProgress = async () => {
    const draft = await api.get(`/drafts/${releaseId}`);
    navigate(`/release/new/step-${draft.step}`);
  };
  fetchProgress();
}, []);
```

---

## 🧱 Project Structure Example

```
/release-email-ui
├── src/
│   ├── components/
│   │   └── FormSteps/
│   ├── pages/
│   │   └── ReleaseWizard.tsx
│   ├── hooks/
│   │   └── useDraft.ts
│   ├── store/
│   │   └── releaseStore.ts
│   ├── utils/
│   │   └── jira.ts, servicenow.ts, gdocs.ts
│   └── App.tsx
```