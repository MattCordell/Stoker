# Stoker

**Stoker** is a web-based authoring tool for creating [FHIR R4 ValueSet](https://hl7.org/fhir/R4/valueset.html) resources. It provides an intuitive interface for healthcare terminology specialists and FHIR implementers to build, validate, and export ValueSet definitions without writing JSON by hand.

## Live Deployments

- **Production (Stable)**: [https://mattcordell.github.io/Stoker/](https://mattcordell.github.io/Stoker/)
- **Development (Latest Features)**: [https://stoker-dev.netlify.app/](https://stoker-dev.netlify.app/)
- **Feature Branches**: `https://[branch-name]--stoker-dev.netlify.app/`

## Purpose

Creating FHIR ValueSet resources typically requires manual JSON editing and deep knowledge of the FHIR specification. Stoker simplifies this process by providing a form-based interface where users can:

- Define ValueSet metadata (title, description, version, status, publisher, copyright)
- Add include blocks using various methods: explicit code lists, ECL expressions, property filters, external ValueSet references, or entire code systems
- Validate the resulting resource against FHIR R4 requirements
- Download the ValueSet as a properly formatted JSON file
- Save and reload work in progress

The tool runs entirely in the browser with no server-side dependencies, making it easy to use and deploy. All functionality is contained in a single [index.html](index.html) file that can be hosted on GitHub Pages or opened locally.

## Usage

Simply open the application in a web browser and start filling in the form. The JSON preview updates in real-time as you work, and you can download the final ValueSet resource when ready.

## Running Locally

While the [production deployment](https://mattcordell.github.io/Stoker/) is recommended for general use, you can run Stoker locally if needed (e.g., for development or testing). Since this is a static web application, you'll need a local HTTP server to properly load the configuration files:

**Python HTTP Server:**
```bash
python -m http.server 8000
```

**Node.js http-server:**
```bash
npx http-server -p 8000
```

**VS Code Live Server:**
Right-click on `index.html` and select "Open with Live Server".

Then navigate to `http://localhost:8000` in your browser.

> **Note:** Opening `index.html` directly in your browser (via `file://`) may cause CORS issues when loading configuration files.
