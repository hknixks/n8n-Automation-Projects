# n8n-Automation-Projects[03_daily_quote_telegram.json](https://github.com/user-attachments/files/28643939/03_daily_quote_telegram.json)
[04_log_emails_to_sheets.json](https://github.com/user-attachments/files/28643940/04_log_emails_to_sheets.json)[02_save_tweets_to_sheets.json](https://github.com/user-attachments/files/28643947/02_save_tweets_to_sheets.json)
[01_weather_check_telegram.json](https://github.com/user-attachments/files/28643946/01_weather_check_telegram.json)
[05_webhook_alert_telegram.json](https://github.com/user-attachments/files/28643944/05_webhook_alert_telegram.json)

          ]
        }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [-460, 60],
      "id": "c3d4e5f6-0003-0003-0003-000000000001",
      "name": "7am every day"
    },
    {
      "parameters": {
        "url": "https://zenquotes.io/api/random",
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [-220, 60],
      "id": "c3d4e5f6-0003-0003-0003-000000000002",
      "name": "get random quote"
    },
    {
      "parameters": {
        "jsCode": "const item = $input.first().json[0];\nreturn [{ json: { message: `☀️ Good morning!\\n\\n\"${item.q}\"\\n\\n— ${item.a}` } }];"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [20, 60],
      "id": "c3d4e5f6-0003-0003-0003-000000000003",
      "name": "format the message"
    },
    {
      "parameters": {
        "chatId": "978442283",
        "text": "={{$json[\"message\"]}}",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [260, 60],
      "id": "c3d4e5f6-0003-0003-0003-000000000004",
      "name": "send to telegram",
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
    "7am every day": {
      "main": [[{ "node": "get random quote", "type": "main", "index": 0 }]]
    },
    "get random quote": {
      "main": [[{ "node": "format the message", "type": "main", "index": 0 }]]
    },
    "format the message": {
      "main": [[{ "node": "send to telegram", "type": "main", "index": 0 }]]
    }
  },
  "active": true,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "d3456789-aaaa-bbbb-cccc-000000000003",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "7ace36949c4ebad6bf7453d077accc0757a1271b92ba46964f30ec1b75747f86"
  },
  "id": "wf0000000000003",
  "tags": []
}

{
  "name": "log emails to sheet (testing)",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "minutes",
              "minutesInterval": 10
            }
          ]
        }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [-500, -20],
      "id": "d4e5f6a7-0004-0004-0004-000000000001",
      "name": "check every 10 mins"
    },
    {
      "parameters": {
        "operation": "getAll",
        "filters": {
          "readStatus": "unread",
          "receivedAfter": "={{ $now.minus(10, 'minutes').toISO() }}"
        },
        "options": {
          "format": "simple"
        }
      },
      "type": "n8n-nodes-base.gmail",
      "typeVersion": 2.2,
      "position": [-260, -20],
      "id": "d4e5f6a7-0004-0004-0004-000000000002",
      "name": "get new emails",
      "credentials": {
        "gmailOAuth2": {
          "id": "H251bT5mBZnWt3hJ",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "conditions": {
          "options": { "caseSensitive": false },
          "conditions": [
            {
              "leftValue": "={{ $json[\"from\"] }}",
              "rightValue": "noreply",
              "operator": { "type": "string", "operation": "notContains" }
            }
          ]
        }
      },
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2,
      "position": [-20, -20],
      "id": "d4e5f6a7-0004-0004-0004-000000000003",
      "name": "skip noreply emails"
    },
    {
      "parameters": {
        "operation": "append",
        "documentId": { "value": "YOUR_SHEET_ID_HERE", "__rl": true, "mode": "id" },
        "sheetName": { "value": "Emails", "__rl": true, "mode": "name" },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "From": "={{ $json[\"from\"] }}",
            "Subject": "={{ $json[\"subject\"] }}",
            "Date": "={{ $json[\"date\"] }}",
            "Snippet": "={{ $json[\"snippet\"] }}"
          }
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4.5,
      "position": [220, -20],
      "id": "d4e5f6a7-0004-0004-0004-000000000004",
      "name": "log to sheet",
      "credentials": {
        "googleSheetsOAuth2Api": {
          "id": "gsheets_cred_01",
          "name": "Google Sheets account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "check every 10 mins": {
      "main": [[{ "node": "get new emails", "type": "main", "index": 0 }]]
    },
    "get new emails": {
      "main": [[{ "node": "skip noreply emails", "type": "main", "index": 0 }]]
    },
    "skip noreply emails": {
      "main": [[{ "node": "log to sheet", "type": "main", "index": 0 }]]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "e4567890-aaaa-bbbb-cccc-000000000004",
  "meta": {
    "templateCredsSetupCompleted": false,
    "instanceId": "7ace36949c4ebad6bf7453d077accc0757a1271b92ba46964f30ec1b75747f86"
  },
  "id": "wf0000000000004",
  "tags": []
}

{
  "name": "webhook alert test v2",
  "nodes": [
    {
      "parameters": {
        "path": "my-alert-hook",
        "responseMode": "onReceived",
        "options": {}
      },
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2,
      "position": [-460, 40],
      "id": "e5f6a7b8-0005-0005-0005-000000000001",
      "name": "Webhook",
      "webhookId": "my-alert-hook-id-001"
    },
    {
      "parameters": {
        "conditions": {
          "options": { "caseSensitive": false },
          "conditions": [
            {
              "leftValue": "={{ $json.body.type }}",
              "rightValue": "",
              "operator": { "type": "string", "operation": "notEmpty" }
            }
          ]
        }
      },
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2,
      "position": [-220, 40],
      "id": "e5f6a7b8-0005-0005-0005-000000000002",
      "name": "check payload not empty"
    },
    {
      "parameters": {
        "jsCode": "const body = $input.first().json.body;\nconst msg = `🚨 Alert!\\nType: ${body.type || 'unknown'}\\nMessage: ${body.message || 'no message'}\\nTime: ${new Date().toLocaleString()}`;\nreturn [{ json: { alert: msg } }];"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [20, 40],
      "id": "e5f6a7b8-0005-0005-0005-000000000003",
      "name": "build alert message"
    },
    {
      "parameters": {
        "chatId": "978442283",
        "text": "={{$json[\"alert\"]}}",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [260, 40],
      "id": "e5f6a7b8-0005-0005-0005-000000000004",
      "name": "send alert to telegram",
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
    "Webhook": {
      "main": [[{ "node": "check payload not empty", "type": "main", "index": 0 }]]
    },
    "check payload not empty": {
      "main": [[{ "node": "build alert message", "type": "main", "index": 0 }]]
    },
    "build alert message": {
      "main": [[{ "node": "send alert to telegram", "type": "main", "index": 0 }]]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "f5678901-aaaa-bbbb-cccc-000000000005",
  "meta": {
    "templateCredsSetupCompleted": false,
    "instanceId": "7ace36949c4ebad6bf7453d077accc0757a1271b92ba46964f30ec1b75747f86"
  },
  "id": "wf0000000000005",
  "tags": []
}

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

{
  "name": "save tweets to sheet maybe",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "minutes",
              "minutesInterval": 30
            }
          ]
        }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [-500, 20],
      "id": "b2c3d4e5-0002-0002-0002-000000000001",
      "name": "Schedule Trigger"
    },
    {
      "parameters": {
        "url": "https://api.twitter.com/2/tweets/search/recent?query=n8n%20automation&max_results=10",
        "authentication": "genericCredentialType",
        "genericAuthType": "httpBearerAuth",
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [-260, 20],
      "id": "b2c3d4e5-0002-0002-0002-000000000002",
      "name": "search tweets about n8n",
      "credentials": {
        "httpBearerAuth": {
          "id": "twitter_bearer_01",
          "name": "Twitter Bearer"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "const tweets = $input.first().json.data || [];\nreturn tweets.map(t => ({ json: { id: t.id, text: t.text, saved_at: new Date().toISOString() } }));"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [-20, 20],
      "id": "b2c3d4e5-0002-0002-0002-000000000003",
      "name": "format tweets"
    },
    {
      "parameters": {
        "operation": "append",
        "documentId": { "value": "YOUR_SHEET_ID_HERE", "__rl": true, "mode": "id" },
        "sheetName": { "value": "Sheet1", "__rl": true, "mode": "name" },
        "columns": {
          "mappingMode": "autoMapInputData",
          "value": {}
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4.5,
      "position": [220, 20],
      "id": "b2c3d4e5-0002-0002-0002-000000000004",
      "name": "save to google sheet",
      "credentials": {
        "googleSheetsOAuth2Api": {
          "id": "gsheets_cred_01",
          "name": "Google Sheets account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Schedule Trigger": {
      "main": [[{ "node": "search tweets about n8n", "type": "main", "index": 0 }]]
    },
    "search tweets about n8n": {
      "main": [[{ "node": "format tweets", "type": "main", "index": 0 }]]
    },
    "format tweets": {
      "main": [[{ "node": "save to google sheet", "type": "main", "index": 0 }]]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "c2345678-aaaa-bbbb-cccc-000000000002",
  "meta": {
    "templateCredsSetupCompleted": false,
    "instanceId": "7ace36949c4ebad6bf7453d077accc0757a1271b92ba46964f30ec1b75747f86"
  },
  "id": "wf0000000000002",
  "tags": []
}
