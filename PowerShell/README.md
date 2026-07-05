# PowerShell - Script de Instalación de Programas

## 📋 Descripción
Script para instalar programas automáticamente en equipos nuevos.

## 🚀 Script

```powershell
# ============================================
# Instalador Automático de Programas
# Para puesta a punto de equipos
# ============================================

# Lista de programas a instalar
$programas = @(
    "Google Chrome",
    "7-Zip",
    "VLC Media Player"
)

Write-Host "🚀 Instalando programas básicos..." -ForegroundColor Green

foreach ($programa in $programas) {
    Write-Host "📦 Instalando: $programa" -ForegroundColor Yellow
    
    # Buscar e instalar con Winget
    winget install --name "$programa" --silent --accept-package-agreements
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✅ $programa instalado correctamente" -ForegroundColor Green
    } else {
        Write-Host "❌ Error instalando $programa" -ForegroundColor Red
    }
}

Write-Host "`n✅ Instalación completada" -ForegroundColor Green


📦 Cómo usarlo

1 Abre PowerShell como Administrador

Haz clic derecho en el botón de Inicio

Selecciona "Windows PowerShell (Administrador)"

O escribe PowerShell en buscar, haz clic derecho y "Ejecutar como administrador"

2 Ve a la carpeta donde está el script
cd C:\Users\TuUsuario\Descargas\

3 Ejecuta el script
.\instalar.ps1
4 Espera a que termine (tarda 2-5 minutos)

📸 Ejemplo de salida
text
🚀 Instalando programas básicos...
📦 Instalando: Google Chrome
✅ Google Chrome instalado correctamente
📦 Instalando: 7-Zip
✅ 7-Zip instalado correctamente
📦 Instalando: VLC Media Player
✅ VLC Media Player instalado correctamente

✅ Instalación completada

⚠️ Notas importantes
Requiere conexión a Internet (descarga los programas)

Requiere permisos de Administrador

Usa Winget (viene con Windows 10/11)

