#n8n_test
n8n Starter Project: Daily Weather Report to Slack
This repository contains a simple, beginner-friendly n8n project designed to automatically fetch the daily weather forecast and post it to a Slack channel. It's a great way to learn the basics of n8n, including triggers, fetching data from an API, and sending notifications.

Project Goal
The objective of this workflow is to run automatically every morning, get the weather forecast for a specified city, format the information into a user-friendly message, and then post that message to a designated Slack channel.

Nodes Used
This workflow uses four fundamental nodes:

Cron: This node acts as the trigger. It is scheduled to start the workflow at a specific time every day (e.g., 7:00 AM).

OpenWeatherMap: This node connects to the OpenWeatherMap API to fetch current weather data for any city you choose.

Set: This node is used to manipulate data. Here, we use it to format the raw weather data into a clean, readable message for Slack.

Slack: This node sends the final message to your specified Slack channel.

Setup Instructions
Prerequisites
Before you start, you will need:

An active n8n instance (either on n8n.cloud or self-hosted).

A Slack workspace where you have permission to add apps.

An OpenWeatherMap API Key (you can get one for free from their website).

Step-by-Step Guide
Create Credentials:

In your n8n dashboard, go to the "Credentials" section on the left.

Add a new credential for OpenWeatherMap API and paste in your API key.

Add a new credential for Slack API. You'll be guided through the OAuth process to connect your Slack workspace.

Import the Workflow:

Create a new, blank workflow in n8n.

Copy the entire JSON content from the section below.

Paste it directly onto the blank canvas. The four nodes should appear and be connected.

Configure the Nodes:

Cron Node: Click on it and set the time you want the workflow to run every day (e.g., 0 7 * * * for 7 AM).

OpenWeatherMap Node:

Select the credential you just created from the dropdown.

In the "City" field, enter the name of the city for which you want the forecast (e.g., London).

Slack Node:

Select the Slack credential you created.

In the "Channel" field, choose whether to post to a public channel (e.g., #general) or a specific user.

The "Text" field is already configured by the Set node to use the weather data.

Activate the Workflow:

Save the workflow.

Toggle the "Active" switch at the top right of the screen to turn it on.

That's it! Your workflow will now run automatically at the scheduled time.

Workflow JSON
Copy the code below and paste it into your n8n canvas to import the complete workflow.

{
  "name": "Daily Weather Report to Slack",
  "nodes": [
    {
      "parameters": {
        "rule": "cron",
        "triggerAtTime": "0 7 * * *"
      },
      "name": "Cron",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1,
      "position": [
        450,
        300
      ]
    },
    {
      "parameters": {
        "authentication": "apiKey",
        "operation": "getCurrent",
        "location": "London",
        "options": {}
      },
      "name": "OpenWeatherMap",
      "type": "n8n-nodes-base.openWeatherMap",
      "typeVersion": 1,
      "position": [
        650,
        300
      ],
      "credentials": {
        "openWeatherMapApi": {
          "id": "YOUR_OPENWEATHERMAP_CREDENTIAL_ID",
          "name": "OpenWeatherMap account"
        }
      }
    },
    {
      "parameters": {
        "values": {
          "string": [
            {
              "name": "slackMessage",
              "value": "={{`Good Morning! ☀️ Here is your weather report for ${$json[\"name\"]}:\n\n*Weather:* ${$json[\"weather\"][0][\"main\"]} (${$json[\"weather\"][0][\"description\"]})\n*Temperature:* ${$json[\"main\"][\"temp\"]}°C (Feels like ${$json[\"main\"][\"feels_like\"]}°C)\n*Humidity:* ${$json[\"main\"][\"humidity\"]}%\n*Wind:* ${$json[\"wind\"][\"speed\"]} m/s`}}"
            }
          ]
        },
        "options": {}
      },
      "name": "Set Message",
      "type": "n8n-nodes-base.set",
      "typeVersion": 1,
      "position": [
        850,
        300
      ]
    },
    {
      "parameters": {
        "authentication": "oAuth2",
        "channel": "#general",
        "text": "={{$json[\"slackMessage\"]}}",
        "attachments": [],
        "otherOptions": {}
      },
      "name": "Slack",
      "type": "n8n-nodes-base.slack",
      "typeVersion": 1,
      "position": [
        1050,
        300
      ],
      "credentials": {
        "slackApi": {
          "id": "YOUR_SLACK_CREDENTIAL_ID",
          "name": "Slack account"
        }
      }
    }
  ],
  "connections": {
    "Cron": {
      "main": [
        [
          {
            "node": "OpenWeatherMap",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "OpenWeatherMap": {
      "main": [
        [
          {
            "node": "Set Message",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Set Message": {
      "main": [
        [
          {
            "node": "Slack",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}

Customization Ideas
Different Trigger: Instead of a Cron job, use a Webhook node to trigger the workflow with an HTTP request.

Multiple Cities: Use a Code node to loop through a list of cities and get the weather for each one.

Different Notification: Replace the Slack node with a Discord node, an Email node, or a Pushbullet node.
