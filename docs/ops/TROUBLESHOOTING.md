# Troubleshooting Guide

## Overview

This guide provides solutions to common issues encountered when running the Stellar Tip Jar backend.

---

## Common Issues

---

### Issue: Service Won't Start

**Symptoms:**
- Application crashes on startup
- Port already in use error

**Diagnosis:**
```bash
lsof -i :8000
journalctl -u tipjar-backend -n 50
```
**Solution**:
```bash
# Kill conflicting process
kill -9 <PID>

# Or change port
export PORT=8001
```

### Issue: Database Connection Failure

**Symptoms**:
- API returns 500 errors
- Logs show connection errors

**Diagnosis**:
```bnash
psql $DATABASE_URL -c "SELECT 1"
```
**Solution**:
- Verify `DATABASE_URL`
- Ensure PostgreSQL is running:
```bash
systemctl status postgresql
systemctl restart postgresql
```

### Issue: Migration Errors

**Symptoms**:
- App fails after schema change
- sqlx errors during startup

**Diagnosis**:
```bash
sqlx migrate info
```
**Solution**:
```bash
sqlx migrate run
```

If needed:
```bash
sqlx migrate revert
```

### Issue: High Memory Usage
**Symptoms**:
- Memory usage > 90%
- Process crashes

**Diagnosis**:
```bash
free -h
ps aux --sort=-%mem | head
```
**Solution**:
- Restart service:
```bash
systemctl restart tipjar-backend
```
- Optimize queries or reduce load

### Issue: Slow API Responses
**Symptoms**:
- High latency
- Timeouts

**Diagnosis**:
- Check database performance
- Check Horizon API latency

**Solution**:
- Optimize queries
- Add indexes
- Reduce heavy operations
- Cache repeated queries


### Issue: Stellar Horizon API Failure
**Symptoms**:
- Transaction failures
- External API errors

**Diagnosis**:
```bash
curl https://horizon-testnet.stellar.org/
```

**Solution**:
- Verify network connectivity
- Retry requests
- Use fallback Horizon endpoints

## Debug Commands

### Check Service
```bash
systemctl status tipjar-backend
journalctl -u tipjar-backend -f
```

### Check Database
```bash
psql $DATABASE_URL
```

### Check Logs
```bash
tail -f /var/log/syslog
```

## General Debug Tips
- Always check logs first
- Verify environment variables
- Test dependencies independently
- Restart services when necessary

