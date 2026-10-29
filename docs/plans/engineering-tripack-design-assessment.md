# Plan: Design Assessment untuk Engineering Tripack Generator

## Tujuan dan status

Menambahkan assessment desain yang konkret sebelum desain diturunkan menjadi strategi dan task, dengan referensi DDD Eric Evans, DRY, KISS, dan SOLID. Assessment menghasilkan alasan keputusan, ownership aturan, batas penyempurnaan struktur, dan rencana verifikasi.

**Status: DONE — implementasi T1–T5 selesai pada 2026-10-29.** Validasi mencakup struktur skill, tautan relatif, serta walkthrough lima skenario dengan artefak sementara. Hasil dan batas verifikasi dicatat dalam [laporan validasi](engineering-tripack-design-assessment-validation.md). T6 menjadi follow-up yang belum dijalankan karena memerlukan 2–3 task nyata pada proyek pengguna; tidak ada klaim peningkatan kualitas implementasi dari walkthrough ini. Instruksi executor tidak diubah. [Catatan hipotesis](../hipotesis/tripcak-generator.md) tetap menyimpan temuan awal dan batas bukti.

- [x] T1 — Susun referensi assessment.
- [x] T2 — Integrasikan assessment ke workflow dan template.
- [x] T3 — Perjelas kewenangan dan perluas Mode C.
- [x] T4 — Selaraskan discovery dan README.
- [x] T5 — Validasi instruksi dan skenario.
- [ ] T6 — Follow-up: pilot dan evaluasi manfaat pada task nyata.

## Scope dan keputusan rancangan

| File | Perubahan yang direncanakan |
|---|---|
| `skills/engineering-tripack-generator/references/design-assessment.md` | Referensi baru: kriteria assessment, pemilihan pola, contoh, dan sumber primer. |
| `skills/engineering-tripack-generator/SKILL.md` | Integrasikan assessment, template hasil, kewenangan penyempurnaan, Mode C, dan discovery. |
| `README.md` | Selaraskan deskripsi generator dan dukungan revisi berdasarkan temuan implementasi. |

Instruksi dan referensi skill ditulis dalam bahasa Inggris. Skill tetap stack-agnostic dan dapat dipasang secara mandiri; contoh yang diperlukan disertakan dalam referensi, tanpa ketergantungan pada checkout BLOGS atau gocraft.

Assessment berlaku pada Mode A, B, dan C dengan kedalaman sesuai dampak perubahan. Referensi dibaca pada tahap assessment; bagian domain diterapkan ketika perubahan menyentuh model atau aturan domain. Task sederhana dapat mempertahankan struktur existing dengan alasan singkat.

Rencana ini tidak mencakup perubahan kode aplikasi atau instruksi gocraft, mandatory subagent, Sonar, maupun frekuensi siklus dan commit. `decision-log.md` tetap kondisional untuk keputusan teknis yang didorong bisnis; alasan teknis ditempatkan dalam technical design.

## T1 — Susun referensi assessment

**Deliverable:** `references/design-assessment.md`.

Susun panduan berdasarkan keputusan yang harus dibuat agent, dengan isi berikut:

1. **Esensi DDD:** konsistensi bahasa bisnis dalam konteks model, kolaborasi untuk menyelesaikan ambiguitas, fokus pada core domain, kesesuaian model dan kode, ownership invariant dan konsistensi aggregate, serta penyempurnaan model dari temuan implementasi.
2. **Kualitas desain:** DRY menilai duplikasi pengetahuan; SRP menilai alasan perubahan; KISS menilai total kompleksitas; OCP menilai variasi yang diperlukan; LSP menilai kesesuaian perilaku implementasi; ISP menilai kebutuhan caller; DIP menilai arah ketergantungan kebijakan terhadap detail.
3. **Pola kondisional:** entity untuk identitas/lifecycle, value object untuk makna dan aturan nilai, aggregate untuk konsistensi perubahan, domain service untuk operasi domain yang tidak cocok dimiliki entity/value object, serta repository/factory ketika akses atau konstruksi memerlukan enkapsulasi.
4. **Kontrak lintas jalur:** bedakan input baru, restoration, dan transisi. Nilai accepted inputs, normalisasi, error ordering, waktu kegagalan, efek samping, dan kompatibilitas caller/data.
5. **Contoh keputusan:** struktur sederhana yang dipertahankan; value object yang memberi manfaat; restoration yang membuat usulan perubahan memerlukan keputusan perilaku.

