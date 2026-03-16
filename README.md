import os
import subprocess
import sys
import argparse
import threading

# ===========================
# CONFIGURACIÓN
# ===========================
GITHUB_USER = "DaryRivero"
REPO_NAME = "proyecto-unificado-nanoforz"
EMAIL = "daryrivero000@gmail.com"
BRANCH_MAIN = "main"
BRANCH_BETA = "beta"
GITHUB_TOKEN = "TU_TOKEN_AQUI"  # Reemplazar con tu token personal

PROJECT_DIR = os.path.expanduser("~/proyecto_unificado_nanoforz")
LOG_DIR = os.path.join(PROJECT_DIR, "logs")

DASHBOARD_SCRIPT = os.path.join(PROJECT_DIR, "dashboard.py")
BROKER_SCRIPT = os.path.join(PROJECT_DIR, "broker_setup.sh")

# ===========================
# FUNCIONES
# ===========================
def init_project():
    os.makedirs(PROJECT_DIR, exist_ok=True)
    os.makedirs(LOG_DIR, exist_ok=True)
    os.chdir(PROJECT_DIR)
    print("[INFO] Directorio de proyecto listo:", PROJECT_DIR)
    
def setup_readme():
    contenido = f"""
# Proyecto Unificado Beta

Propietario: Dary Rivero  
Marca registrada: Nanoforz  
Indexa: Dary Rivero / Nanoforz  

Este proyecto integra nodos neuronales, dashboard único, broker MQTT, indexación de metadatos y protección de propiedad intelectual.
Todos los elementos visuales y código son propiedad del titular. Acceso no autorizado será registrado.
"""
    with open("README.md","w") as f:
        f.write(contenido)
    print("[INFO] README.md creado.")

def setup_git():
    subprocess.run(["git", "init"], check=True)
    subprocess.run(["git", "config", "user.name", GITHUB_USER], check=True)
    subprocess.run(["git", "config", "user.email", EMAIL], check=True)
    subprocess.run(["git", "add", "."], check=True)
    subprocess.run(["git", "commit", "-m", "Commit inicial beta Nanoforz"], check=True)
    
    remote_url = f"https://{GITHUB_USER}:{GITHUB_TOKEN}@github.com/{GITHUB_USER}/{REPO_NAME}.git"
    
    # Crear repositorio remoto si no existe
    import requests
    url = "https://api.github.com/user/repos"
    headers = {"Authorization": f"token {GITHUB_TOKEN}"}
    data = {"name": REPO_NAME, "private": True}
    requests.post(url, headers=headers, json=data)
    
    subprocess.run(["git", "branch", "-M", BRANCH_MAIN], check=True)
    subprocess.run(["git", "remote", "add", "origin", remote_url], check=True)
    subprocess.run(["git", "push", "-u", "origin", BRANCH_MAIN], check=True)
    
    subprocess.run(["git", "checkout", "-b", BRANCH_BETA], check=True)
    subprocess.run(["git", "push", "-u", "origin", BRANCH_BETA], check=True)
    print("[INFO] GitHub integrado con ramas main y beta.")

def setup_broker():
    if not os.path.exists(BROKER_SCRIPT):
        with open(BROKER_SCRIPT, "w") as f:
            f.write("""#!/bin/bash
