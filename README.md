1. Objective dari Use Case

    Tujuan dari proyek ini adalah membangun sebuah prototype asisten AI berbasis Semantic Search dan LLM untuk tim customer service e-commerce. Sistem ini dirancang untuk menjawab pertanyaan pelanggan secara otomatis berdasarkan knowledge base FAQ yang sudah ada. Fokus utamanya adalah kemampuan sistem dalam memahami makna pertanyaan pelanggan sehingga sistem tetap dapat menemukan jawaban yang relevan meskipun pelanggan menggunakan kata-kata yang berbeda dari teks FAQ asli.


2. Sumber dan Struktur Knowledge Base

    - Sumber Data: File ecommerce_faq_knowledge_base.csv yang terletak di dalam folder data.
    - Struktur Data: File CSV ini memiliki 4 kolom utama:
        - faq_id: ID unik untuk setiap entri FAQ.
        - category: Kategori pertanyaan (misal: Pemesanan, Pengiriman, Pembayaran, Pengembalian, Akun, Promo, Produk, Customer Service).
        - question: Teks pertanyaan dari pelanggan.
        - answer: Teks jawaban yang sesuai dengan pertanyaan.
    - Total entry: Terdapat 59 baris data FAQ yang akan digunakan sebagai basis pengetahuan.


3. Batasan dan Asumsi

    - Batasan Pengetahuan: 
    Sistem hanya akan menjawab berdasarkan informasi yang tersedia di dalam knowledge base FAQ yang disediakan. Sistem tidak diizinkan mencari informasi eksternal atau berhalusinasi.

    - Penanganan Pertanyaan di Luar Konteks: 
    Jika pertanyaan pengguna tidak memiliki kecocokan semantik yang cukup dengan FAQ yang ada (di bawah ambang batas relevansi), LLM akan diinstruksikan untuk mengatakan bahwa informasi tidak tersedia dan menyarankan pengguna menghubungi customer service manusia.

    - Asumsi Bahasa: 
    Diasumsikan pengguna mengajukan pertanyaan dalam Bahasa Indonesia, sesuai dengan bahasa yang digunakan pada knowledge base.

    - Pembaruan Data: 
    Knowledge base bersifat statis (tidak ada pembaruan real-time) selama proses pengujian prototype ini.


4. Problem Statement & Knowledge Base

    - Problem: Tim CS e-commerce menerima banyak pertanyaan repetitif. Dibutuhkan asisten AI berbasis semantic search yang mampu memahami makna pertanyaan (parafrase/sinonim), bukan sekadar mencocokkan kata kunci.
    - Knowledge Base: File ecommerce_faq_knowledge_base.csv berisi 59 FAQ (kolom: faq_id, category, question, answer). Teks question dan answer digabungkan menjadi satu unit retrievable.


5. Alur Sistem (RAG Pipeline)

    - Ingest: Memuat CSV dan menggabungkan Q+A menjadi satu teks.
    - Embed: Mengubah teks menjadi vektor menggunakan gemini-embedding-001 (dimensi 3072).
    - Store: Menyimpan vektor ke Vector Database (FAISS IndexFlatL2).
    - Retrieve: Mengubah query user menjadi vektor, lalu mencari top-k FAQ terdekat (jarak L2 terkecil).
    - Generate: Menyisipkan hasil retrieval ke dalam Prompt terstruktur (Persona, Task, Context, Aturan) dan mengirimkannya ke LLM (gemini-3.1-flash-lite).


6. Hasil Pengujian & Analisis

    - Direct Query (mirip teks asli): Retrieval sangat akurat (distance sangat kecil). LLM menjawab persis sesuai konteks tanpa halusinasi.
    - Paraphrase (bahasa sehari-hari): Sistem berhasil menangkap makna meski kata kunci berbeda. LLM merangkum jawaban dengan natural.
    - Out-of-Context (tidak ada di FAQ): Distance sangat besar. Berkat aturan prompt, LLM menolak menjawab dan menyuruh menghubungi CS (berhasil mencegah halusinasi).


7. Keterbatasan Sistem

    - Rate Limit: Kuota API gratis Gemini membatasi pengujian beruntun.
    - Statis: Knowledge base tidak real-time; harus *re-embedding manual jika ada FAQ baru.
    - Metrik: Penggunaan L2 Distance kurang optimal untuk teks yang panjangnya bervariasi dibanding Cosine Similarity.