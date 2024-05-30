# Repositorium.ch GPT

Als Spielerei haben wir die [öffentliche API](https://repositorium.ch/api) des Repositorium.ch an einen OpenAI GPT angeschlossen ([Link zum Repositorium.ch GPT im GPT-Store](https://chatgpt.com/g/g-R4HEHP23T-repositorium-ch)).

## Das Repositorium.ch als Funktion an ein GPT schliessen
Die Repositorium.ch Suche kann in wenigen einfachen Schritten in einen GPT gebaut werden. 
1. GPT erstellen
2. Prompt konfigurieren (den im Repositorium.ch verwendeten Prompt finden Sie weiter unten)
3. Unter `Actions` eine neue Funktion hinzufügen
4. Die Suche brauch keinen `Authentication` header, da das Repositorium.ch vollständig offen betrieben wird.
5. Das Repositorium.ch OpenAPI Schema angeben (das Schema finden Sie weiter unten)
6. Auf die Datenschutzerklärung des Repositorium.ch verlinken.
7. Unter `Available actions` kann die `askQuestion` Funktion nun getestet werden.
8. Speichern und GPT verwenden 🚀

## Prompt
Der Prompt kann selbstverständlich frei angepasst werden. Unser Prompt enthält noch Zusatzinformationen zur Gründung des Repositorium und versucht dem GPT klar anzuweisen, dass die Quellen verlinkt werden müssen. Der Prompt kann aber sicherlich noch genauer und besser formuliert werden (Pull-Requests sind selbstverständlich auch hier sehr willkommen!)

```bash
Deine Aufgabe ist es im Repositorium.ch zu suchen. Dafür hast Du direkten Zugang zum Fachrepositorium zum Schweizer Recht: Repositorium.ch. Die Plattform ist eine zentrale, frei und kostenlos zugängliche, schweizweite und schweizbezogene, institutionenunabängige und fachbezogene Plattform, die vom Verein Repositorium.ch betrieben wird. Das Projekt startete am Open Legal Lab 2022 und wurde am Open Legal Lab 2024 in den Release gegeben. 

Du beantwortest Fragen aus dem Schweizerischen Recht und verwendest dafür ausschliesslich die Informationen von der Suchmaschine Repositorium.ch. Wenn du etwas nicht beantworten kannst sagst Du immer: Das kann ich leider nicht beantworten. Aber wussten Sie, dass ... (hier kannst du dann einen Fakt aus den Resultaten angeben, den du interessant findest).

Füge Deiner Antwort IMMER die Quelle und Autorennamen an. Du musst zwingend eine Suche durchführen. Wenn nicht klar ist, was gesucht wird, versuche mit allgemeinen rechtlichen Konzepten, die sich aus der Anfrage ableiten lassen zu suchen.

###
Beispiel eines FAILURE: Direkte Antwort, ohne Suchresultate. 
Beispiel eines SUCCESS: Antwort, wofür mindestens eine Suche (idealerweise auch mehrere) durchgeführt wurdenn.
###
```

## OpenAPI Schema
```json
{
  "openapi": "3.0.0",
  "info": {
    "title": "Katie API",
    "description": "API for asking questions within a specific domain.",
    "version": "1.0.0"
  },
  "servers": [
    {
      "url": "https://app.katie.qa/api/v3",
      "description": "Main API server"
    }
  ],
  "paths": {
    "/ask/20275809-8c17-4f3d-a3f7-0657c1435e54": {
      "post": {
        "operationId": "askQuestion",
        "summary": "Asks a question in a specific domain.",
        "parameters": [
          {
            "name": "domain-id",
            "in": "path",
            "required": true,
            "description": "The ID of the domain to ask the question in.",
            "schema": {
              "type": "string"
            }
          },
          {
            "name": "limit",
            "in": "query",
            "required": false,
            "description": "Limit the number of returned answers, e.g. return 10 answers",
            "schema": {
              "type": "integer",
              "format": "int32",
              "default": 5
            }
          },
          {
            "name": "offset",
            "in": "query",
            "required": false,
            "description": "Pagination: Offset indicates the start of the returned answers, e.g. 0 for starting with the answer with the best ranking, whereas 0 also the default",
            "schema": {
              "type": "integer",
              "format": "int32",
              "default": 0
            }
          }
        ],
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "properties": {
                  "question": {
                    "type": "string",
                    "description": "The question to ask."
                  },
                  "includePayloadData": {
                    "type": "boolean",
                    "default": false
                  }
                }
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Question successfully submitted and processed.",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "answer": {
                      "type": "string",
                      "description": "The answer to the submitted question."
                    }
                  }
                }
              }
            }
          },
          "400": {
            "description": "Invalid request, such as missing required parameters."
          },
          "500": {
            "description": "Internal server error."
          }
        }
      }
    }
  }
}
```

