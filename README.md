# ArchSearch
ArchSearch is an advanced academic research tool that can find papers and professors relevant to your topic. It breaks down complex research with summaries and questions to elevate your education. It also generate simulation code depending on what you upload in the pdf upload section. 

# FEATURES
 Paper & Professor Discovery - An agent create search terms based of your input and finds paper and professors using OpenAlex based off the terms that were created. OpenAlex is a database with thousands of research papers/professors. 
 
Interactive Debate Agent - Based off the research paper you uploaded, this agent asks questions regarding the research paper, ensuring you have a better understanding of the paper uploaded.

PDF Uploader and Smart Summary - This agent applies keywords to handle particular pages because of token limits, and it leverages those keywords to filter the PDF papers before generating a summary derived from the filtered papers.

Simulation Studio - Creates computational models and python simulation code based on the literature that has been uploaded in the pdf uploader and the parameter bounds.

Reliability & Confidence Scoring - It gives you an overall score on papers for their methodological rigor, automated checks, and auto-generated APA citations.

# Tech Stack
Backend: Flask, Flask-CORS
LLM: Qwen2.5-7B-Instruct, served through **Featherless AI**
Paper/professor data: OpenAlex API
PDF parsing: PyMuPDF
Frontend: Single HTML file (vanilla JS, no framework)

# Prerequisites
Python 3.10+ recommended
A Featherless AI API key
An OpenAlex API key (optional for basic use, but recommended to avoid rate limits)

## To get started:
1. Clone/download repository.
2. Download the libraries in the requirement.txt or "$ pip install -r requirements.txt"
3. In workflow.py, add your api key in "API_KEY. "
4. Run your cmd in the folder of the scripts.
5. Type in your cmd python -m workflow.py for the backend to run.
6. Go to your frontend (index), and click on it or open up localhost:5000.
7. If localhost does not work for you, you can click on the html file inside templates which would open it up.

## How this works:
- Flask connects the frontend to the backend, and every request goes through it before hitting any AI or external API.

Paper & Professor Discovery
- When you search something, Flask hands the input to the LLM (Qwen) and asks it to create 2–4 search terms.
- Those terms are passed to OpenAlex to find 5 relevant papers.
- Flask grabs the title, link, and authors for each result and formats them into paper/professor cards.
- The results come back as a JSON response, which the browser's JS unpacks and uses to populate the papers and professors lists.
  
PDF Uploader
- The browser sends the PDF to Flask.
- Flask gives it to PyMuPDF, which pulls out all the text.
- That text is stored in a global variable on the server (PDF_STORAGE), which is used by the debate agent.
- Flask sends the first 3,000 characters of that text to the LLM twice — once for a summary, once for an opening debate question.
- Both results are shown on the browser: the summary box and the debate box.
  
Debate Agent
- When a message is sent, it's passed to Flask.
- Flask checks if there's any value in PDF_STORAGE.
- If empty, Flask replies asking the user to upload a PDF first — no LLM call is made.
- If there's text, Flask takes the first 3,000 characters, adds the user's message, and sends both to the LLM with a "sharp academic debater" prompt.
- The LLM's reply is sent back to the browser and added to the chat window.
  
Simulation Agent
- Runs as part of the same search request as the paper finder — not a separate step.
- Once search terms come back from OpenAlex, Flask sends the original topic to the LLM with a prompt to write an executable Python script (using numpy and matplotlib) that models or simulates that topic.
- The LLM's response usually comes wrapped in markdown code fences, so Flask strips those with a few regex passes to get raw code.
- The code is shown directly in the simulation box on the frontend — it isn't executed anywhere, just displayed for the user to copy and run themselves.
- Currently based only on the search topic, not on the actual papers found or the PDF text in PDF_STORAGE — so it isn't literature-aware yet, even though that's the goal.

# NOTE
The PDF summary and debate agent only read the first 3,000 characters of the extracted text, to keep API costs down given limited credits. If you have more credits available, you can increase this limit in workflow.py (search for extracted_text[:3000]) for more complete summaries and debate context on longer documents.
