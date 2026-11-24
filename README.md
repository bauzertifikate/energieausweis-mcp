# Energieausweis Express — Official MCP Server

Official [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server for **[Energieausweis Express](https://energieausweis-express.de)** (by [Bauzertifikate UG](https://bauzertifikate.de)).

Let AI assistants like **Claude**, **Claude Code**, **Cursor**, or any Streamable HTTP MCP client prepare an official German **Energieausweis** — both **Verbrauchsausweis** (consumption-based, from heating/energy bills) and **Bedarfsausweis** (demand-based, completely online from building & heating facts + photos, with no U-values and no on-site appointment required) under GEG / GModG 2027 — backed by official German 3D cadastre building models (LoD2) and reviewed & signed by a qualified German *Energieberater* (§ 88 GEG/GModG) with official DIBt registration.

> **Human-in-the-loop by design:** No MCP tool can place an order or charge payment. The assistant prepares a draft order and sends a confirmation email to the property owner, who reviews all prefilled values on `energieausweis-express.de`, adds required photos on their phone, and explicitly places the order themselves.

---

## Quick Start (No Account or API Key Required)

- **Endpoint (Streamable HTTP):** `https://energieausweis-express.de/mcp`
- **Official MCP Registry ID:** `de.energieausweis-express/energieausweis`
- **Authentication:** None required (`start_order` returns a scoped `order_id` + private `order_token` valid only for that single order)
- **Human Documentation (DE):** https://energieausweis-express.de/ki-assistent

### Claude Code
```bash
claude mcp add --transport http energieausweis https://energieausweis-express.de/mcp
```

### Claude (Web / Desktop — Custom Connector)
1. Open **Settings → Connectors → Add custom connector**
2. Enter URL: `https://energieausweis-express.de/mcp`
3. Enable the connector in your chat and provide your address plus heating bills (for a *Verbrauchsausweis*) or building & heating details (for a *Bedarfsausweis*).

### Cursor / Windsurf / JSON MCP Config
```json
{
  "mcpServers": {
    "energieausweis-express": {
      "url": "https://energieausweis-express.de/mcp"
    }
  }
}
```

### Example Prompts (German)
- **Verbrauchsausweis:** *"Bereite mir bitte einen Verbrauchsausweis für mein Wohnhaus in der Musterstraße 5, 50667 Köln vor. Hier sind meine Gasabrechnungen der letzten drei Jahre."*
- **Bedarfsausweis:** *"Bereite mir bitte einen Bedarfsausweis für mein Einfamilienhaus (Baujahr 1972, Gasheizung von 2012 im Keller, Kunststofffenster 2-fach von 2015) in der Musterstraße 5, 50667 Köln vor."*

---

## Available Tools (11 Tools)

All tools declare explicit MCP safety annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`).

| Tool | Read-Only | Purpose |
| :--- | :---: | :--- |
| `check_eligibility` | Yes | Checks whether a *Verbrauchsausweis* and/or *Bedarfsausweis* is legally permitted under GEG / GModG 2027 (including the pre-1977 rule and Jan 1, 2027 transition), returning all-inclusive EUR prices, delivery time, and required inputs for both. |
| `find_building` | Yes | Looks up a German address in official 3D building data (LoD2) and returns candidate buildings (`building_ref`, type, footprint, cadastre function). |
| `start_order` | No | Creates a non-binding draft order (`certificate_type`: `verbrauch` or `bedarf`) for the confirmed `building_ref`. Returns scoped `order_id`, private `order_token`, 3D-prefilled estimates, open points, and (for *Bedarfsausweis*) dynamic `bedarf_questions` and `photo_tasks`. |
| `update_order` | No | Enters or updates building facts (including switching `certificate_type`), heating details, consumption periods in original bill units (`kWh`, `m³`, `l`, `kg`) for *Verbrauchsausweis*, or `bedarf` building/plant answers (emitters, generator/tank location, windows, PV/solar, etc. — no U-values) for *Bedarfsausweis*. |
| `add_document` | No | Uploads energy bills (`rechnung`) or photos — Verbrauchsausweis photos (`fassade_strasse`, `heizung`, `typenschild`) or Bedarfsausweis Hausrundgang photos (`fassade` with compass `sector`, `fenster`, `dachboden`, `kellerdecke`, `heizkoerper`, `treppenhaus`, plant photos). JPG, PNG, WebP, PDF, base64, max 10 MB, up to 20 files per order. |
| `review_order` | Yes | Returns completeness status, open points, legal & remote eligibility, final price, delivery timing, preliminary energy rating (*Verbrauchsausweis*) or preliminary class band (*Bedarfsausweis*), and remaining `bedarf_questions` / `photo_tasks`. |
| `request_confirmation` | No | Emails the property owner a 7-day link to review all assistant-entered data, take missing photos on their phone, and decide whether to order. Requires explicit user consent first. |
| `get_status` | Yes | Checks order status in plain German (prepared, ordered, under Energieberater review, open inquiry, issued). |
| `answer_inquiry` | No | Submits user-confirmed answers or files to an Energieberater's follow-up question (*Rückfrage*). |
| `get_certificate` | Yes | Returns 1-hour download links for the issued Energieausweis PDF and invoice PDF. |
| `get_listing_facts` | Yes | Returns ready-to-paste German mandatory real-estate ad disclosures (*Pflichtangaben nach § 87 GEG/GModG*) for either *Verbrauchsausweis* or *Bedarfsausweis* once issued. |

---

## Scope & Legal Notes
- **Coverage:** Residential buildings (*Wohngebäude*) in Germany — both **Verbrauchsausweis** and online **Bedarfsausweis** (calculated via DIN/TS 18599 from 3D LoD2 geometry, homeowner answers, and photos).
- **Commercial / Mixed-Use Buildings:** Commercial and mixed-use certificates can be ordered directly at [energieausweis-express.de](https://energieausweis-express.de).
- **Issuance:** Every certificate is manually checked and signed by a qualified *Energieberater* (§ 88 GEG/GModG), officially registered with the **DIBt**, and valid for 10 years.
- **Privacy & Legal:** [Datenschutz](https://energieausweis-express.de/datenschutz) · [Kundeninformationen](https://energieausweis-express.de/kundeninformationen) · [Impressum](https://energieausweis-express.de/impressum)
