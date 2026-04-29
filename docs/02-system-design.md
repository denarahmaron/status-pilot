Mau lanjut? Gw akan breakdown architecture diagram-nya — gimana FE, BE, worker, DB, dan monitoring saling terhubung — sebelum lo mulai nulis satu baris kode pun.

<img width="1440" height="1560" alt="image" src="https://github.com/user-attachments/assets/7067f126-c3a3-43e5-ad61-6f560883d0ad" />

Ini system design-nya. Lo bisa klik tiap komponen untuk nanya lebih detail. Sekarang gw jelaskan keputusan arsitektur yang penting:
Nginx sebagai pintu masuk tunggal. Semua traffic — baik dari dashboard admin maupun public status page — masuk lewat Nginx dulu. Dia yang handle SSL termination dan routing ke service yang tepat. Ini pattern standar production yang recruiter expect lo tau.
API server dan Monitor worker dipisah. Ini keputusan paling krusial. Kalau digabung, satu request HTTP yang lambat bisa nge-block proses monitoring. Dengan dipisah, worker berjalan independen dan punya lifecycle sendiri — kalau API down pun, monitoring tetap jalan.
BullMQ + Redis untuk job queue. Monitor worker gak langsung ping service setiap N menit pakai setInterval. Dia pakai job queue: setiap monitor punya job terjadwal di Redis, worker ambil job → ping target → simpan hasil → reschedule. Ini yang bikin sistem scalable dan tahan crash.
TimescaleDB bukan PostgreSQL plain. Data uptime itu time-series — lo akan query hal seperti "uptime 30 hari terakhir" atau "average response time per jam". TimescaleDB adalah extension PostgreSQL yang dioptimasi untuk query seperti ini, jauh lebih cepat dibanding GROUP BY timestamp di plain Postgres.
"Monitor the monitor" dengan Uptime Kuma. Ini yang sering dilupain. Siapa yang monitor StatusPilot-nya sendiri kalau StatusPilot down? Uptime Kuma jalan sebagai service terpisah di port berbeda, tugasnya cuma satu: pastikan StatusPilot masih hidup.
