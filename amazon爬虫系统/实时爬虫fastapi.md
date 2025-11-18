获取token：
```bash
curl -X 'POST' \ 'http://47.95.172.156:8000/token' \ -H 'Content-Type: application/json' \ -d '{ "username": "lsev_test", "password": "lsev0315." }'
```

获取实时商详数据：
```bash
curl -X GET "http://47.95.172.156:8000/get_realtime_product_details/?asin=B09N6KWZ1T&market_id=APJ6JRA9NG5V4" \
-H "Authorization: Bearer {{your_token}}"
```




