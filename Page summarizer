= Simple Page Summarizer

A simple tool that provides a summary of the currently open web page in the form of a bookmarklet. The bookmarklet 
creates a half-screen overlay displaying the summary, with options to expand, close, or ask follow-up questions via a 
chat-like interface. Users generate the bookmarklet through a dedicated web page. The generated bookmarklet code is 
packaged into a draggable button for easy addition to the bookmarks bar.

== Overlay Behavior

=== Overlay UI Components

1. Half-screen overlay with adjustable size (via handle at the bottom).
2. Close button in the top corner to exit and remove the overlay.
3. Textarea to display the summary.
4. Chat-like interface below the summary for follow-up questions.

=== Functionality

1. On activation, the bookmarklet extracts web page content and sends it to the selected LLM service.
2. The LLM response populates the textarea.
3. Follow-up questions are sent via the chat-like interface, with responses appended in the textarea.
4. Closing the overlay cancels ongoing requests and removes the overlay.


= Dedicated Build Page

== Features

1. *LLM Service Selection:*
   - Dropdown or radio buttons to select an LLM service (e.g., locally hosted Ollama, ChatGPT, etc.).
   - Input fields for API keys or connection details, depending on the service.
   - A bookmarklet code is automatically generated to reflect the selected options.

2. *Drag to Bookmark Bar:*
   - A draggable button labeled "Drag to Bookmarks Bar."
   - The button contains the generated bookmarklet code as its URL.

3. *Preview and Test:*
   - Optional in-browser testing to simulate the overlay and ensure connectivity with the selected LLM.

= Future

The project has the potential to evolve into a standalone browser extension with the same functionality.
