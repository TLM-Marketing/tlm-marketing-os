# Marketing OS

A Claude Code project that turns your marketing knowledge into a content system. Open it in Claude Code or Claude Cowork, and it works as your marketing team's operating system — generating on-brand content, managing campaign briefs, and keeping everyone aligned.

## What This Is

A shared project (GitHub repo) that your marketing team works from. It contains:

- **Knowledge Base** — Everything your company knows: products, competitors, audiences, brand guidelines
- **Content Templates** — Proven structures for LinkedIn, X, and blog posts
- **Program Brief Workflow** — A step-by-step process for launching campaigns: brief → generate → feedback → final content
- **Sales Integration** — Sales team contributes field insights; marketing produces sales enablement content

## How to Use It

### 1. Open in Claude Code or Cowork
Clone this repo and open the folder in Claude Code (or add it as a project in Claude Cowork). Claude reads `CLAUDE.md` automatically and understands the entire system.

### 2. Set Up Your Company (First Time)
Say *"set up my company"* or just *"get started"*. Claude will walk you through onboarding — one question at a time:

1. **Company basics** — name, what you do, value proposition
2. **Products** — name, features, pricing for each product
3. **Competitors** — top 3-4 and how you position against them
4. **Target audiences** — ideal customer profile and key personas
5. **Brand voice** — tone, style, words to use and avoid, visual identity
6. **Sales insights** — common objections, what wins deals (optional)

Claude fills in the entire knowledge base from your answers. Takes about 10 minutes.

### 3. Start a Campaign
Tell Claude: *"I want to start a new campaign for [product/topic]."*

Claude will:
1. Walk you through a Program Brief
2. Read the relevant knowledge base files
3. Generate content drafts for each channel (with image prompts)
4. Ask for your feedback on each piece
5. Save the final approved content to the `output/` folder

### 4. Collaborate as a Team
Since it's a Git repo, your whole team can:
- Pull the latest knowledge base updates
- Add new briefs and content
- Sales updates `knowledge-base/sales/sales-knowledge.md` with field insights
- Everyone works from the same context

## Project Structure

```
marketing-os/
├── CLAUDE.md                          # System instructions
├── README.md                          # This file
├── knowledge-base/
│   ├── company/                       # Company knowledge
│   │   ├── product-knowledge.md
│   │   ├── competitive-analysis.md
│   │   └── target-audiences.md
│   ├── brand/                         # Brand & content
│   │   ├── brand-guidelines.md
│   │   └── content-templates/
│   │       ├── linkedin-post.md
│   │       ├── x-post.md
│   │       └── blog-post.md
│   └── sales/                         # Sales team inputs
│       └── sales-knowledge.md
├── products/                          # Product-specific knowledge
│   └── example-product/
│       └── product-brief.md
├── briefs/                            # Campaign briefs
│   └── _program-brief-template.md
└── output/                            # Generated content
    ├── content/
    └── sales-enablement/
```

## Pre-loaded Example

This project comes with example content for a fictional B2B SaaS company called **Flowdeck** — a go-to-market automation platform. Use it to see how the system works, then replace with your own company's information.

## Image Generation (Optional)

The system generates image prompts for every post by default. To also generate the images automatically, connect an image generation MCP server:

### Option A: FAL AI (Recommended — easiest setup)
```bash
claude mcp add --transport http fal-ai https://mcp.fal.ai/mcp --header "Authorization: Bearer YOUR_FAL_KEY"
```
Get your API key at [fal.ai](https://fal.ai). Supports 1,000+ models including Flux and SDXL.

### Option B: Replicate
```bash
claude mcp add replicate -- uvx mcp-server-replicate
```
Set `REPLICATE_API_TOKEN` in your environment. Get your key at [replicate.com](https://replicate.com).

### Option C: OpenAI DALL-E
```bash
claude mcp add openai-mcp -- npx -y @jezweb/openai-mcp
```
Set `OPENAI_API_KEY` in your environment.

### No MCP? No Problem
Without an MCP, the system still generates detailed image prompts with every post. Copy the prompt into any image tool (Midjourney, DALL-E, Ideogram, etc.).

## Requirements

- [Claude Code](https://claude.ai) (CLI or desktop) or Claude Cowork
- A GitHub account (for team collaboration)
- (Optional) An image generation MCP for automatic image creation

---

Built by [TLM.Marketing](https://tlm.marketing)
