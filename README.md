https://hub.docker.com/r/bocker060/mmaction2-api

# ST-GCN (MMAction2) Finetuned in Golf Swing, HTTP API (bocker060/mmaction2-api)

GPU-enabled Docker image exposing an HTTP API that consumes `skeleton2d.csv` (COCO-17 order) and returns embeddings/predictions. 

This mmaction2 (stgcn) provides inference for a single skeleton. For multi-file training and testing, please visit other repos and images.

Get `skeleton2d.csv` from: https://github.com/ChanGyu-Cho/openpose-api (using OpenPose COCO-17 model)

---

## 🔐 Finetuned Checkpoint (Linux container path)

- Finetuned model: **`stgcn_70p.pth`**
- Your Windows local path example: `.\mmaction2\docker\api_src\stgcn_70p.pth`
- **Use a Linux path in the container**, e.g.:

```
/workspace/api_src/stgcn_70p.pth
```

### Mount at runtime (recommended)

```bash
docker run -d --gpus all -p 19031:19031 \
  -v /ABSOLUTE/HOST/PATH/stgcn_70p.pth:/workspace/api_src/stgcn_70p.pth:ro \
  -e MODEL_PATH=/workspace/api_src/stgcn_70p.pth \
  --name mmaction2-api bocker060/mmaction2-api
```

> If your API supports `MODEL_PATH`, set it via `-e MODEL_PATH=...`.  
> Otherwise keep the default internal path bundled in the image.

For fine-tuning and model editing details, please refer to the official **MMAction2** repository.

---

## 🚀 Quickstart

```bash
docker run -d --gpus all -p 19031:19031 --name mmaction2-api bocker060/mmaction2-api
# Optional: ngrok
ngrok config add-authtoken <YOUR_TOKEN>
ngrok http 19031
```

---

## 🔌 Endpoint

**POST** `/mmaction_stgcn_test`  
Base URL (default): `http://localhost:19031/mmaction_stgcn_test`

### Request (JSON)

| Field       | Type   | Required | Description                                  |
|------------|--------|:--------:|----------------------------------------------|
| `csv_base64` | string |   ✅     | Base64 of the entire `skeleton2d.csv` (UTF-8) |

---

## 🧾 Response (Example)

```json
{
  "result": {
    "num_samples": 1,
    "predictions": [true],
    "accuracy": null
  }
}
```

Sample client console preview:
```
✅ API 요청 성공 (처리 시간: 2.83초)
   num_samples: 1
   predictions (first 3): [true]
   predictions_json: [true]
   accuracy: not provided (inference-only)
```

---

## 🧪 Client example

```bash
python mmaction-client.py
```

Does:
- Read `skeleton2d.csv` → base64 encode → POST  
- Robust timeouts / error messages  
- Prints `num_samples`, preview of `predictions`, and `accuracy` (if provided)

**PowerShell one-liner**
```powershell
$csv = Get-Content .\skeleton2d.csv -Raw
$b = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($csv))
curl -X POST "http://localhost:19031/mmaction_stgcn_test" `
  -H "Content-Type: application/json" `
  -d (@{csv_base64=$b} | ConvertTo-Json)
```

---

## 🧱 Pipeline (context)

```lua
[Images] --(OpenPose API)--> skeleton2d.csv
skeleton2d.csv --(MMAction2 API)--> predictions/embeddings
```

---

## 🛠️ Troubleshooting

- **HTTP 4xx/5xx**: `docker logs mmaction2-api`
- **Large CSV(>~10MB)**: client warns; consider segmenting long sequences
- **Schema**: Ensure COCO-17 columns — `Nose_x, Nose_y, Nose_c, LEye_x, ..., RAnkle_c`

---

## 🇰🇷 한글 요약

- 실행: `docker run -d --gpus all -p 19031:19031 --name mmaction2-api bocker060/mmaction2-api`  
- 엔드포인트: `POST /mmaction_stgcn_test` (CSV base64 입력 → 예측 JSON)  
- 클라이언트: `mmaction-client.py` 실행해 결과 확인
