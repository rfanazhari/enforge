# Hipotesis Perbaikan Engineering Tripack Generator

## Status dan tujuan

Catatan diskusi 2026-10-29 untuk menyiapkan perbaikan skill `engineering-tripack-generator` dan handoff ke gocraft. Temuan berasal dari pembacaan instruksi dan contoh dokumen; hubungan sebab-akibat dengan kualitas implementasi belum dibuktikan. Usulan di sini belum menjadi aturan workflow yang disetujui. Penyimpanan catatan ini tidak mengubah skill atau kode implementasi.

## Konteks dan bahan penilaian

Diskusi berawal dari kekhawatiran bahwa GREEN dapat menghasilkan implementasi yang memenuhi tes tanpa cukup menilai DDD, SRP, DRY, dan KISS. Assessment setelah GREEN tetap berguna, tetapi pilihan struktur dapat terbentuk lebih awal dalam technical design dan diteruskan ke task plan.

Bahan yang diperiksa:

- [README ENFORGE](../../README.md), terutama deskripsi generator dan hubungan antarskill.
- [Engineering Tripack Generator](../../skills/engineering-tripack-generator/SKILL.md): discovery, mode revisi, template, dan urutan generation.
- [Technical design BLOGS](../../../go-boilerplate/docs/services/taskdemo/engineering/blogs/technical-design.md), tactical strategy, dan task plan BLOGS-004 sebagai contoh konkret.
- [Hipotesis refactor gocraft](../../../gocraft/docs/refactor/hipotesis.md) dan instruksi workflow gocraft sebagai konteks executor.

Skill sudah mewajibkan pemeriksaan codebase, justifikasi penempatan, analisis kompatibilitas untuk adjustment, serta pelestarian keputusan saat revisi. Perbaikan perlu memperkuat penilaian dan handoff tersebut. Tidak ada tes implementasi yang dijalankan untuk penilaian ini.

## Temuan dan hipotesis

### H1 — Assessment desain sebelum task plan

**Temuan:** alur generation bergerak dari analisis ke technical design, tactical strategy, dan task plan. Belum ada langkah eksplisit dengan kriteria selesai untuk menguji ownership invariant dan kualitas pembagian tanggung jawab. Kolom Justification meminta alasan penempatan, tetapi belum meminta evaluasi alternatif yang material.

**Hipotesis:** assessment sebelum desain diturunkan menjadi task dapat menemukan kelemahan struktur lebih awal.

**Usulan:** nilai pemilik aturan, kemungkinan bypass invariant, tanggung jawab yang bercampur, pengulangan pengetahuan, serta kompleksitas yang harus dipahami caller. Bandingkan alternatif ketika ada tradeoff material; hasilnya boleh mempertahankan desain sederhana dengan alasan singkat. Gunakan DDD sesuai kebutuhan bisnis proyek dan prinsip SRP/DRY/KISS, tanpa mewajibkan setiap pola DDD.

### H2 — Kewenangan menyempurnakan struktur

**Temuan:** tripack disebut source of truth untuk implementasi. Belum ada pemisahan eksplisit antara constraint yang mengikat dan pilihan struktur yang dapat disempurnakan executor.

**Hipotesis:** tanpa pembedaan ini, executor dapat menganggap kepatuhan terhadap struktur awal sebagai ukuran kualitas desain.

**Usulan:** catat jenis keputusan, alasan, dan ruang penyempurnaannya:

| Jenis keputusan | Perlakuan yang diusulkan |
| --- | --- |
| Aturan bisnis, accepted inputs, error, lifecycle, dan kompatibilitas | Pertahankan; perubahan memerlukan keputusan tersendiri. |
| Struktur internal untuk memenuhi aturan | Boleh disempurnakan dalam scope dengan alasan konkret dan bukti perilaku tetap terjaga. |
| Struktur yang secara eksplisit diwajibkan beserta alasannya | Perlakukan sebagai constraint; selesaikan konflik sebelum mengubahnya. |
| Aggregate boundary, transaksi, atau dampak ke konsumen di luar scope | Ungkapkan kebutuhan keputusan atau task lanjutan. |

Scope perlu mengikuti tanggung jawab perubahan dan dampak langsungnya. File baru atau penyesuaian caller terkait dapat diperlukan, dengan tetap membatasi perluasan scope.

### H3 — Revisi berdasarkan temuan implementasi

**Temuan:** Mode C berangkat dari perubahan requirement. Belum ada jalur eksplisit untuk requirement yang tetap, tetapi struktur awal perlu disempurnakan berdasarkan assessment. Klasifikasi implemented-but-deviating belum membedakan pelanggaran kontrak dari penyempurnaan yang sah.

**Hipotesis:** reconciliation dapat mengarahkan kode kembali ke struktur lama sebelum menilai apakah dokumennya yang perlu diperbarui.

**Usulan:** perluas pemicu Mode C untuk temuan desain. Sebelum reconciliation, tentukan apakah yang perlu diubah adalah kode, dokumen, atau keduanya. Pertahankan task ID, riwayat revisi, dan keputusan sebelumnya; kebutuhan keputusan manusia mengikuti dampak perubahan, bukan setiap perbedaan struktur.

