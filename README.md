# (Simple) Jira Template Generator  
[![Made with HTML](https://img.shields.io/badge/Made%20with-HTML-orange.svg)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![No dependencies](https://img.shields.io/badge/Dependencies-None-brightgreen.svg)](#)
[![Runs locally](https://img.shields.io/badge/Runs-Locally-blue.svg)](#)

---

## Background
By pure coincidence, I discovered that Jira can pre-fill issue fields simply by adding parameters to a **URL**.  
When I clicked one of those URLs, a new issue opened — already filled in!  
It turned out this wasn’t an official API, but rather an **internal Jira feature** used by the platform itself.

That led to a thought: *what if there was a simple webpage that built these URLs automatically — with reusable templates for common reports?*  
This project is the result.

---

## Editing templates

All templates and configuration live **at the top of the HTML file**, inside a YAML block:

```yaml
<script type="text/yaml" id="templatesYaml">
templates:
  "Bug report CCX-3":
    project: "CCX-3"
    issuetype: "Bug"
    summary: "Bug: <text>"
    description: |
      *Problem description*
      <text>

      *Reproduction*
      <text>

defaults:
  baseUrl: "https://mydomain.com"
  endpoint: "init"
  pid:
    "CCX-3": 10302
  order: ["issuetype","summary"]
  issuetypes:
    Bug: 1
    Epic: 2
  dynamics:
    environment:
      lab:   ["LabA","LabB","LabC"]
      node:  ["Node1","Node2","Node3"]
      build: ["Build-Alpha","Build-Beta","Build-Gamma"]
</script>
```

💡 You edit the YAML **directly inside the file**.  
Reason: a Single Page Application (SPA) cannot load local files due to browser sandbox (CORS) restrictions.  
By embedding YAML inline, the tool remains fully self-contained — no dependencies, no external calls.

---

## Fields

- **templates** – each template appears in the *Template* dropdown.  
- **defaults** – defines global settings (base URL, endpoint, PID mapping, field order, dropdown mappings).  
- **dynamics** – defines fields built from multiple dropdowns (e.g. `environment` = `lab`, `node`, `build`).  
  Selected values are combined into a single Jira field.

All labels in the UI match their YAML field names exactly (`project`, `description`, `environment`, etc.).

---

## 🖋 Text formatting
Only **bold** text (`*text*`) is supported.  
Line breaks (`\n`) are preserved exactly as written in the YAML block.

---

## Usage

1. Save the file as `jira-template.html`.  
2. Run it locally:  
   ```bash
   python3 -m http.server 8080
   # open http://localhost:8080/jira-template.html
   ```
3. Choose a template, adjust the fields, and click **Create** → Jira opens with pre-filled fields.  
   Or click **Copy URL** to share the generated link.

---

## Security

- No external JavaScript or network traffic — everything runs locally.  
- `window.open(..., 'noopener,noreferrer')` prevents referer leaks.  
- YAML parser supports only basic safe structures (no script execution).  
- Warning is shown if URL length exceeds ~1800 characters.

---

## Example of a dynamic field

```yaml
dynamics:
  environment:
    lab:   ["LabA","LabB","LabC"]
    node:  ["Node1","Node2","Node3"]
    build: ["Build-Alpha","Build-Beta","Build-Gamma"]
```

This generates three dropdowns — **Lab**, **Node**, and **Build** — which together form the `environment` field in Jira.  
You can still edit the final field manually if needed.

---

## 💾 Repository setup

The project is fully local — no build tools or dependencies needed.

```bash
git clone https://github.com/stromg/jira-template.git
cd jira-template
python3 -m http.server 8080
```

---

## Reference links

If you want to read about Jira’s built-in URL parameter feature, Atlassian provides these public references:

- **Atlassian Knowledge Base:** [How to create issues using direct HTML links in Jira Server](https://confluence.atlassian.com/jirakb/how-to-create-issues-using-direct-html-links-in-jira-server-159474.html)  
  Example:  
  ```
  https://jira.atlassian.com/secure/CreateIssueDetails!init.jspa?pid=10420&issuetype=4&summary=say+hello+world
  ```

- **Community discussion:** [Pre-populate Label in CreateIssue URL](https://community.atlassian.com/t5/Jira-questions/Pre-populate-Label-in-CreateIssue-URL/qaq-p/628361)

---

Vibe-coded with ❤️  by **Göran Ström**

