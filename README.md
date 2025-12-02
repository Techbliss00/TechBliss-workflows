# Automated AI Image Generation Workflow for n8n

![n8n Workflow Badge](https://img.shields.io/badge/n8n-Workflow-blue?style=flat&logo=n8n) ![AI-Powered](https://img.shields.io/badge/AI-Powered-green) ![Google Sheets Integration](https://img.shields.io/badge/Google%20Sheets-Integrated-orange)

This repository contains an n8n workflow JSON file (`image generation workflow (Abhishek Paswan).json`) designed to automate the creation of professional, vertical AI-generated images for corporate-tech social media content (e.g., LinkedIn posts). The workflow fetches new tasks from a Google Sheet, generates optimized image prompts using Google Gemini, renders images with the Flux model via Pollinations.ai, uploads them to Google Drive, and updates the sheet with shareable links.

Perfect for content creators, marketers, or teams managing high-volume social media visuals without manual design work.

## Workflow Overview

The workflow runs **daily at 10:00 AM** and processes rows marked as "New" in your Google Sheet. For each row:

1. **Read New Tasks**: Queries a specified Google Sheet for rows where `Status = "New"`.
2. **Loop Over Items**: Processes each row individually.
3. **Set Image Parameters**: Configures defaults like model (`flux`), width (`1080`), and height (`1920`) for vertical 9:16 format.
4. **Generate Prompt**: Uses an AI Agent with Google Gemini to craft a clean, professional prompt based on `Topic Title`, `Idea/Angle`, `LinkedInPost`, and `IncludeText` (or `Include_Image_Text`).
5. **Create Image**: Sends the prompt to Pollinations.ai API to generate the image (with fixed seed=42 for reproducibility).
6. **Upload to Drive**: Saves the image to a designated Google Drive folder.
7. **Update Sheet**: Appends the Drive shareable link to `Image for Social 1`, `Image for Social 2`, and `Image for Social 3` columns, then sets `Status = "Done"`.

### Prompt Generation Rules (Embedded in Workflow)
- **Style**: Minimal, modern, futuristic corporate-tech aesthetic (e.g., neural lines, glowing nodes, holographic shapes, smooth gradients).
- **Constraints**: One simple scene, no people (unless topic requires), no clutter, 20–35 words max.
- **Text Overlay**: Only if `IncludeText = "yes"` – short 1–2 word title (e.g., "A.I.") at bottom, perfectly readable, uppercase, centered.
- **Technical**: Soft lighting, crisp details, vertical composition.

Example Input Row (Google Sheet):
| Topic Title | Idea/Angle | IncludeText | Status |
|-------------|------------|-------------|--------|
| Edge AI in Wearables | Revolutionizing health tracking with real-time insights | yes | New |

Generated Prompt Example: "Abstract holographic neural network with glowing blue nodes and smooth gradients representing edge AI insights, vertical composition, soft lighting, 'EDGE AI' text at bottom."

## Prerequisites

- **n8n Instance**: Self-hosted or cloud (version 1.0+ recommended).
- **Credentials**:
  - Google Sheets OAuth2 API (for reading/updating the sheet).
  - Google Drive OAuth2 API (for uploading images).
  - Google Gemini (PaLM) API (for prompt generation; uses `gemini-2.5-pro` model).
- **Google Sheet Setup**:
  - Use this template: [Content Creation Sheet](https://docs.google.com/spreadsheets/d/1W4_9qdwUkNJv_MEJ2lfnEW2xaO3Fv5wUIkt9If_KHDY/edit?gid=0#gid=0).
  - Key Columns: `Topic Title`, `Idea/Angle`, `LinkedInPost`, `IncludeText` (or `Include_Image_Text`), `Status` (set to "New" for processing).
- **Google Drive Folder**: Create a folder named "image generation agent" (or update the workflow's `folderId` to match yours; current ID: `14OwuuBu8i5ZwBjplR7XawtUXpKuA160W`).

## Installation & Setup

1. **Import Workflow**:
   - In n8n, go to **Workflows > Import from File**.
   - Upload `image generation workflow (Abhishek Paswan).json`.
   - Connect your Google credentials in the nodes (e.g., "Google Sheets - Read New", "Upload file to Drive", "Google Gemini Chat Model").

2. **Customize Nodes** (Optional):
   - **Schedule Trigger**: Adjust `triggerAtHour` if not 10 AM.
   - **Google Sheets - Read New**: Update `documentId` and `sheetName` to your sheet.
   - **Fields - Set Values**: Modify `width`, `height`, or `model` (e.g., switch to another Pollinations model).
   - **AI Agent - Create Image From Prompt**: Tweak the system message for different styles.
   - **HTTP Request - Create Image**: API endpoint is fixed to Pollinations.ai; ensure `nologo: true` for clean outputs.
   - **Upload file to Drive**: Set your `driveId` and `folderId`.

3. **Test the Workflow**:
   - Add a test row to your sheet with `Status = "New"`.
   - Manually execute the workflow.
   - Verify: Image appears in Drive, sheet updates with link, status changes to "Done".

4. **Activate**:
   - Toggle the workflow to **Active** in n8n.
   - It will run automatically at the scheduled time.

## Node Breakdown

| Node Name | Type | Purpose | Position |
|-----------|------|---------|----------|
| Schedule Trigger | Schedule Trigger | Triggers daily at 10 AM | (-1216, -96) |
| Google Sheets - Read New | Google Sheets | Fetches rows where `Status = "New"` | (-976, -96) |
| Loop Over Items | Split in Batches | Processes each row in a loop | (-736, -96) |
| Fields - Set Values | Set | Sets image params (model, size) | (-512, -96) |
| AI Agent - Create Image From Prompt | LangChain Agent | Generates prompt via Gemini | (-320, -96) |
| Google Gemini Chat Model | Google Gemini | LLM for prompt creation | (-320, 128) |
| HTTP Request - Create Image | HTTP Request | Calls Pollinations.ai API | (-32, -96) |
| Upload file to Drive | Google Drive | Uploads generated image | (176, -96) |
| Update Sheet - Set Done | Google Sheets | Updates sheet with link & status | (400, -96) |

## Usage Examples

- **Corporate Tech Post**: Topic: "Quantum Computing Breakthroughs", Idea: "Entangled qubits unlocking new realities". IncludeText: "yes" → Generates abstract quantum visualization with "QUANTUM" overlay.
- **Marketing Campaign**: Batch multiple rows for a week's content; workflow handles one-by-one to avoid rate limits.

## Limitations & Troubleshooting

- **Rate Limits**: Pollinations.ai may throttle; add delays if processing many items.
- **Gemini Safety Settings**: Workflow disables blocks for harassment/hate/sexuality/danger – review for compliance.
- **Image Quality**: Fixed seed ensures consistency; test prompts manually at [Pollinations.ai](https://image.pollinations.ai).
- **Errors**:
  - Sheet not found? Check `documentId` URL.
  - API Fail? Verify credentials and retry (workflow has retry on HTTP fail).
  - No Text? Ensure `IncludeText` column exists and is "yes".

## Contributing

- Fork the repo and submit PRs for enhancements (e.g., add video generation, multi-model support).
- Report issues for bugs or feature requests.

## License

MIT License – Feel free to use, modify, and distribute.

## Credits

- Built by Abhishek Paswan.
- Powered by n8n, Google Workspace, Google Gemini, and Pollinations.ai.

---

*Last Updated: December 02, 2025*  
For support, open an issue or reach out via n8n community forums. 🚀