### H4 — Pemetaan invariant dan restoration

**Temuan:** template memiliki data/schema dan component breakdown, tetapi belum secara eksplisit meminta pemetaan konsep domain, invariant, dan pemiliknya. Pada BLOGS, aturan judul/konten/publikasi terperinci, sementara RestoreBlog hanya disebut merehidrasi persisted state.

**Hipotesis:** pilihan value object dapat dibuat tanpa menyelesaikan kontrak restoration, sehingga validasi baru diam-diam mengubah accepted inputs, error ordering, atau waktu kegagalan.

**Usulan:** untuk perubahan domain, petakan konsep, aturan nilai, aturan transisi, pemilik, dan cara enforcement. Jelaskan apakah restoration mempercayai, memvalidasi, atau menormalisasi data. Untuk task tanpa perubahan domain, cukup catat ketidakberlakuannya.

Value object memiliki validitas nilai; aggregate memiliki konsistensi dan transisi. Nilai apakah tipe tersebut melindungi invariant dan menyederhanakan caller. Constructor tervalidasi, state immutable, dan accessor dapat mencukupi; tidak perlu menciptakan method tambahan agar terlihat behavioral.

### H5 — Pola existing perlu dinilai kesesuaiannya

**Temuan:** discovery meminta satu atau dua flow existing sebagai referensi pola. Belum ada arahan eksplisit untuk membedakan konvensi proyek yang wajib dari struktur existing yang memiliki keterbatasan.

**Hipotesis:** pola yang mudah ditemukan dapat diteruskan tanpa evaluasi kesesuaiannya untuk aturan baru.

**Usulan:** jelaskan bagian yang layak direuse dan keterbatasannya terhadap kebutuhan task. Penyimpangan yang diperlukan perlu alasan; assessment ini tidak memperluas pekerjaan menjadi pembersihan legacy.

### H6 — Sinkronisasi discovery instructions

**Temuan:** README menyebut CLAUDE.md dan AGENTS.md, sedangkan skill berpusat pada CLAUDE.md dan meminta user jika file tersebut tidak ada. Contoh BLOGS menggunakan AGENTS.md dan codebase saat CLAUDE.md tidak tersedia.

**Usulan:** selaraskan discovery agar membaca instruksi proyek yang tersedia dan memverifikasi codebase. Klarifikasi diperlukan untuk informasi material yang belum ditemukan atau konflik yang belum terselesaikan.

## Pembagian tanggung jawab lintas skill

| Tahap | Tanggung jawab yang diusulkan |
| --- | --- |
| Generator, sebelum task plan | Uji ownership invariant, alternatif material, kompatibilitas, dan alasan keputusan. |
| Awal eksekusi gocraft | Cocokkan desain dengan kode aktual; ungkapkan konflik dan asumsi yang memengaruhi implementasi. |
| Setelah GREEN | Nilai struktur yang terbentuk; lakukan refactor yang manfaatnya dapat dijelaskan dan perilakunya terjaga. |
| Sinkronisasi dokumen | Rekam penyempurnaan yang sah agar task berikutnya memakai desain terbaru. |

Generator tetap stack-agnostic. Detail implementasi Go berada pada instruksi executor/proyek. Assessment awal melengkapi assessment setelah GREEN karena sebagian masalah baru terlihat saat kode terbentuk.

## Prioritas perubahan skill

1. Tambahkan assessment sebelum task plan, dengan hasil: dipertahankan beserta alasan, direvisi, atau ditunda dengan alasan dan kebutuhan keputusan.
2. Tambahkan pemetaan invariant bila relevan dan klasifikasi keputusan beserta ruang penyempurnaan struktur.
3. Perluas Mode C untuk temuan implementasi, termasuk keputusan arah reconciliation.
4. Perjelas kriteria selesai: setiap aturan material memiliki pemilik dan rencana bukti; keputusan struktur material memiliki alasan; blocker dinyatakan; task/DoD konsisten dengan desain hasil assessment.
5. Selaraskan discovery README/skill dan aturan handoff dengan executor. Pertahankan decision-log.md yang bersifat kondisional; alasan teknis tetap dapat dicatat dalam technical design.

## Evaluasi dan keputusan terbuka

Pilot dapat memakai 2–3 task nyata dengan kebutuhan berbeda. Catat temuan sebelum implementasi, penyempurnaan setelah GREEN, temuan review manusia, dampak kompatibilitas, biaya assessment, dan kualitas alasan mempertahankan desain. Jumlah file, value object, atau refactor bukan ukuran keberhasilan. Pilot kecil memberi bukti eksploratif, bukan pembuktian kausal.

Masih perlu diputuskan: seberapa rinci pencatatan keputusan, kapan sinkronisasi tripack dilakukan, batas kewenangan executor, dan bentuk assessment yang cukup ringan. Mandatory subagent, Sonar, serta perubahan frekuensi siklus/commit gocraft belum ditetapkan oleh catatan ini.

**Hipotesis kerja:** assessment saat perencanaan dan implementasi, disertai kewenangan memperbarui struktur berdasarkan bukti, dapat meningkatkan kualitas desain sambil menjaga kontrak dan scope.
