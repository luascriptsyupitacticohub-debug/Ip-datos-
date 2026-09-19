<div style="max-width: 600px; margin: 0 auto; font-family: 'Segoe UI', sans-serif; color: #ffffff; background-color: #0f0f1a; padding: 20px; border-radius: 15px;">
    <h2 style="text-align: center; color: #00ffcc;">📡 Mi IP y Ubicación</h2>
    
    <div id="loading" style="text-align: center; color: #a0a0b0; padding: 40px 0; font-size: 18px;">
        Obteniendo tu información de red, por favor espera...
    </div>

    <div id="resultBox" style="display: none; background-color: #1a1a2e; padding: 20px; border-radius: 8px; border-left: 4px solid #00ffcc; margin-bottom: 20px;"></div>

    <div id="mapContainer" style="display: none; border-radius: 8px; overflow: hidden; border: 1px solid #2a2a4a; margin-bottom: 20px;">
        <iframe id="googleMap" style="width: 100%; height: 300px; border: 0;" src="" allowfullscreen loading="lazy"></iframe>
    </div>

    <div id="shareGroup" style="display: none; gap: 10px; margin-top: 20px; display: flex;">
        <button onclick="copyToClipboard()" style="flex: 1; padding: 12px; border: none; border-radius: 8px; background-color: #00ffcc; color: #0f0f1a; font-weight: bold; cursor: pointer;">📋 Copiar Datos</button>
        <button onclick="shareViaWhatsApp()" style="flex: 1; padding: 12px; border: none; border-radius: 8px; background-color: #2a2a4a; color: #ffffff; font-weight: bold; cursor: pointer;">💬 Enviar WhatsApp</button>
    </div>
</div>

<script>
    let currentData = null;

    window.onload = async function() {
        await getMyLocation();
    };

    async function getMyLocation() {
        const loading = document.getElementById('loading');
        const resultBox = document.getElementById('resultBox');
        const mapContainer = document.getElementById('mapContainer');
        const shareGroup = document.getElementById('shareGroup');

        try {
            const url = `https://ipwho.is/`;
            const response = await fetch(url);

            if (response.ok) {
                const data = await response.json();

                if (data && data.success !== false) {
                    currentData = data; 
                    
                    resultBox.innerHTML = `
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Dirección IP:</span>
                            <span style="text-align: right; font-weight: 600; word-break: break-all;">${data.ip}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">País:</span>
                            <span style="text-align: right; font-weight: 600;">${data.country}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Región:</span>
                            <span style="text-align: right; font-weight: 600;">${data.region}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Ciudad:</span>
                            <span style="text-align: right; font-weight: 600;">${data.city}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Código Postal:</span>
                            <span style="text-align: right; font-weight: 600;">${data.postal || 'N/A'}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Zona Horaria:</span>
                            <span style="text-align: right; font-weight: 600;">${data.timezone ? data.timezone.id : 'N/A'}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #2a2a4a; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Proveedor (ISP):</span>
                            <span style="text-align: right; font-weight: 600;">${data.connection ? data.connection.isp : 'N/A'}</span>
                        </div>
                        <div style="display: flex; justify-content: space-between; padding: 12px 0; gap: 15px;">
                            <span style="color: #a0a0b0; min-width: 120px;">Registro (ASN):</span>
                            <span style="text-align: right; font-weight: 600;">${data.connection ? data.connection.asn : 'N/A'}</span>
                        </div>
                    `;
                    
                    loading.style.display = "none";
                    resultBox.style.display = "block";
                    shareGroup.style.display = "flex";

                    const mapSrc = `https://maps.google.com/maps?q=${data.latitude},${data.longitude}&z=12&output=embed`;
                    document.getElementById('googleMap').src = mapSrc;
                    mapContainer.style.display = "block";

                } else {
                    throw new Error(data.message || "No se pudo obtener la ubicación.");
                }
            } else {
                throw new Error("Error al conectar con el servidor.");
            }
        } catch (error) {
            loading.style.display = "none";
            resultBox.innerHTML = `<p style="color: #e94560; text-align: center; font-weight: bold;">Error: ${error.message}</p>`;
            resultBox.style.display = "block";
        }
    }

    function formatMessage() {
        if (!currentData) return "";
        const isp = currentData.connection ? currentData.connection.isp : 'N/A';
        const asn = currentData.connection ? currentData.connection.asn : 'N/A';
        const tz = currentData.timezone ? currentData.timezone.id : 'N/A';
        return `*Datos de Red*
📌 *Dirección IP:* ${currentData.ip}
🌍 *País:* ${currentData.country}
📍 *Región:* ${currentData.region}
🏙️ *Ciudad:* ${currentData.city}
📮 *Código Postal:* ${currentData.postal || 'N/A'}
⏱️ *Zona Horaria:* ${tz}
🏢 *ISP:* ${isp}
🔢 *Registro (ASN):* ${asn}
🗺️ *Google Maps:* https://maps.google.com/?q=${currentData.latitude},${currentData.longitude}`;
    }

    function copyToClipboard() {
        const text = formatMessage();
        if (navigator.clipboard && navigator.clipboard.writeText) {
            navigator.clipboard.writeText(text).then(() => {
                alert("✅ Información copiada al portapapeles correctamente.");
            }).catch(err => {
                fallbackCopyTextToClipboard(text);
            });
        } else {
            fallbackCopyTextToClipboard(text);
        }
    }

    function fallbackCopyTextToClipboard(text) {
        const textArea = document.createElement("textarea");
        textArea.value = text;
        textArea.style.top = "0";
        textArea.style.left = "0";
        textArea.style.position = "fixed";
        document.body.appendChild(textArea);
        textArea.focus();
        textArea.select();
        try {
            const successful = document.execCommand('copy');
            alert(successful ? "✅ Información copiada al portapapeles." : "❌ No se pudo copiar.");
        } catch (err) {
            alert("❌ Error al copiar la información.");
        }
        document.body.removeChild(textArea);
    }

    function shareViaWhatsApp() {
        const text = formatMessage();
        const whatsappUrl = `https://wa.me/?text=${encodeURIComponent(text)}`;
        window.open(whatsappUrl, '_blank');
    }
</script>
