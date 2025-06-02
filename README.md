# 🚨 WhatsApp API - Modified By Array

![GitHub stars](https://img.shields.io/github/stars/arraynothere/Baileys?style=social)
![GitHub license](https://img.shields.io/github/license/arraynothere/Baileys)
![Node.js](https://img.shields.io/badge/node-%3E%3D14.0-green)
![Contributors](https://img.shields.io/github/contributors/arraynothere/Baileys)

Baileys WhatsApp API adalah library berbasis Node.js untuk berkomunikasi dengan WhatsApp Web tanpa perlu WebSocket tambahan. Ini adalah hasil modifikasi dari Whiskey Baileys agar lebih stabil dan mendukung lebih banyak tipe pesan.

Dikembangkan dengan performa tinggi untuk kebutuhan bot, otomatisasi pesan, dan integrasi aplikasi WhatsApp lainnya.

## 📦 About This Project
Repositori ini dimodifikasi dan dikelola oleh **Array** dengan dukungan tim kami 😇
dan proyek ini masih dalam tahap pengembangan karna akses tanpa batas :^

---

## ✨ Main Features

✅ **Autentikasi tanpa QR** menggunakan session authentication  
✅ **Dukungan Multi-Device (MD)** terbaru dari WhatsApp  
✅ **Kirim & terima pesan** dalam berbagai format  
✅ **Mengelola grup** (buat grup, tambahkan/kick anggota, atur deskripsi, dll.)  
✅ **Integrasi event** seperti masuk/keluar grup, pesan diterima, pesan terbaca  
---

## 📥 Installation

Pastikan **Node.js ≥ 14.0++** sudah terpasang,
Kemudian jalankan perintah berikut di terminal:

```sh
npm install @arraynothere/baileys
```

Atau dengan **Yarn**:

```sh
yarn add @arraynothere/baileys
```

---

## 🚀 Basic Uses of Pairing Code

```javascript
const { useMultiFileAuthState, makeWASocket } = require('@arraynothere/baileys');
const readline = require('readline');

const rl = readline.createInterface({ input: process.stdin, output: process.stdout });
const question = (q) => new Promise(res => rl.question(q, res));

async function start() {
  const { state, saveCreds } = await useMultiFileAuthState('./session');
  const usePairingCode = true;

  const conn = makeWASocket({
    auth: state,
    printQRInTerminal: !usePairingCode,
    keepAliveIntervalMs: 50000,
  });

  conn.ev.on('creds.update', saveCreds);

  if (usePairingCode && !conn.authState.creds.registered) {
    const phone = (await question('Enter Your Number Phone:\n')).replace(/\D/g, '');
    rl.close();
    try {
      const code = await conn.requestPairingCode(phone);
      console.log('Code Whatsapp:', code.match(/.{1,4}/g).join('-'));
    } catch (e) {
      console.log('Failed to get pairing code:', e.message);
    }
  }

  conn.ev.on('connection.update', ({ connection, lastDisconnect }) => {
    if (connection === 'close') {
      const reason = lastDisconnect?.error?.output?.statusCode || 'Unknown';
      console.log('Connection closed:', reason);
      if ([401, 405].includes(reason)) {
        console.log('Logged out, delete session folder to relogin');
      } else if ([515, 428].includes(reason)) {
        console.log('Restarting...');
        start();
      }
    } else if (connection === 'open') {
      console.log('Whatsapp connected');
    }
  });

  conn.ev.on('messages.upsert', async (m) => {
    if (m.type === 'notify') {
      const msg = m.messages[0];
      if (!msg.key.fromMe && msg.message) {
        await conn.sendMessage(msg.key.remoteJid, { text: 'Hello there!' });
      }
    }
  });
}

start();
```

---

## 📜 API Documentation

| Fitur               | Deskripsi |
|---------------------|----------|
| `sendMessage()`    | Mengirim pesan teks, gambar, video, dll. |
| `updateProfile()`  | Mengubah foto profil dan nama pengguna |
| `getChats()`       | Mendapatkan daftar chat pengguna |
| `groupParticipantsUpdate()` | Menambahkan/menghapus anggota grup |

📖 **Dokumentasi lengkap:** [Baileys API Docs](https://github.com/adiwajshing/Baileys/wiki)

---

## 🤝 Contribution

Kami menyambut kontribusi dari siapa saja! Jika ingin membantu:  
1. **Fork** repositori ini  
2. **Buat branch baru**  
3. **Buat Pull Request (PR)**  

💡 Punya ide? Buat **Issue** di [repository ini](https://github.com/arraynothere/Baileys/issues).  

---

## 📬 Contact

📩 **Email**: arraynothere@gmail.com  
🌍 **Website**: [Baileys API](https://github.com/arraynothere/Baileys)  

---

# 🌝 **Happy Coding :D** �
