# ST-GCN (MMAction2) Finetuned in Golf Swing, HTTP API (bocker060/mmaction2-api)

GPU-enabled Docker image exposing an HTTP API that consumes `skeleton2d.csv` (COCO-17 order) and returns embeddings/predictions. 

This mmaction2 (stgcn) provides inference for a single skeleton. For multi-file training and testing, please visit other repos and images.

get skeleton2d.csv file in https://github.com/ChanGyu-Cho/openpose-api (using openpose coco-17 model)

---

## 🚀 Quickstart

```bash
docker run -d --gpus all -p 19031:19031 --name mmaction2-api bocker060/mmaction2-api
# Optional: ngrok
ngrok config add-authtoken <YOUR_TOKEN>
ngrok http 19031
```

# 🔌 Endpoint
POST /mmaction_stgcn_test
Base URL (default): http://localhost:19031/mmaction_stgcn_test

Request (JSON)
Field	Type	Required	Description
csv_base64	string	✅	Base64 of the entire skeleton2d.csv (UTF-8)

# Response 
```json
✅ API 요청 성공 (처리 시간: 2.83초)
   num_samples: 1
   predictions (first 3): [true]
   predictions_json: [true]
   accuracy: not provided (inference-only)
```

# 🧪 Client example
```bash
python mmaction-client.py
```
Does:

Read skeleton2d.csv → base64 encode → POST

Robust timeouts / error messages

Prints num_samples, preview of predictions, and accuracy (if provided)

PowerShell one-liner:

```powershell
$csv = Get-Content .\skeleton2d.csv -Raw
$b = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($csv))
curl -X POST "http://localhost:19031/mmaction_stgcn_test" `
  -H "Content-Type: application/json" `
  -d (@{csv_base64=$b} | ConvertTo-Json)
```

🧱 Pipeline (context)
```lua
[Images] --(OpenPose API)--> skeleton2d.csv
skeleton2d.csv --(MMAction2 API)--> predictions/embeddings
``` 

# 🛠️ Troubleshooting
HTTP 4xx/5xx: docker logs mmaction2-api

Large CSV(>~10MB): client warns; consider segmenting long sequences

Schema: Ensure COCO-17 columns: Nose_x, Nose_y, Nose_c, LEye_x, ..., RAnkle_c

# 🇰🇷 한글 요약
실행: docker run -d --gpus all -p 19031:19031 --name mmaction2-api bocker060/mmaction2-api

엔드포인트: POST /mmaction_stgcn_test (CSV base64 입력 → 예측 JSON)

클라이언트: mmaction-client.py 실행해 결과 확인
