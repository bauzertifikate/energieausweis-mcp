# Energieausweis Express — Official MCP Server

Official [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server for **[Energieausweis Express](https://energieausweis-express.de)** (by [Bauzertifikate UG](https://bauzertifikate.de)).

Let AI assistants like **Claude**, **Claude Code**, **Cursor**, or any Streamable HTTP MCP client prepare an official German **Energieausweis** (*Verbrauchsausweis* under GEG / GModG 2027) directly from a property address and heating/energy bills — backed by official German 3D cadastre building models (LoD2) and reviewed & signed by a qualified German *Energieberater* (§ 88 GEG/GModG) with official DIBt registration.

> **Human-in-the-loop by design:** No MCP tool can place an order or charge payment. The assistant prepares a draft order and sends a confirmation email to the property owner, who reviews all prefilled values on `energieausweis-express.de`, adds required photos, and explicitly places the order themselves.

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
3. Enable the connector in your chat and upload your heating bills (*Heizkostenabrechnungen* / gas / oil / district heating bills).

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

### Example Prompt (German)
> *"Bereite mir bitte einen Verbrauchsausweis für mein Wohnhaus in der Musterstraße 5, 50667 Köln vor. Hier sind meine Gasabrechnungen der letzten drei Jahre."*

---

## Available Tools (11 Tools)

All tools declare explicit MCP safety annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`).

| Tool | Read-Only | Purpose |
| :--- | :---: | :--- |
| `check_eligibility` | Yes | Checks whether a *Verbrauchsausweis* is legally permitted under GEG / GModG 2027 (including the pre-1977 rule and Jan 1, 2027 transition), returning the all-inclusive EUR price, delivery time, and required documents. |
| `find_building` | Yes | Looks up a German address in official 3D building data (LoD2) and returns candidate buildings (`building_ref`, type, footprint, cadastre function). |
| `start_order` | No | Creates a non-binding draft order for the confirmed `building_ref`. Returns scoped `order_id`, private `order_token`, 3D-prefilled estimates, and open points. |
| `update_order` | No | Enters or updates building facts, heating system details, and billing periods in original bill units (`kWh`, `m³`, `l`, `kg`). |
| `add_document` | No | Uploads energy bills or photos (JPG, PNG, WebP, PDF, base64, max 10 MB, up to 20 files per order). |
| `review_order` | Yes | Returns completeness status, open points, legal eligibility, final price, and preliminary energy rating once consumption data is complete. |
| `request_confirmation` | No | Emails the property owner a 7-day link to review all assistant-entered data, upload photos, and decide whether to order. Requires explicit user consent first. |
| `get_status` | Yes | Checks order status in plain German (prepared, ordered, under Energieberater review, open inquiry, issued). |
| `answer_inquiry` | No | Submits user-confirmed answers or files to an Energieberater's follow-up question (*Rückfrage*). |
| `get_certificate` | Yes | Returns 1-hour download links for the issued Energieausweis PDF and invoice PDF. |
| `get_listing_facts` | Yes | Returns ready-to-paste German mandatory real-estate ad disclosures (*Pflichtangaben nach § 87 GEG/GModG*) once issued. |

---

## Scope & Legal Notes
- **Coverage:** Residential buildings (*Wohngebäude*) in Germany (*Verbrauchsausweis*).
- **Bedarfsausweis & Commercial:** Owners needing an online *Bedarfsausweis* (from photos, without on-site appointment) or a commercial/mixed-use certificate can order directly at [energieausweis-express.de](https://energieausweis-express.de).
- **Issuance:** Every certificate is manually checked and signed by a qualified *Energieberater* (§ 88 GEG/GModG), officially registered with the **DIBt**, and valid for 10 years.
- **Privacy & Legal:** [Datenschutz](https://energieausweis-express.de/datenschutz) · [Kundeninformationen](https://energieausweis-express.de/kundeninformationen) · [Impressum](https://energieausweis-express.de/impressum)
