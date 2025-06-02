// Örnek JavaScript Kodu - Saniyede 2 kere HTTPS isteği
// Bu kodu GitHub'daki README.md dosyasına kopyalayın

const https = require('https');
const url = require('url');

// Hedef URL
const TARGET_URL = 'http://localhost:3000/';

// İstek sayacı
let requestCount = 0;

// HTTPS isteği gönderen fonksiyon
function sendRequest() {
    try {
        const parsedUrl = url.parse(TARGET_URL);
        
        const options = {
            hostname: parsedUrl.hostname,
            port: 443,
            path: parsedUrl.path,
            method: 'GET',
            headers: {
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
            },
            // SSL sertifika hatalarını görmezden gel
            rejectUnauthorized: false
        };

        const req = https.request(options, (res) => {
            requestCount++;
            
            let data = '';
            
            res.on('data', (chunk) => {
                data += chunk;
            });
            
            res.on('end', () => {
                console.log(`[${new Date().toLocaleTimeString()}] İstek #${requestCount} - Durum: ${res.statusCode}`);
                console.log(`Veri boyutu: ${data.length} byte`);
            });
        });

        req.on('error', (error) => {
            console.log(`[${new Date().toLocaleTimeString()}] Hata: ${error.message}`);
        });

        req.setTimeout(5000, () => {
            console.log(`[${new Date().toLocaleTimeString()}] İstek zaman aşımı`);
            req.destroy();
        });

        req.end();
        
    } catch (error) {
        console.log(`[${new Date().toLocaleTimeString()}] Genel hata: ${error.message}`);
    }
}

// Başlangıç mesajı
console.log('🚀 HTTPS İstek Bombardımanı Başlatılıyor...');
console.log(`📡 Hedef URL: ${TARGET_URL}`);
console.log('⏱️  Saniyede 2 istek gönderiliyor');
console.log('Press Ctrl+C to stop');

// Saniyede 2 kere (500ms aralıklarla) istek gönder
const interval = setInterval(() => {
    sendRequest();
}, 500); // 500ms = saniyede 2 kere

// 30 saniye sonra otomatik durdur (opsiyonel)
setTimeout(() => {
    clearInterval(interval);
    console.log(`\n✅ İşlem tamamlandı. Toplam ${requestCount} istek gönderildi.`);
    process.exit(0);
}, 3000000); // 30 saniye

// Graceful shutdown
process.on('SIGINT', () => {
    clearInterval(interval);
    console.log(`\n🛑 İşlem durduruldu. Toplam ${requestCount} istek gönderildi.`);
    process.exit(0);
});
