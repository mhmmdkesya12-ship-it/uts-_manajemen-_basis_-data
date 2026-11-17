let db;

initSqlJs({
    locateFile: file => `https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.8.0/${file}`
}).then(function(SQL){
    db = new SQL.Database();

    db.run(`
        CREATE TABLE IF NOT EXISTS riwayat (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nama_sekolah TEXT,
            tahun_masuk INTEGER,
            tahun_lulus INTEGER,
            keterangan TEXT
        );
    `);

    const checkData = db.exec("SELECT COUNT(*) FROM riwayat");
    if (checkData[0].values[0][0] === 0) {
        db.run(`INSERT INTO riwayat (nama_sekolah, tahun_masuk, tahun_lulus, keterangan) VALUES 
            ('SDN 1 Rajapolah', 2013, 2019, 'Lulus'),
            ('SMPN 1 Rajapolah', 2019, 2022, 'Lulus'),
            ('SMKN 1 Rajapolah', 2022, 2025, 'Lulus');
        `);
    }

    tampilkanData();
});

function tampilkanData() {
    const tbody = document.getElementById('tabelRiwayat');
    tbody.innerHTML = '';

    try {
        const result = db.exec("SELECT * FROM riwayat");
        if (result.length > 0) {
            const rows = result[0].values;
            rows.forEach((row, index) => {
                let tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${index + 1}</td>
                    <td>${row[1]}</td>
                    <td>${row[2]}</td>
                    <td>${row[3]}</td>
                    <td>${row[4]}</td>
                `;
                tbody.appendChild(tr);
            });
        }
    } catch (err) {
        console.error("Gagal memuat data:", err);
    }
}

document.getElementById('formRiwayat').addEventListener('submit', function(e) {
    e.preventDefault();

    const nama_sekolah = document.getElementById('nama_sekolah').value;
    const tahun_masuk = document.getElementById('tahun_masuk').value;
    const tahun_lulus = document.getElementById('tahun_lulus').value;
    const keterangan = document.getElementById('keterangan').value;

    const stmt = db.prepare("INSERT INTO riwayat (nama_sekolah, tahun_masuk, tahun_lulus, keterangan) VALUES (?, ?, ?, ?)");
    stmt.run([nama_sekolah, tahun_masuk, tahun_lulus, keterangan]);
    stmt.free();

    this.reset();
    tampilkanData();
});
