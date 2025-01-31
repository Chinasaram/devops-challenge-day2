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

## Prerequisites

- An AWS account with permissions to use Lambda and SNS
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