Untuk setiap prinsip/pola, berikan pertanyaan assessment, kondisi relevan, tradeoff atau risiko salah penerapan, dan bentuk bukti yang diperlukan. Gunakan SRP sebagai bagian SOLID agar penilaiannya tidak berulang. Pisahkan ringkasan sumber dari aturan operasional yang kita usulkan.

**Selesai bila:** referensi membantu memilih dan menjelaskan desain, termasuk keputusan mempertahankan desain sederhana. Jumlah tipe, method, interface, atau file tidak menjadi ukuran kualitas; pola diterapkan berdasarkan masalah dan manfaat yang dijelaskan.

## T2 — Integrasikan assessment ke workflow dan template

**Dependensi:** T1.

Ubah alur menjadi: discovery → analisis/baseline → draft technical design → assessment dan penyempurnaan desain → tactical strategy → task plan → pemeriksaan konsistensi. Keputusan bisnis yang muncul tetap ditangani melalui mekanisme decision log yang tersedia.

Tambahkan pointer eksplisit ke referensi pada langkah assessment, beserta kriteria selesai. Tempatkan detail penilaian di referensi; `SKILL.md` menyimpan urutan, kewajiban, routing, dan hasil minimum.

Adaptasi template technical design agar dapat mencatat:

- Konsep/istilah dan konteks berlakunya, jika relevan.
- Aturan material → pemilik → jalur enforcement → rencana bukti.
- Kontrak creation, restoration, dan transisi, jika relevan.
- Keputusan struktur material, alasan, alternatif dengan tradeoff material, dan ruang penyempurnaannya.
- Hasil assessment: dipertahankan, diperbaiki, atau ditunda dengan alasan dan kebutuhan keputusan.

Gabungkan informasi dengan bagian existing yang sesuai. Hindari laporan assessment terpisah dan penyalinan checklist lengkap ke setiap tripack. Tandai ketidakberlakuan bagian domain secara singkat bila perlu.

**Selesai bila:** setiap aturan material memiliki pemilik dan rencana bukti; setiap keputusan struktur material memiliki alasan; blocker dan pekerjaan yang terdampak terlihat; strategi, dependency, dan DoD mengikuti desain hasil assessment. Keputusan tertunda tidak diperlakukan sebagai desain siap eksekusi untuk task yang bergantung padanya.

## T3 — Perjelas kewenangan dan perluas Mode C

**Dependensi:** T2.

Bedakan kontrak yang mengikat, pilihan struktur internal, dan struktur yang diwajibkan secara eksplisit beserta alasannya. Executor dapat menyempurnakan struktur internal dalam scope dengan alasan dan bukti perilaku tetap terjaga. Perubahan aturan bisnis, accepted inputs, error, lifecycle, kompatibilitas, atau constraint eksplisit memerlukan penyelesaian keputusan sesuai dampaknya.

Perluas Mode C agar menerima perubahan requirement maupun temuan desain/implementasi dengan requirement tetap. Pertahankan klasifikasi coverage/progress existing; untuk perbedaan yang ditemukan, tambahkan penilaian: pelanggaran kontrak, penyempurnaan struktur yang sah, atau keputusan belum selesai.

Sebelum membuat reconciliation task, tentukan apakah kode, dokumen, atau keduanya perlu berubah. Pertahankan task ID, status completed yang masih valid, obsolete markers, revision history, dan riwayat keputusan. Reconciliation mendahului task yang bergantung pada hasilnya; pembaruan dokumen saja tidak membutuhkan task perbaikan kode.

Catat handoff: perubahan desain yang memengaruhi task berikutnya harus disinkronkan sebelum task tersebut dimulai. Penutupan task menyelaraskan dokumen yang terdampak; perubahan struktur kecil tidak memerlukan pencatatan setiap langkah lokal. Penerapan mekanisme ini di gocraft merupakan pekerjaan lanjutan.

**Selesai bila:** perbedaan struktur dapat dinilai tanpa otomatis mengembalikan kode ke desain lama; perubahan perilaku terselubung tetap terdeteksi; aggregate boundary, transaksi, dan dampak di luar scope dinilai sebagai keputusan material ketika jaminan atau tanggung jawab berubah.

