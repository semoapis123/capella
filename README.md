// Localhost:3000'e Saniyede 2 İstek
// Bu kodu GitHub'daki README.md dosyasına kopyalayın

const http = require('http');

const TARGET_URL = 'http://localhost:3000/';
let requestCount = 0;
let successCount = 0;
let errorCount = 0;
let startTime = Date.now();

function sendRequest() {
    requestCount++;
    
    console.log(`[${new Date().toLocaleTimeString()}] 🚀 İstek #${requestCount} → ${TARGET_URL}`);
    
    const req = http.get(TARGET_URL, {
        headers: {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
            'Connection': 'keep-alive'
        },
        timeout: 5000
    }, (res) => {
        successCount++;
        let dataSize = 0;
        
        res.on('data', (chunk) => {
            dataSize += chunk.length;
        });
        
        res.on('end', () => {
            console.log(`[${new Date().toLocaleTimeString()}] ✅ İstek #${requestCount} OK - ${res.statusCode} - ${dataSize} byte`);
        });
        
    });
    
    req.on('error', (err) => {
        errorCount++;
        console.log(`[${new Date().toLocaleTimeString()}] ❌ İstek #${requestCount} HATA: ${err.message}`);
    });
    
    req.on('timeout', () => {
        errorCount++;
        console.log(`[${new Date().toLocaleTimeString()}] ⏰ İstek #${requestCount} TIMEOUT`);
        req.destroy();
    });
    
    req.end();
}

// Başlangıç banner
console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
console.log('🎯 LOCALHOST:3000 BOMBARDIMANI BAŞLATILDI!');
console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
console.log(`📡 Hedef: ${TARGET_URL}`);
console.log('⚡ Hız: Saniyede 2 istek (500ms interval)');
console.log('⏱️  Süre: 30 saniye');
console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');

// İlk isteği hemen gönder
sendRequest();

// Her 500ms'de bir devam et (saniyede 2)
const attackInterval = setInterval(() => {
    sendRequest();
}, 50);

// Durum raporu her 5 saniyede
const statusInterval = setInterval(() => {
    const elapsed = Math.round((Date.now() - startTime) / 1000);
    const rate = Math.round(requestCount / elapsed);
    console.log(`📊 [${elapsed}s] ${requestCount} istek | ${successCount} başarılı | ${errorCount} hata | ${rate}/s`);
}, 5000);

// 30 saniye sonra durdur
setTimeout(() => {
    clearInterval(attackInterval);
    clearInterval(statusInterval);
    
    const duration = Math.round((Date.now() - startTime) / 1000);
    const avgRate = Math.round(requestCount / duration);
    const successRate = Math.round((successCount / requestCount) * 100);
    
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    console.log('🏁 BOMBARDIMAN TAMAMLANDI!');
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    console.log(`📊 Toplam İstek: ${requestCount}`);
    console.log(`✅ Başarılı: ${successCount}`);
    console.log(`❌ Hatalı: ${errorCount}`);
    console.log(`⏱️  Süre: ${duration} saniye`);
    console.log(`📈 Ortalama Hız: ${avgRate} istek/saniye`);
    console.log(`🎯 Başarı Oranı: %${successRate}`);
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    
    process.exit(0);
}, 30000);

// Ctrl+C ile acil durdurma
process.on('SIGINT', () => {
    clearInterval(attackInterval);
    clearInterval(statusInterval);
    
    const duration = Math.round((Date.now() - startTime) / 1000);
    
    console.log('\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    console.log('🛑 KULLANICI TARAFINDAN DURDURULDU!');
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    console.log(`📊 Toplam İstek: ${requestCount}`);
    console.log(`✅ Başarılı: ${successCount}`);
    console.log(`❌ Hatalı: ${errorCount}`);
    console.log(`⏱️  Süre: ${duration} saniye`);
    if (duration > 0) {
        console.log(`📈 Ortalama: ${Math.round(requestCount/duration)} istek/saniye`);
    }
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
    
    process.exit(0);
});
