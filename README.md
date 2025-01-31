# NBA GAMEDAY NOTIFICATION SYSTEM

## Overview

This project uses AWS Lambda function to fetch NBA game data from the SportsData.io API, formats the data, and publishes game updates to an AWS SNS topic. It provides details for scheduled, in-progress, and final games, including scores, last plays, and broadcasting channels.

## Architecture Diagram

![NBA Gameday Notification diagram](./images/snstopic.png)

## Features

- Fetches NBA game data for the current date
- Formats game data based on status (Scheduled, InProgress, or Final)
- Publishes formatted game updates to an AWS SNS topic
- Logs API responses for debugging
- Uses AWS EventBridge to schedule and trigger the Lambda function automatically

## Prerequisites

- An AWS account with permissions to use use Lambda, SNS, and EventBridge
- A SportsData.io API key
- An SNS topic for publishing updates

## Environment Variables

| Variable | Description |
|----------|-------------|
| `NBA_API_KEY` | API key for accessing SportsData.io |
| `SNS_TOPIC_ARN` | ARN of the SNS topic to publish game updates |

## Setup Instructions

### 1. Configure AWS Lambda

- Create a new AWS Lambda function
- Use a Python runtime (Python 3.x recommended)
- Add the necessary IAM permissions for Lambda to publish to SNS

### 2. Set Environment Variables

In the AWS Lambda console, set the following environment variables:

- `NBA_API_KEY`: Your SportsData.io API key
- `SNS_TOPIC_ARN`: The ARN of your SNS topic

### 3. Deploy the Function

- Upload your Python script as a Lambda function
- Ensure the necessary dependencies (`boto3`, `urllib`, `json`, `datetime`, `os`) are available in the execution environment

### 4. Configure AWS EventBridge for Scheduling

- Open the AWS EventBridge console
- Create a new rule
- Choose **Schedule** as the rule type
- Select recurring schedule as schedule type and cron-based schedule as schedule type
- Set the schedule expression (e.g., `cron(0 12 * * ? *)` for a specific time)
- Select the target as **AWS Lambda** and choose the deployed Lambda function
- Enable the rule to trigger the function at the specified schedule

## API Usage

This function fetches game data from:

```
https://api.sportsdata.io/v3/nba/scores/json/GamesByDate/{YYYY-MM-DD}?key={NBA_API_KEY}
```

## Function Flow

1. Retrieves the current date in Central Time (UTC-6)
2. Fetches NBA game data from the SportsData.io API
3. Formats the game data based on its status:
   - **Final**: Displays final scores and quarter breakdown
   - **InProgress**: Shows live scores and last play details
   - **Scheduled**: Provides game start time and broadcast channel
4. Publishes the formatted message to the specified AWS SNS topic
5. Logs responses and errors for debugging
6. **Scheduled Execution**: AWS EventBridge is used to trigger the Lambda function at a predefined schedule.

## Error Handling

- API request failures are caught and logged
- SNS publishing failures are handled and logged

## Example Output

```
Game Status: Scheduled
LAC vs CHA
Start Time: 2025-01-31T19:00:00
Channel: FDSS

---
Game Status: Scheduled
DAL vs DET
Start Time: 2025-01-31T19:00:00
Channel: KFAA

---

Game Status: InProgress
Bulls vs Celtics
Current Score: 78-85
Last Play: 3PT shot made by Jayson Tatum
Channel: TNT
```