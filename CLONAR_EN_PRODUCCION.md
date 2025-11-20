# Instrucciones para Clonar el Repositorio en Producción

Este documento contiene las instrucciones para clonar el repositorio `faryb-odoo` en el servidor de producción usando SSH.

## Información del Repositorio

- **Repositorio GitHub**: https://github.com/GabrielFerrin/faryb-odoo
- **Rama de Producción**: `main`
- **Rama de Desarrollo**: `gabriel`
- **Método de Autenticación**: SSH Keys
- **Usuario Git**: GabrielFerrin
- **Email Git**: gabodesarrollo@gmail.com

## Pasos para Configurar en Producción

### 1. Generar Clave SSH (si no existe)

```bash
ssh-keygen -t ed25519 -C "gabodesarrollo@gmail.com" -f ~/.ssh/id_ed25519
```

Presiona Enter para aceptar la ubicación por defecto y deja la contraseña vacía (o configura una si prefieres).

### 2. Mostrar la Clave Pública

```bash
cat ~/.ssh/id_ed25519.pub
```

**Copia toda la salida** que empieza con `ssh-ed25519` y termina con tu email.

### 3. Agregar la Clave SSH a GitHub

1. Ve a: https://github.com/settings/keys
2. Click en el botón verde **"New SSH key"**
3. En **Title**: escribe `Servidor Odoo Producción` (o el nombre que prefieras)
4. En **Key**: pega la clave completa que copiaste en el paso 2
5. Click en **"Add SSH key"**

### 4. Verificar la Conexión SSH con GitHub

```bash
ssh -T git@github.com
```

Deberías ver un mensaje como:
```
Hi GabrielFerrin! You've successfully authenticated, but GitHub does not provide shell access.
```

### 5. Agregar GitHub a known_hosts (evita advertencias)

```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

### 6. Clonar el Repositorio

```bash
# Navegar al directorio donde quieres clonar (ajusta la ruta según tu configuración)
cd /ruta/a/tu/directorio

# Clonar el repositorio
git clone git@github.com:GabrielFerrin/faryb-odoo.git addons

# O si ya existe el directorio addons y quieres clonar ahí:
cd /ruta/a/tu/directorio/addons
git clone git@github.com:GabrielFerrin/faryb-odoo.git .
```

### 7. Configurar Usuario y Email (Solo para este Repositorio)

```bash
cd /ruta/a/tu/directorio/addons
git config user.name "GabrielFerrin"
git config user.email "gabodesarrollo@gmail.com"
```

**Nota**: Se usa `git config` sin `--global` para que solo aplique a este repositorio.

### 8. Verificar que Estás en la Rama de Producción

```bash
cd /ruta/a/tu/directorio/addons
git branch
```

Deberías ver `* main` (la rama de producción).

Si estás en otra rama, cambia a main:

```bash
git checkout main
```

### 9. Verificar la Configuración

```bash
cd /ruta/a/tu/directorio/addons
git status
git remote -v
git config --local --list | grep -E "(user.name|user.email)"
```

Deberías ver:
- Estado limpio del repositorio
- El remote `origin` apuntando a `git@github.com:GabrielFerrin/faryb-odoo.git`
- Usuario: `GabrielFerrin` y Email: `gabodesarrollo@gmail.com`

## Estructura de Ramas

- **`main`**: Rama de producción. Esta es la rama que debe estar activa en el servidor de producción.
- **`gabriel`**: Rama de desarrollo. Se usa para desarrollo y pruebas antes de hacer merge a `main`.

## Comandos Útiles en Producción

### Ver el Estado del Repositorio

```bash
git status
```

### Traer Cambios de la Rama de Producción

```bash
git pull origin main
```

### Ver el Historial de Commits

```bash
git log --oneline -10
```

### Ver las Ramas Disponibles

```bash
git branch -a
```

### Cambiar a la Rama de Producción

```bash
git checkout main
```

### Ver la Configuración del Repositorio

```bash
git config --local --list
```

## Notas Importantes

1. **Rama de Producción**: Siempre trabaja con la rama `main` en producción. No hagas cambios directamente en producción.

2. **Configuración Local**: La configuración de usuario y email es solo para este repositorio, no afecta otros proyectos Git en el servidor.

3. **SSH Keys**: Cada servidor debe tener su propia clave SSH agregada a GitHub. No compartas claves privadas entre servidores.

4. **Sincronización**: Para actualizar producción, usa `git pull origin main` desde la rama `main`.

5. **Seguridad**: Nunca hagas commits directamente en producción. Los cambios deben venir de la rama de desarrollo mediante pull requests o merge.

## Solución de Problemas

### Error: "Permission denied (publickey)"

- Verifica que la clave SSH esté agregada correctamente en GitHub
- Verifica que la clave esté en `~/.ssh/id_ed25519.pub`
- Prueba la conexión con: `ssh -T git@github.com`

### Error: "Host key verification failed"

- Ejecuta: `ssh-keyscan github.com >> ~/.ssh/known_hosts`

### Error: "fatal: not a git repository"

- Asegúrate de estar en el directorio correcto donde clonaste el repositorio

### Verificar que la Clave SSH Está Configurada Correctamente

```bash
# Ver el fingerprint de tu clave
ssh-keygen -lf ~/.ssh/id_ed25519.pub

# Comparar con el que aparece en GitHub (en la página de SSH keys)
```

