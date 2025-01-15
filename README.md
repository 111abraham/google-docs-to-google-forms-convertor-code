
# Doc to Forms

Doc to Forms is a Google Apps Script project that automates the process of generating Google Forms from a Google Docs file. This tool processes the content of a Google Doc, identifies questions and corresponding choices, and creates a structured Google Form.

---

## 🚀 Features

- Automatically detect and parse questions from a Google Doc.
- Supports **closed-ended** and **open-ended** question formats.
- Adds multiple-choice or paragraph text fields to the Google Form.
- Logs progress for easier debugging.
- Skips irrelevant or empty paragraphs in the document.

---

## 🛠️ Installation & Usage

### Prerequisites
- A **Google Account** with access to Google Drive.
- Basic knowledge of Google Apps Script.

### Setup Instructions
1. Open [Google Apps Script](https://script.google.com/).
2. Create a new Apps Script project.
3. Copy and paste the following code into the script editor:

   
# Doc to Forms Script

This script creates a Google Form from a Google Doc by extracting questions and choices based on specific formatting rules.

---

## Code

```javascript
function createFormFromDoc() {
  const docId = "REPLACE_WITH_YOUR_DOC_ID"; // Replace with your document ID
  const doc = DocumentApp.openById(docId);
  const body = doc.getBody();
  const paragraphs = body.getParagraphs();

  const form = FormApp.create("Generated Form");
  let currentQuestion = null;
  let choices = [];
  let isOpenEnded = false;

  paragraphs.forEach(paragraph => {
    const text = paragraph.getText().trim();
    Logger.log("Processing paragraph: " + text);

    if (!text) return; // Skip empty lines

    // Detect section headers or standalone text
    if (text.match(/^\w+:/)) {
      Logger.log("Skipping section header: " + text);
      return;
    }

    // Detect questions
    if (text.endsWith("?") || text.toLowerCase().includes("(closed-ended)") || text.toLowerCase().includes("(open-ended)")) {
      Logger.log("Detected question: " + text);

      // Add the previous question to the form
      if (currentQuestion) {
        if (isOpenEnded) {
          form.addParagraphTextItem().setTitle(currentQuestion);
        } else if (choices.length > 0) {
          const uniqueChoices = [...new Set(choices)];
          form.addMultipleChoiceItem().setTitle(currentQuestion).setChoiceValues(uniqueChoices);
        }
        Logger.log("Added question: " + currentQuestion);
      }

      // Start a new question
      currentQuestion = text;
      choices = [];
      isOpenEnded = text.toLowerCase().includes("(open-ended)");
    } else {
      // Process choices for the current question
      Logger.log("Detected choice: " + text);
      choices.push(text);
    }
  });

  // Add the last question
  if (currentQuestion) {
    if (isOpenEnded) {
      form.addParagraphTextItem().setTitle(currentQuestion);
    } else if (choices.length > 0) {
      const uniqueChoices = [...new Set(choices)];
      form.addMultipleChoiceItem().setTitle(currentQuestion).setChoiceValues(uniqueChoices);
    }
    Logger.log("Added final question: " + currentQuestion);
  }

  Logger.log("Form created successfully! Access it here: " + form.getEditUrl());
}

```

 4. Replace the placeholder REPLACE_WITH_YOUR_DOC_ID with the Google Docs ID of your document.
 5. Save the project and authorize necessary permissions.
 6. Run the createFormFromDoc function from the Apps Script editor.

  Logger.log("Form created successfully! Access it here: " + form.getEditUrl());
}


🧑‍💻 Contributing
We welcome contributions! To contribute:

1.Fork the repository.
2. Create a feature branch: git checkout -b feature/your-feature-name.
3. Commit your changes: git commit -m "Add your feature".
4. Push to the branch: git push origin feature/your-feature-name.
5. Open a pull request.

🐛 Troubleshooting
Blank Form Issue
If the generated form is blank:

Ensure your document follows the supported formats for questions and choices.
Check the Google Doc for empty or improperly formatted paragraphs.
Verify the document ID in the script.
Duplicate Choices Error
The script automatically handles duplicate choices. Ensure your document doesn't have repeating choices.

📜 License
This project is licensed under the MIT License. See the LICENSE file for details.

📧 Contact
For questions or feedback, please reach out via GitHub Issues.

Example Output
Here’s an example of a Google Doc input and the resulting form:

Google Doc Input

What is your favorite fruit? (Closed-ended)
- Apple
- Banana
- Orange

Describe your perfect day. (Open-ended)

Generated Google Form
What is your favorite fruit?
(Multiple Choice)

Apple
Banana
Orange
Describe your perfect day.
(Paragraph Text)

Happy automating! 😊
