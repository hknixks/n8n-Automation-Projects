# n8n-automation-projects/
├── workflows/
│   ├── [01_weather_check_telegram.json](https://github.com/user-attachments/files/28644079/01_weather_check_telegram.json)
│   ├── 02_save_tweets_to_sheets.json (https://github.com/user-attachments/files/28643947/02_save_tweets_to_sheets.json)
│   ├── 03_daily_quote_telegram.json (https://github.com/user-attachments/files/28643939/03_daily_quote_telegram.json)
│   ├── 04_log_emails_to_sheets.json (https://github.com/user-attachments/files/28643940/04_log_emails_to_sheets.json)
│   ├── 05_webhook_alert_telegram.json (https://github.com/user-attachments/files/28643944/05_webhook_alert_telegram.json)
│   └── 06_email_triage_ai_draft.json
└── README.md



{
  "name": "weather check telegram test",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "hours",
              "hoursInterval": 1
            }
          ]
        }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [-400, 0],
      "id": "a1b2c3d4-0001-0001-0001-000000000001",
      "name": "every hour trigger"
    },
    {
      "parameters": {
        "url": "https://wttr.in/Lagos?format=j1",
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [-160, 0],
      "id": "a1b2c3d4-0001-0001-0001-000000000002",
      "name": "get weather"
    },
    {
      "parameters": {
        "chatId": "978442283",
        "text": "=Good morning! Weather in Lagos: {{$json[\"current_condition\"][0][\"weatherDesc\"][0][\"value\"]}} - Temp: {{$json[\"current_condition\"][0][\"temp_C\"]}}°C",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [80, 0],
      "id": "a1b2c3d4-0001-0001-0001-000000000003",
      "name": "send weather to telegram",
      "credentials": {
        "telegramApi": {
          "id": "hjzN3nSlgdErkRe3",
          "name": "Telegram account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "every hour trigger": {
      "main": [[{ "node": "get weather", "type": "main", "index": 0 }]]
    },
    "get weather": {
      "main": [[{ "node": "send weather to telegram", "type": "main", "index": 0 }]]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "b1234567-aaaa-bbbb-cccc-000000000001",
  "meta": {
    "templateCredsSetupCompleted": false,
    "instanceId": "7ace36949c4ebad6bf7453d077accc0757a1271b92ba46964f30ec1b75747f86"
  },
  "id": "wf0000000000001",
  "tags": []
}
