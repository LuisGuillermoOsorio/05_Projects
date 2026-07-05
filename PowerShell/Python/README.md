# Python - Organizador Automático de Archivos

## 📋 Descripción
Script en Python que organiza automáticamente los archivos de una carpeta en subcarpetas según su tipo (imágenes, PDFs, videos, documentos, etc.) y fecha de modificación.

## 🚀 Script

```python
import os
import shutil
import time
from datetime import datetime

# Diccionario de extensiones a organizar
EXTENSIONES = {
    ".jpg": "Imagenes",
    ".png": "Imagenes", 
    ".jpeg": "Imagenes",
    ".gif": "Imagenes",
    ".pdf": "PDFs",
    ".mp4": "Videos",
    ".avi": "Videos",
    ".mov": "Videos",
    ".docx": "Documentos_Word",
    ".xlsx": "Documentos_Excel",
    ".pptx": "Documentos_PowerPoint",
    ".pbix": "Documentos_PowerBi",
    ".exe": "Ejecutables",
    ".msi": "Ejecutables",
    ".mp3": "Musica",
    ".zip": "Comprimidos",
    ".rar": "Comprimidos",
    ".txt": "Textos",
    ".py": "Codigo_Python",
    ".js": "Codigo_JavaScript",
    ".html": "Codigo_HTML",
    ".css": "Codigo_CSS",
}

def organizar_carpeta(ruta):
    """Organiza todos los archivos de la carpeta seleccionada"""
    
    # Verificar que la ruta existe
    if not os.path.exists(ruta):
        print(f"❌ La carpeta '{ruta}' no existe")
        return
    
    print(f"\n📂 Organizando: {ruta}")
    
    # Crear carpetas necesarias
    carpetas_creadas = set()
    for carpeta in EXTENSIONES.values():
        ruta_carpeta = os.path.join(ruta, carpeta)
        if not os.path.exists(ruta_carpeta):
            os.makedirs(ruta_carpeta)
            carpetas_creadas.add(carpeta)
    
    if carpetas_creadas:
        print(f"✅ Carpetas creadas: {', '.join(carpetas_creadas)}")
    
    # Contador de archivos movidos
    contador = 0
    
    # Recorrer archivos
    for archivo in os.listdir(ruta):
        ruta_archivo = os.path.join(ruta, archivo)
        
        # Saltar si no es archivo o es el script mismo
        if not os.path.isfile(ruta_archivo) or archivo == "organizar.py":
            continue
        
        # Obtener extensión
        nombre, ext = os.path.splitext(archivo)
        ext = ext.lower()
        
        # Determinar carpeta destino
        carpeta_destino = EXTENSIONES.get(ext, "Otros")
        
        # Obtener fecha de modificación
        try:
            fecha_mod = datetime.fromtimestamp(os.path.getmtime(ruta_archivo))
            subcarpeta = fecha_mod.strftime("%Y-%m")  # Ej: 2024-12
        except:
            subcarpeta = "Sin_Fecha"
        
        # Ruta completa de destino
        destino = os.path.join(ruta, carpeta_destino, subcarpeta)
        os.makedirs(destino, exist_ok=True)
        
        # Manejar archivos duplicados
        ruta_destino = os.path.join(destino, archivo)
        if os.path.exists(ruta_destino):
            nombre_base, ext_archivo = os.path.splitext(archivo)
            ruta_destino = os.path.join(destino, f"{nombre_base}_{int(time.time())}{ext_archivo}")
        
        # Mover archivo
        try:
            shutil.move(ruta_archivo, ruta_destino)
            contador += 1
            print(f"  ✅ {archivo} → {carpeta_destino}/{subcarpeta}")
        except Exception as e:
            print(f"  ❌ Error moviendo {archivo}: {str(e)}")
    
    print(f"\n✅ ¡Completado! Se organizaron {contador} archivos")

# ============================================
# EJECUCIÓN
# ============================================

if __name__ == "__main__":
    print("\n" + "="*50)
    print("📂 ORGANIZADOR AUTOMÁTICO DE ARCHIVOS")
    print("="*50)
    print("\nEste script organizará tus archivos en carpetas")
    print("según su tipo (Imágenes, PDFs, Videos, etc.)")
    
    # Pedir la carpeta al usuario
    ruta = input("\n📁 Ruta de la carpeta a organizar: ").strip()
    
    # Si no escribe nada, usar la carpeta actual
    if not ruta:
        ruta = os.getcwd()
        print(f"📁 Usando carpeta actual: {ruta}")
    
    # Confirmar
    print(f"\n📂 Carpeta seleccionada: {ruta}")
    confirmar = input("¿Deseas continuar? (s/n): ").strip().lower()
    
    if confirmar == 's':
        organizar_carpeta(ruta)
    else:
        print("❌ Operación cancelada")

📦 Cómo usarlo
Requisitos previos
Python 3.x instalado

No requiere librerías externas (solo usa módulos nativos)

Pasos para ejecutar
Guarda el script en un archivo llamado organizar.py

Abre una terminal (CMD, PowerShell o Terminal)

Navega a la carpeta donde guardaste el script:

bash
cd C:\Users\TuUsuario\Descargas
Ejecuta el script:

bash
python organizar.py
Escribe la ruta de la carpeta que quieres organizar:

text
📁 Ruta de la carpeta a organizar: C:\Users\TuUsuario\Descargas
Confirma escribiendo 's' y presiona Enter

Ejemplo de uso
text
📂 Organizando: C:\Users\TuUsuario\Descargas
✅ Carpetas creadas: Imagenes, PDFs, Videos, Documentos_Word
  ✅ factura.pdf → PDFs/2024-12
  ✅ foto.jpg → Imagenes/2024-12
  ✅ video.mp4 → Videos/2024-12
  ✅ contrato.docx → Documentos_Word/2024-12

✅ ¡Completado! Se organizaron 4 archivos

Estructura que crea
text
Carpeta_Original/
├── Imagenes/
│   └── 2024-12/
│       ├── foto.jpg
│       └── imagen.png
├── PDFs/
│   └── 2024-12/
│       └── factura.pdf
├── Videos/
│   └── 2024-12/
│       └── video.mp4
├── Documentos_Word/
│   └── 2024-12/
│       └── contrato.docx
└── Otros/
    └── 2024-12/
        └── archivo.xyz

⚠️ Notas importantes
¡Haz una copia de seguridad! Siempre antes de organizar archivos importantes

No ejecutes el script en la carpeta del sistema (C:\Windows, C:\Program Files)

Si un archivo está en uso, el script lo omitirá y continuará con los demás

Los archivos se organizan por tipo y por año-mes de modificación

🛠️ Personalización
Puedes agregar más extensiones en el diccionario EXTENSIONES:

python
EXTENSIONES = {
    # ... extensiones existentes ...
    ".ps1": "Scripts_PowerShell",  # Agregar PowerShell
    ".bat": "Scripts_Batch",       # Agregar Batch
    ".sql": "Bases_Datos",         # Agregar SQL
}
