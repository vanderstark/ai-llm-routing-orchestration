# OmniRoute Docker Deployment

A minimal Docker Compose setup for running **OmniRoute** — an LLM provider abstraction router — standalone (without Hermes Agent).

🌐 **Live Demo URL:** `http://localhost:20128`  
🔌 **API URL:** `http://localhost:20129/v1/*`

---

## 📦 Prerequisites

- [Docker](https://docs.docker.com/engine/install/) >= 20.10
- [Docker Compose](https://docs.docker.com/compose/) >= 2.20
- Git (optional, for cloning repo)

---

## 🚀 Quick Start Deployment

```bash
# 1. Clone the repository
git clone https://github.com/vanderstark/ai-llm-routing-orchestration.git
cd ai-llm-routing-orchestration

# 2. Copy example environment file
cp ./omniroute/.env.example ./omniroute/.env

# 3. Edit the .env file (Wajib diisi minimal AUTH_SECRET)
nano ./omniroute/.env
# At minimum set:
#   AUTH_SECRET=your-secret-key
#   LLM provider keys (GEMINI_API_KEY, CLAUDE_API_KEY, dll)

# 4. Start the services
docker compose up -d

# 5. Wait a few seconds, then check status
docker compose ps
```

---

## 🛠️ Konfigurasi `.env` (Wajib)

Salin dari `.env.example` dan isi nilai-nilai berikut:

| Variabel | Deskripsi | Contoh |
|----------|-----------|--------|
| `AUTH_SECRET` | Secret untuk mengamankan dashboard | `super-secret-123` |
| `DASHBOARD_AUTH_USER` | Username login dashboard | `admin` |
| `DASHBOARD_AUTH_PASS` | Password login dashboard | `password123` |
| `GEMINI_API_KEY` | Google Gemini API key | `AIza...` |
| `CLAUDE_API_KEY` | Anthropic Claude API key | `sk-ant-...` |
| `OPENROUTER_API_KEY` | OpenRouter API key | `sk-or-...` |
| `REDIS_URL` | Redis connection string | `redis://redis:6379` |
| `PORT` | Port dashboard | `20128` |
| `API_PORT` | Port API | `20129` |

🔒 **Penting:** `AUTH_SECRET` wajib diisi agar dashboard bisa dijalankan. Lupa = error start container.

---

## 📱 Akses Antarmuka

Setelah container berjalan, buka browser di:

| Fitur | URL | Keterangan |
|-------|-----|------------|
| **Dashboard Utama** | `http://localhost:20128` | Halaman utama OmniRoute, pilih provider & model |
| **API REST** | `http://localhost:20129/v1/models` | Daftar model yang tersedia |
| **API Chat** | `http://localhost:20129/v1/chat/completions` | Endpoint untuk kirim pesan ke LLM |
| **Redis CLI** | `redis-cli -h localhost -p 6379` | Cek Redis status (opsional) |

---

## 🎯 Fitur Utama OmniRoute

| Fitur | Deskripsi |
|-------|-----------|
| **Provider Abstraction** | Tulis satu prompt, jalankan ke Gemini, Claude, OpenAI, dll |
| **Router Smart** | Sistem otomatis memilih provider termurah/tercepat |
| **Dashboard UI** | Antantuka web untuk memilih model & melihat response |
| **Rate Limiting** | Terintegrasi dengan Redis untuk mencegah over-use |
| **WebSocket Live** | Streaming response real-time via port 20132 |
| **Cookie Providers** | Support untuk Gemini Web, Claude Web, dll (butuh profile Chromium) |

---

## 📚 Tutorial Penggunaan

### 1. **Cek Model yang Tersedia**

```bash
# Melalui curl
curl http://localhost:20129/v1/models

# Atau dari browser
open http://localhost:20128
```

### 2. **Kirim Pesan ke LLM**

```bash
curl -X POST http://localhost:20129/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-1.5-flash",
    "messages": [{"role": "user", "content": "Apa itu LQ45?"}],
    "max_tokens": 500
  }'
```

### 3. **Gunakan dari Python (Langsung ke API)**

```python
import requests

url = "http://localhost:20129/v1/chat/completions"
payload = {
    "model": "gemini-1.5-flash",
    "messages": [{"role": "user", "content": "Translate 'Selamat pagi' to English"}],
    "max_tokens": 100
}

response = requests.post(url, json=payload)
print(response.json()["choices"][0]["message"]["content"])
```

### 4. **Akses dari Hermes Agent (Jika Dibutuhkan)**

Konfigurasi Hermes untuk menggunakan OmniRoute sebagai provider:

```
provider_url: http://localhost:20129/v1/chat/completions
api_key: [AUTH_SECRET dari .env]
model: gemini-1.5-flash
```

---

## 🛠️ Troubleshooting

### Masalah Umum & Solusi:

| Masalah | Solusi |
|---------|--------|
| **Container stuck di restart** | Cek `AUTH_SECRET` di `.env` sudah diisi atau tidak |
| **401 Unauthorized di Dashboard** | Set `DASHBOARD_AUTH_USER` dan `DASHBOARD_AUTH_PASS` di `.env` |
| **Model tidak muncul** | Pastikan `GEMINI_API_KEY` atau kunci provider lain sudah benar |
| **Redis error** | Jalankan `docker compose restart redis` dan cek `redis-cli ping` |
| **Port already in use** | Change port di `.env` (contoh: `PORT=20127`) |

### Log Cek:

```bash
# Lihat log OmniRoute
docker compose logs omniroute

# Lihat log Redis
docker compose logs omniroute-redis
```

---

## 📦 Menambah Provider Baru

Untuk menambahkan provider LLM baru (misalnya: Mistral, Llama Cloud), edit file konfigurasi di dalam container atau tambahkan environment variable sesuai dokumentasi OmniRoute.

Cara umum:
1. Masuk ke container: `docker exec -it omniroute /bin/sh`
2. Edit file `.env` atau konfigurasi lokal
3. Restart: `docker compose restart omniroute`

---

## 🧹 Bersihkan & Matikan

```bash
# Stop semua container (tapi simpan data)
docker compose down

# Hapus seluruh data (include Redis & container state)
docker compose down -v
```

---

## 📜 Lisensi

MIT License — bebas digunakan untuk proyek pribadi maupun komersial.

---

## 🙏 Kontribusi

Jika menemukan bug atau ingin menambahkan fitur, silakan:
1. Fork repo ini
2. Buat branch fitur (`git checkout -b fitur-x`)
3. Commit perubahan (`git commit -m 'feat: tambah fitur X'`)
4. Push ke branch (`git push origin fitur-x`)
5. Buat Pull Request

---

## 📞 Kontak

Repo dipelihara oleh **vanderstark** — untuk pertanyaan lebih lanjut, hubungi melalui GitHub Issues.