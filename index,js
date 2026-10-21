const express = require('express');
const path = require('path');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Bellek içi veritabanı (Kullanıcılar ve Oyunlar)
let users = [];
let games = [
    { id: 1, title: "Classic Crossroads", creator: "Owner", thumb: "Crossroads" },
    { id: 2, title: "Chaos Canyon", creator: "Owner", thumb: "Chaos Canyon" },
    { id: 3, title: "Happy Home in Robloxia", creator: "Owner", thumb: "Happy Home" },
    { id: 4, title: "Doomspire Brickbattle", creator: "Owner", thumb: "Doomspire" }
];

// Ana Sayfa
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'));
});

// Kayıt Ol API
app.post('/api/register', (req, res) => {
    const { username, password } = req.body;
    if (!username || !password) {
        return res.status(400).json({ success: false, message: "Kullanıcı adı ve şifre zorunludur." });
    }

    const exists = users.find(u => u.username.toLowerCase() === username.toLowerCase());
    if (exists) {
        return res.status(400).json({ success: false, message: "Bu kullanıcı adı zaten alınmış." });
    }

    // İlk kullanıcı veya adı Owner/Admin olan hesaba sınırsız Robux ve Owner rütbesi ver
    const isOwner = users.length === 0 || username.toLowerCase() === 'owner' || username.toLowerCase() === 'admin';

    const newUser = {
        id: users.length + 1,
        username,
        password,
        role: isOwner ? 'OWNER' : 'MEMBER',
        robux: isOwner ? 999999999 : 50,
        tix: isOwner ? 999999999 : 100
    };

    users.push(newUser);
    res.json({ success: true, user: newUser });
});

// Giriş Yap API
app.post('/api/login', (req, res) => {
    const { username, password } = req.body;
    const user = users.find(u => u.username.toLowerCase() === username.toLowerCase() && u.password === password);

    if (!user) {
        return res.status(401).json({ success: false, message: "Geçersiz kullanıcı adı veya şifre!" });
    }

    res.json({ success: true, user });
});

// Oyun Listesi API
app.get('/api/games', (req, res) => {
    res.json(games);
});

// Ana Ekranda Oyun Oluşturma API
app.post('/api/create-game', (req, res) => {
    const { title, creator } = req.body;
    if (!title) {
        return res.status(400).json({ success: false, message: "Oyun adı boş bırakılamaz." });
    }

    const newGame = {
        id: games.length + 1,
        title: title,
        creator: creator || "Owner",
        thumb: title.substring(0, 14)
    };

    games.unshift(newGame);
    res.json({ success: true, game: newGame });
});

// Roblox İstemci Rotaları
app.get('/Login/Negotiate.ashx', (req, res) => {
    res.type('text/plain');
    res.send('OK');
});

app.get('/Asset/CharacterFetch.ashx', (req, res) => {
    res.type('text/plain');
    res.send('');
});

app.get('/game/Join.ashx', (req, res) => {
    const luaScript = `
        local client = game:GetService("NetworkClient")
        local player = game:GetService("Players"):CreateLocalPlayer(0)
        player.Name = "${req.query.username || 'Owner'}"
        client:Connect("127.0.0.1", 53640, 0, 20)
    `;
    res.type('text/plain');
    res.send(luaScript);
});

app.listen(PORT, '0.0.0.0', () => {
    console.log(`QweBlox sunucusu çalışıyor: Port ${PORT}`);
});