## T4 — Selaraskan discovery dan README

**Dependensi:** T2–T3.

Discovery membaca instruksi proyek yang tersedia dan berlaku, termasuk `AGENTS.md` dan `CLAUDE.md`, lalu memverifikasi konteks terhadap codebase. Klarifikasi ditujukan pada informasi material yang belum ditemukan atau konflik yang belum terselesaikan. Perbarui seluruh penyebutan discovery, termasuk frontmatter, testing template, dan ringkasan workflow.

Saat mengambil referensi flow existing, jelaskan bagian yang layak direuse dan keterbatasannya untuk kebutuhan baru. Bedakan konvensi wajib dari pilihan struktur existing; penilaian ini tetap mengikuti scope task.

README menjelaskan assessment sebelum task plan dan revisi berdasarkan temuan implementasi dengan cakupan yang sama seperti skill.

**Selesai bila:** proyek yang hanya menyediakan `AGENTS.md` dapat dipahami melalui discovery; instruksi yang tersedia tidak dianggap saling menggantikan tanpa pemeriksaan; README dan skill konsisten.

## T5 — Validasi instruksi dan skenario

**Dependensi:** T1–T4.

Jalankan validator skill yang tersedia, periksa tautan relatif, dan tinjau konsistensi frontmatter, workflow, template, dan Rules. Validator struktural tidak membuktikan kualitas keputusan desain.

Lakukan walkthrough berbasis skenario menggunakan artefak sementara, dengan hasil yang diharapkan berikut:

| Skenario | Hasil yang diperiksa |
|---|---|
| Mode A dengan aturan domain dan restoration | Ownership dan kontrak tiap jalur jelas; pola dipilih dengan alasan. |
| Mode B sederhana tanpa perubahan domain | Assessment proporsional; struktur dapat dipertahankan dengan alasan. |
| Mode C, struktur membaik dan kontrak tetap | Dokumen dapat diperbarui tanpa task pengembalian kode yang tidak diperlukan; ID/status dipertahankan. |
| Mode C, validasi restoration mempersempit accepted state | Perubahan perilaku terungkap dan keputusan ditandai sebelum task dependen siap dieksekusi. |
| Proyek hanya memiliki `AGENTS.md`; pola existing kurang cocok | Discovery berlanjut dari sumber tersedia dan menjelaskan reuse beserta keterbatasannya. |

**Selesai bila:** hasil setiap skenario dan keterbatasan verifikasi dicatat. Walkthrough adalah pemeriksaan instruksi, dengan klaim terpisah dari hasil implementasi nyata.

## T6 — Pilot dan evaluasi manfaat

**Dependensi:** T5; memerlukan task nyata pada proyek pengguna.

Gunakan 2–3 task dengan kebutuhan berbeda. Catat temuan sebelum implementasi, penyempurnaan setelah GREEN, temuan review manusia, dampak kompatibilitas, biaya assessment, dan kualitas alasan mempertahankan desain.

Evaluasi apakah aturan material lebih jelas pemiliknya, masalah ditemukan lebih awal, caller lebih sederhana, dan sinkronisasi mengurangi keputusan berdasarkan desain usang. Gunakan hasil untuk memangkas atau memperjelas instruksi. Pilot memberi bukti eksploratif; hasilnya tidak dianggap pembuktian kausal.

T1–T5 menghasilkan perubahan skill yang siap ditinjau. Klaim peningkatan kualitas implementasi tetap menunggu bukti T6; ketiadaan task pilot tidak perlu menahan penyusunan perubahan skill.

## Sumber primer untuk referensi

- [Eric Evans — Domain-Driven Design Reference](https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf): dasar konsep dan pola DDD; gunakan ringkasan dengan atribusi.
- [The Pragmatic Programmer — Tips](https://pragprog.com/tips/): DRY sebagai representasi pengetahuan yang otoritatif.
- [Robert C. Martin — The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html): tanggung jawab dan alasan perubahan.
- [Robert C. Martin — SOLID Relevance](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html): penjelasan SOLID, termasuk kontrak implementasi interface.

Kriteria KISS, pemicu pembacaan referensi, hasil assessment, dan aturan handoff ditulis sebagai keputusan workflow kita; atribusi kepada sumber mengikuti konsep yang benar-benar didukung sumber tersebut.
