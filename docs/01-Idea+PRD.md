<img width="1440" height="5108" alt="image" src="https://github.com/user-attachments/assets/ce37ebef-930d-481b-9c8a-d11050193a59" />

Ini PRD lengkap buat StatusPilot — nama sementara, lo bisa ganti nanti.
Beberapa keputusan teknis yang perlu lo perhatiin:
TimescaleDB dipilih bukan PostgreSQL biasa, karena data uptime itu time-series — jutaan row dalam hitungan bulan. TimescaleDB jauh lebih efisien untuk query "uptime 30 hari terakhir" dibanding PostgreSQL plain.
BullMQ + Redis untuk scheduler monitor — ini bagian yang paling "DevOps-flavored" dari app ini. Lo bakal bikin worker yang ping setiap service secara berkala, handle retry, dan trigger alert. Ini yang bikin project ini beda dari CRUD biasa.
Scope MVP dulu — jangan loncat ke V2/V3 sebelum MVP-nya live. Status page yang beneran jalan di domain publik lebih impressive dari fitur lengkap yang belum kelar.
