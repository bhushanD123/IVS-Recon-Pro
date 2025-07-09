# IVS Recon Pro - Detailed Project Overview

## Project Summary

**IVS Recon Pro** is a web-based security reconnaissance tool built with Next.js. Instead of running actual network commands, it uses **Google's Gemini AI (via Genkit)** to simulate realistic security scans. This provides a safe yet powerful way to learn about and visualize the process of network scanning, vulnerability analysis, and exploit research. It features a comprehensive dashboard, detailed vulnerability reports, an exploit database, persistent scan history, and an AI-powered chatbot to answer security questions.

---

## Core Technologies

- **Framework:** **Next.js 15** (using the App Router). This provides a robust foundation with server-side rendering, client-side navigation, and a clear project structure.
- **Language:** **TypeScript**. This adds static typing to JavaScript, which helps prevent bugs and improves code quality.
- **AI Engine:** **Genkit with Google AI**. This is the heart of the application. Genkit is a framework that simplifies calling AI models. We use it to interact with Google's Gemini models to generate all the simulated scan data.
- **UI Components:** **ShadCN UI**. A collection of beautifully designed, accessible, and reusable React components built on Radix UI and Tailwind CSS.
- **Styling:** **Tailwind CSS**. A utility-first CSS framework that allows for rapid styling directly in the HTML. The theme is customized in `src/app/globals.css`.
- **State Management:**
    - **React Hooks (`useState`, `useEffect`, `useActionState`):** Used for managing component-level state.
    - **Browser Storage (`sessionStorage` and `localStorage`):** Used to persist data across page navigations and browser sessions. `sessionStorage` holds the latest scan result, while `localStorage` keeps a persistent history of all scans.

---

## How It All Works: The Data Flow

1.  **User Initiates a Scan:**
    - The user goes to the **Dashboard**, enters a target (e.g., `example.com`), and clicks "Scan".
    - This submits a form that triggers a **Next.js Server Action** called `performScan` (located in `src/app/actions.ts`).

2.  **AI Generates the Scan Data:**
    - The `performScan` action calls a **Genkit AI Flow** called `performScanFlow` (in `src/ai/flows/perform-scan.ts`).
    - This flow contains a detailed prompt that instructs the Gemini AI model to act like a security tool. It asks the AI to generate realistic data for OS detection, open ports, DNS records, API endpoints, XSS vulnerabilities, and potential CVEs.
    - The AI returns a structured JSON object containing all of this simulated data.

3.  **Results are Stored and Displayed:**
    - The AI's response is sent back to the `performScan` action.
    - The action saves the result in two places:
        1.  **Session Storage:** The complete result is saved as `latestScanResult`. This makes it immediately available to other pages in the current browser tab.
        2.  **Local Storage:** The result is added to a `scanHistory` list. This ensures your scan history persists even if you close the browser.
    - The result is also passed back to the Dashboard UI, which re-renders to display the detailed findings in accordions and tables.

4.  **Viewing Data on Other Pages:**
    - **Reports Page:** Reads the `latestScanResult` from session storage to generate a view-only report that can be downloaded as a PDF.
    - **Vulnerability Page:** Also reads `latestScanResult` to provide a focused breakdown of the CVEs and XSS issues found.
    - **History Page:** Reads the entire `scanHistory` from local storage to show a list of all past scans.
    - **Chat Page:** When a user asks a question, it can pass the `latestScanResult` as context to the AI, allowing the chatbot to answer questions specifically about that scan.

---

## Folder & File Breakdown

Here's a guide to the most important files and folders in the project:

#### `src/app/` - The Heart of the Application (Next.js App Router)

- **`(app)/`**: A route group containing all the main pages of the application.
    - `layout.tsx`: Defines the shared UI for all pages in this group, including the main sidebar navigation.
    - `dashboard/page.tsx`: The main dashboard page where scans are initiated.
    - `cves/page.tsx`: A tool to look up exploits for a specific CVE ID.
    - `exploits/page.tsx`: A tool to find exploits for a given service name.
    - `vulnerability/page.tsx`: Shows a detailed analysis of vulnerabilities from the last scan.
    - `reports/page.tsx`: Generates and allows downloading of a PDF report.
    - `history/page.tsx`: Displays a log of all past scans.
    - `chat/page.tsx`: The interactive AI chatbot interface.
    - `resources/page.tsx`: A static page with helpful cybersecurity links.
    - `about/page.tsx`: A static page with information about the project.
- **`actions.ts`**: Contains all **Next.js Server Actions**. These are the functions that run on the server when you submit a form. They handle data validation and call the AI flows.
- **`layout.tsx` (root)**: The main layout for the entire app. It sets up the HTML structure, fonts, and global providers.
- **`globals.css`**: Defines the color theme (CSS variables) for ShadCN and other global styles.

#### `src/ai/` - The AI Brains

- **`genkit.ts`**: Initializes the Genkit `ai` object, configuring it to use the Google AI plugin.
- **`flows/`**: This directory holds all the Genkit AI flows.
    - `perform-scan.ts`: The main AI flow that simulates a comprehensive security scan.
    - `suggest-exploits-for-cve.ts`: Powers the CVE lookup tool.
    - `suggest-exploits-for-service.ts`: Powers the exploit finder tool.
    - `security-chat-flow.ts`: Powers the conversational AI on the chat page.

#### `src/components/` - Reusable UI Elements

- **`ui/`**: A large collection of **ShadCN UI components** like `Button`, `Card`, `Table`, `Input`, `Sidebar`, etc. These are the building blocks of the user interface.
- **`icons.tsx`**: Contains custom SVG icons used in the app, such as the logo.

#### `src/hooks/` & `src/lib/` - Helpers and Utilities

- **`hooks/use-mobile.tsx`**: A custom hook to detect if the app is being viewed on a mobile device.
- **`hooks/use-toast.ts`**: Logic for showing toast notifications.
- **`lib/utils.ts`**: General utility functions, most importantly the `cn` function for merging Tailwind CSS classes.

#### Root Configuration Files

- **`package.json`**: The manifest for this Node.js project. It lists all dependencies and scripts needed to run the application (like `npm run dev`).
- **`tailwind.config.ts`**: Configures Tailwind CSS, defining custom fonts, colors, and other theme settings.
- **`next.config.ts`**: Configuration file for the Next.js framework.
- **`tsconfig.json`**: Configures the TypeScript compiler.
