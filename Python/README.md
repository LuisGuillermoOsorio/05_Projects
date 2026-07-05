# Python - Organizador Automático de Archivos

## 📋 Descripción
Script en Python que organiza automáticamente los archivos de una carpeta en subcarpetas según su tipo (imágenes, PDFs, videos, documentos, etc.) y fecha de modificación.

## 🚀 Script

import os
import shutil
import getpass
import time
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
from tkinter import Tk, filedialog
from datetime import datetime

# Configuración inicial
usuario = getpass.getuser()  # Obtener usuario actual

# Diccionario de extensiones (Mantenido en MAYÚSCULAS)
EXTENSIONES = {
    # Imágenes
    ".jpg": "Imagenes",
    ".png": "Imagenes",
    ".jpeg": "Imagenes",
    ".gif": "Imagenes",
    ".bmp": "Imagenes",
    ".svg": "Imagenes",
    
    # Documentos
    ".pdf": "PDFs",
    ".docx": "Documentos_Word",
    ".xlsx": "Documentos_Excel",
    ".pptx": "Documentos_PowerPoint",
    ".txt": "Textos",
    
    # Videos
    ".mp4": "Videos",
    ".avi": "Videos",
    ".mov": "Videos",
    ".mkv": "Videos",
    
    # Música
    ".mp3": "Musica",
    ".wav": "Musica",
    ".flac": "Musica",
    
    # Comprimidos
    ".zip": "Comprimidos",
    ".rar": "Comprimidos",
    ".7z": "Comprimidos",
    
    # Código
    ".py": "Codigo_Python",
    ".js": "Codigo_JavaScript",
    ".html": "Codigo_HTML",
    ".css": "Codigo_CSS",
    ".ps1": "Scripts_PowerShell",
    ".sql": "Bases_Datos",
    
    # Ejecutables
    ".exe": "Ejecutables",
    ".msi": "Ejecutables",
    
    # Power BI
    ".pbix": "Documentos_PowerBi",
}

def esperar_archivo_libre(ruta_archivo, intentos=5, espera=1):
    """Espera a que un archivo esté disponible para su acceso"""
    for _ in range(intentos):
        try:
            with open(ruta_archivo, 'rb') as f:
                return True
        except (PermissionError, OSError):
            time.sleep(espera)
    return False

def crear_estructura_carpetas(ruta_base):
    """Crea todas las carpetas necesarias al inicio"""
    # Corregido: Se usa EXTENSIONES en mayúsculas
    for carpeta in set(EXTENSIONES.values()).union({"Otros"}):
        os.makedirs(os.path.join(ruta_base, carpeta), exist_ok=True)

def procesar_archivo(ruta_base, archivo):
    """Procesa y mueve un solo archivo a su destino correspondiente"""
    ruta_archivo = os.path.join(ruta_base, archivo)

    # Saltar si no es archivo o es el log
    if not os.path.isfile(ruta_archivo) or archivo == "log_movimientos.txt":
        return

    # Esperar si el archivo está en uso (ideal para descargas en curso)
    if not esperar_archivo_libre(ruta_archivo):
        print(f"⚠️ Archivo {archivo} bloqueado por el sistema. Omitiendo...")
        return

    # Obtener extensión del archivo
    nombre, ext = os.path.splitext(archivo)
    ext = ext.lower()

    # Determinar carpeta destino según extensión (Corregido: EXTENSIONES)
    carpeta_tipo = os.path.join(ruta_base, EXTENSIONES.get(ext, "Otros"))

    # Obtener fecha de modificación
    try:
        fecha_mod = datetime.fromtimestamp(os.path.getmtime(ruta_archivo))
        subcarpeta_fecha = fecha_mod.strftime("%Y-%B")
        carpeta_fecha = os.path.join(carpeta_tipo, subcarpeta_fecha)
    except Exception as e:
        print(f"❌ Error obteniendo fecha de {archivo}: {str(e)}")
        return

    try:
        # Crear estructura de carpetas destino
        os.makedirs(carpeta_fecha, exist_ok=True)

        # Generar ruta destino
        destino = os.path.join(carpeta_fecha, archivo)

        # Manejar archivos duplicados
        if os.path.exists(destino):
            nuevo_nombre = f"{nombre}_{int(time.time())}{ext}"
            destino = os.path.join(carpeta_fecha, nuevo_nombre)

        # Mover el archivo
        shutil.move(ruta_archivo, destino)

        # Registrar en log
        with open(os.path.join(ruta_base, "log_movimientos.txt"), "a", encoding="utf-8") as log:
            log.write(f"{datetime.now().strftime('%Y-%m-%d %H:%M:%S')} | {usuario} | {archivo.ljust(30)} → {destino}\n")
        
        print(f"✓ Movido: {archivo} → {destino}")

    except Exception as e:
        print(f"❌ Error moviendo {archivo}: {str(e)}")

def ordenar_archivos_existentes(ruta_base):
    """Organiza los archivos que ya existen en la carpeta al iniciar"""
    for archivo in os.listdir(ruta_base):
        procesar_archivo(ruta_base, archivo)

class ManejadorEventos(FileSystemEventHandler):
    """Manejador de eventos para Watchdog optimizado"""
    def on_created(self, event):
        if not event.is_directory:
            # Extraer solo el nombre del archivo del path absoluto
            archivo = os.path.basename(event.src_path)
            time.sleep(1)  # Estabilización para escrituras rápidas
            print(f"\n🔄 Nuevo archivo detectado: {archivo}")
            # Procesa SÓLO el archivo nuevo, no toda la carpeta
            procesar_archivo(ruta, archivo)

# --- FLUJO PRINCIPAL ---

# Seleccionar carpeta a organizar
ventana = Tk()
ventana.withdraw()
ruta = filedialog.askdirectory(title="Seleccione la carpeta a ordenar")

if ruta: # Verificar que el usuario no haya cancelado el diálogo
    # Crear estructura inicial de carpetas
    crear_estructura_carpetas(ruta)

    # Procesar archivos existentes
    print("\n🔍 Procesando archivos existentes...")
    ordenar_archivos_existentes(ruta)

    # Iniciar observador de cambios en tiempo real
    print(f"\n👀 Iniciando vigilancia en tiempo real: {ruta}")
    print("Presiona Ctrl+C para detener\n")

    event_handler = ManejadorEventos()
    observer = Observer()
    observer.schedule(event_handler, ruta, recursive=False)
    observer.start()

    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("\n🛑 Deteniendo el observador...")
        observer.stop()
    observer.join()
else:
    print("Operación cancelada por el usuario.")

    ## 📦 Cómo usarlo

1. **Guarda el script** en un archivo llamado `organizar.py`

2. **Abre PowerShell o CMD** como Administrador

3. **Navega a la carpeta** del script:
   ```bash
   cd C:\Users\TuUsuario\Descargas
   ```

4. **Ejecuta el script**:
   ```bash
   python organizar.py
   ```

5. **Escribe la ruta** de la carpeta a organizar
