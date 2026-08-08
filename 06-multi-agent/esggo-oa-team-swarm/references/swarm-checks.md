# Swarm Checks

## Single command

```bash
ssh -i ~/.ssh/esggo_original ubuntu@161.118.252.147 "cd /opt/esggo && agents-cli swarm start --agents=30"
```

## Expected output shape

Success indicators:
- 5 teams initialized, 6 agents each
- Soul anthem banner printed
- Status line `READY TO EXECUTE`

Failure indicators:
- command not found
- permission/install errors
- non-zero exit before squad counts

## Return protocol

Ask the user for exactly:
- `成功`, or
- `失敗 + 錯誤訊息`

Do not request screenshots, full terminal dumps, or repeated same-shape evidence.
If an inspection step is still needed, ask for one specific command output only.