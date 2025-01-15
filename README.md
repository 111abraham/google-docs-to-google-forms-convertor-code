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
