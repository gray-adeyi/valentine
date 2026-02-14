# 💘 Valentine Questionnaire

A delightful, interactive web application built with **Svelte 5** and **Vite** to ask that special someone to be your Valentine. 

This project allows you to create a personalized link for your "lover," guiding them through a playful, branching questionnaire involving GIFs, music, and confetti. Responses are stored locally, emphasizing privacy and a personal touch.

## ✨ Features

- **Personalized Experience**: Encode your partner's name into the URL for a custom greeting.
- **Interactive Storytelling**: A branching questionnaire with playful "Yes/No" options.
    - *Note*: The "No" path tries to guilt-trip them back to "Yes" 😉.
- **Multimedia Elements**:
    - **Audio**: Plays romantic background music ("Serenade" tracks) upon acceptance.
    - **Visuals**: Animated GIFs for each step of the questionnaire.
    - **Confetti**: A celebration effect when they finally say "Yes"!
- **Privacy-Focused**: All data and conversation history are stored in the browser's `localStorage`. No external database is used.
- **Responsive Design**: Looks great on both mobile and desktop.
- **Modern Tech Stack**: Built with the latest Svelte 5 (Runes) and TypeScript.

---

## 🚀 Getting Started

Follow these instructions to get a copy of the project up and running on your local machine.

### Prerequisites

- **Node.js** (v16 or higher recommended)
- **npm** (comes with Node.js)

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/gray-adeyi/valentine.git
    cd valentine
    ```

2.  **Install dependencies:**
    ```bash
    npm install
    # or if using pnpm/yarn/bun
    # pnpm install
    # yarn install
    # bun install
    ```

### Running Locally

Start the development server:

```bash
npm run dev
```

Open your browser and navigate to the URL shown in the terminal (usually `http://localhost:5173`).

---

## 📖 Usage Guide

How to use the application to ask your Valentine:

### 1. Create a Personalized Link
You need to generate a link that includes your partner's name.

1.  Open the app in your browser (e.g., `http://localhost:5173`).
2.  Add `?q=encode<Name>` to the URL.
    -   *Example*: `http://localhost:5173/?q=encodeJuliet`
3.  The app will automatically redirect you to a new URL with a Base64 encoded string.
    -   *Result*: `http://localhost:5173/?q=SnVsaWV0`
4.  **Copy this new URL.** This is the link you will send to your partner.

### 2. Send to Your Partner
Share the copied link with your special someone. When they open it, they will see a greeting with their name and can proceed through the questionnaire.

### 3. View Their Response
### 3. View Their Response
When your partner completes the questionnaire and reaches the final "Yes" screen:

1.  A **"Send My Reply 💌"** button will appear.
2.  Clicking this button will open **WhatsApp** with a pre-filled message containing their responses.
3.  They just need to hit send to share their answers with you! 
    - *Note*: This works best on mobile devices with WhatsApp installed. On desktop, it may open WhatsApp Web.

*Alternative*: Reviewing answers locally
Since responses are stored in `localStorage`, you can also view the conversation log on their device if they haven't sent it yet:

1.  Ask them to visit the **Decode** link on the **same device/browser** they used to answer.
    -   URL: `http://localhost:5173/?q=decode`
2.  The app will display a modal with the conversation log (e.g., "Mi Amore: Yes, my love...").
3.  **Ask them to take a screenshot** of this modal and send it to you! 📸

---

## 📂 Project Structure

A quick look at the codebase structure:

```
valentine/
├── public/              # Static assets (images, audio files)
├── src/
│   ├── lib/
│   │   ├── components/  # Svelte components (e.g., QuestionaireCard.svelte)
│   │   └── utils/       # Utility functions (e.g., base64Parser.ts)
│   ├── App.svelte       # Main application component & routing logic
│   ├── main.ts          # Entry point
│   └── types.ts         # TypeScript type definitions
├── index.html           # HTML entry point
├── package.json         # Project dependencies and scripts
└── vite.config.ts       # Vite configuration
```

## 🛠️ Built With

-   [Svelte 5](https://svelte.dev/) - The cybernetically enhanced web apps framework (using Runes!).
-   [Vite](https://vitejs.dev/) - Next Generation Frontend Tooling.
-   [TypeScript](https://www.typescriptlang.org/) - Typed JavaScript.

## 🤝 Contributing

Feel free to fork this project and customize the questions, images, or music to make it even more special for your Valentine!

1.  Fork the Project
2.  Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3.  Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4.  Push to the Branch (`git push origin feature/AmazingFeature`)
5.  Open a Pull Request

---

*Made with ❤️ for Lover's Season.*