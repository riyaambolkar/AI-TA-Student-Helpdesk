# LangChain based Resume to Job Filtering Pipeline
# AI-Powered Job Matcher Using LangChain and OpenAI

## Project Overview

This project is an AI-powered job matching tool that takes a candidate's resume as input, fetches real-time job postings from multiple companies, and uses a large language model to rank those jobs based on how well they match the candidate's skills and experience. The output is a ranked table with match scores, reasons for each match, missing skills, and clickable links to the actual job postings.

---
## Problem Statement

Job searching is a time-consuming and repetitive process. A candidate has to manually visit dozens of company career pages, read through each job description, and try to figure out which roles actually match their background. This process can take hours and still result in applying to jobs that are not a good fit, or missing roles that would have been a great match.

There is no centralized, intelligent tool that can read your resume and tell you, across multiple companies and job boards, which specific roles are most relevant to you and why.

---

## Solution
This notebook solves the problem in three steps.
First, it reads the candidate's resume as a PDF and extracts the text automatically.
Second, it hits the public APIs of two major applicant tracking systems, Greenhouse and Lever, to pull live job listings from over 25 well-known tech companies including Airbnb, Dropbox, Figma, Plaid, Twilio, Pinterest, and more.
Third, it sends both the resume text and the job listings to GPT-4o-mini and asks the model to rank each job by how well it fits the candidate, assign a match score from 0 to 100, explain why the job is a good fit, and identify any skills the candidate is missing for that role.
The result is a clean, ranked table displayed directly in the notebook with clickable links to apply.
## Use Case
This tool is built for job seekers in the tech industry, specifically those targeting software engineering, data engineering, machine learning, and related roles at companies that use Greenhouse or Lever as their hiring platform.
A student finishing their degree can upload their resume, enter their preferred role and location, and within a minute get a ranked list of the best-fit jobs across Airbnb, Dropbox, Plaid, and 20 other companies, without visiting a single career page manually.
The tool is also useful for career changers who want to understand where their current skills map to and what gaps they need to fill before applying.

---

## Why LangChain Was Used Instead of Directly Calling OpenAI

LangChain is a framework that makes it easier to build applications that use large language models as a component in a larger workflow, not just as a chatbot.

In this project, LangChain was used for two reasons.

The first reason is the tool decorator. LangChain provides a way to define Python functions as tools using the @tool decorator. This means the job-fetching functions, fetch_greenhouse_jobs and fetch_lever_jobs, can be described to the model in a structured way and invoked cleanly inside the pipeline. Without LangChain, you would have to manually wire the function calls and outputs together in a more fragile way.

The second reason is the ChatOpenAI wrapper. LangChain's ChatOpenAI class provides a clean abstraction for calling OpenAI models with proper message formatting, temperature control, and future compatibility if you want to swap the model out for a different provider like Anthropic or Mistral without rewriting the core logic.

LangChain treats the language model as one piece of a larger pipeline. In this project, the pipeline is: fetch data from external APIs, prepare it into a structured prompt, pass it to the model, and render the result as a formatted table. LangChain is designed for exactly this pattern.

---

## How This Is Different From Asking ChatGPT or Claude Directly

If you open ChatGPT or Claude and paste your resume and a job description, the model will give you feedback. That is useful but it is a manual, one-at-a-time process.

This project is different in the following ways.

It is automated. You do not paste anything manually. The notebook fetches live job data from real company APIs on its own and passes everything to the model without any manual copying or pasting.

It is real-time. ChatGPT and Claude are trained on data up to a certain date. The jobs in this tool are fetched live at the moment you run the notebook, so they reflect what is actually open right now at each company.

It is multi-source and comparative. Instead of analyzing one job description at a time, this tool sends up to 30 job listings simultaneously to the model and asks it to rank all of them relative to each other and relative to your resume. ChatGPT cannot do this without you doing all the data collection yourself.

It produces structured output. Rather than a paragraph of feedback, the output is a ranked, sortable data table with scores, reasons, and direct links, formatted specifically for decision-making and action.

It is a pipeline, not a conversation. ChatGPT and Claude are conversational interfaces meant for interactive back-and-forth. This project is a software application where the language model is embedded inside a data pipeline that has defined inputs, processing steps, and a structured output format. That is the fundamental difference between using AI as a chat product and building an AI-powered application using a framework like LangChain.

---

## API Key

You will be prompted to enter your OpenAI API key when running the notebook. The input is hidden like a password field using Python's getpass library. The key is stored only in memory for the duration of the session and is never written to the notebook file.
You can get an API key from platform.openai.com/api-keys.

---


## Dependencies

The following Python libraries are required and installed automatically in Cell 1.

langchain is the framework for building LLM-powered pipelines and defining tools.

langchain-openai is the LangChain connector for OpenAI models.

langgraph is the LangChain extension for agent graph workflows.

requests is used for making HTTP calls to the Greenhouse and Lever public APIs.

pypdf is used for extracting text from uploaded PDF resumes.

pandas is used for formatting and displaying the ranked results table.

---

## Supported Companies

Greenhouse platform: Airbnb, Dropbox, Reddit, Notion, Figma, Stripe, Coinbase, Duolingo, HubSpot, Asana, Zendesk, Twilio, Datadog, Snowflake, Pinterest, Squarespace
Lever platform: Netflix, Uber, Lyft, Atlassian, Canva, Shopify, Cloudflare, Plaid, Brex, Airtable, Retool, Loom

---
## How This Project Can Be Expanded
The current version is a working prototype. The following are natural directions for expanding its capabilities.
Adding more job boards: The Ashby API follows a similar structure to Greenhouse and Lever. Companies like Linear, Vercel, and Rippling use Ashby. Adding a fetch_ashby_jobs tool would significantly expand coverage with minimal additional code.
Adding resume tailoring: After ranking jobs, a second AI call could automatically rewrite the resume summary or bullet points to be more aligned with the top-ranked job description, using the same LangChain pipeline structure.
Adding email alerts: The tool could be scheduled to run daily and send an email notification when new jobs appear that score above a certain threshold, using Python's smtplib or a service like SendGrid.
Building a web interface: The notebook logic could be moved into a Flask or FastAPI backend with a simple frontend form, so users can upload their resume and get results through a browser without needing Google Colab at all.
Adding salary data: If salary information is available in the API response for certain companies, it could be included as an additional factor in the ranking logic so the model considers compensation alongside skill fit.
Storing results over time: Results could be saved to a local database or a Google Sheet so a user can track which jobs they have already seen, applied to, or received a response from, and avoid seeing the same postings repeatedly.
Supporting more resume formats: Currently only PDFs are supported. Adding support for Word documents using the python-docx library would make the tool accessible to more users.
Fine-tuning the ranking prompt: The system prompt sent to GPT-4o-mini could be made more specific by including the candidate's target salary range, preferred seniority level, or specific tech stack preferences, giving the model more context to produce a more accurate and personalized match score.
