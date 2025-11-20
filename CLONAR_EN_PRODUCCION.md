# Instrucciones para Configurar Git en la Carpeta de Producción

Este documento contiene las instrucciones para configurar el control de versiones Git en la carpeta de producción del mismo servidor donde está el desarrollo.

## Información del Repositorio

- **Repositorio GitHub**: https://github.com/GabrielFerrin/faryb-odoo
- **Rama de Producción**: `main`
- **Rama de Desarrollo**: `gabriel`
- **Método de Autenticación**: SSH Keys (reusando la misma clave del desarrollo)
- **Usuario Git**: GabrielFerrin
- **Email Git**: gabodesarrollo@gmail.com

## Flujo de Trabajo

1. **Desarrollo**: Trabajas en la carpeta de desarrollo, pruebas y haces commits
2. **Push a GitHub**: Subes cambios a la rama `gabriel`
3. **Merge en GitHub**: Fusionas `gabriel` → `main` en GitHub (pull request o merge directo)
4. **Pull en Producción**: Desde la carpeta de producción, haces `git pull origin main`

## Pasos para Configurar en Producción

### 1. Verificar que la Clave SSH Existe (Reusar la del Desarrollo)

Como ambos proyectos están en el mismo servidor, puedes reusar la misma clave SSH:

```bash
# Verificar que la clave SSH existe
ls -la ~/.ssh/id_ed25519*

# Si existe, verificar la conexión con GitHub
ssh -T git@github.com
```

Deberías ver un mensaje como:
```
Hi GabrielFerrin! You've successfully authenticated, but GitHub does not provide shell access.
```

**Nota**: Si la clave SSH ya está configurada en desarrollo, no necesitas generar una nueva. La misma clave funcionará para ambas carpetas.

### 2. Navegar a la Carpeta de Producción

```bash
# Ajusta la ruta según tu configuración
cd /ruta/a/tu/carpeta/produccion/addons
```

### 3. Inicializar el Repositorio Git (si no está inicializado)

```bash
# Si la carpeta NO tiene git inicializado:
git init
```

### 4. Agregar el Repositorio Remoto

```bash
git remote add origin git@github.com:GabrielFerrin/faryb-odoo.git

# O si ya existe el remote, actualízalo:
git remote set-url origin git@github.com:GabrielFerrin/faryb-odoo.git
```

### 5. Configurar Usuario y Email (Solo para este Repositorio)

```bash
git config user.name "GabrielFerrin"
git config user.email "gabodesarrollo@gmail.com"
```

**Nota**: Se usa `git config` sin `--global` para que solo aplique a este repositorio.

### 6. Traer el Código de la Rama de Producción

```bash
# Traer todas las ramas del repositorio remoto
git fetch origin

# Cambiar a la rama main (producción)
git checkout -b main origin/main

# O si ya existe la rama main local:
git checkout main
git pull origin main
```

### 7. Verificar la Configuración

```bash
git status
git remote -v
git branch
git config --local --list | grep -E "(user.name|user.email)"
```

Deberías ver:
- El remote `origin` apuntando a `git@github.com:GabrielFerrin/faryb-odoo.git`
- Estar en la rama `main` (producción)
- Usuario: `GabrielFerrin` y Email: `gabodesarrollo@gmail.com`

## Estructura de Ramas

- **`main`**: Rama de producción. Esta es la rama que debe estar activa en el servidor de producción.
- **`gabriel`**: Rama de desarrollo. Se usa para desarrollo y pruebas antes de hacer merge a `main`.

## Comandos Útiles en Producción

### Ver el Estado del Repositorio

```bash
git status
```

### Traer Cambios de la Rama de Producción (Después de hacer merge en GitHub)

```bash
# Asegúrate de estar en la rama main
git checkout main

# Traer los últimos cambios de GitHub
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

### Verificar que Estás en la Rama Correcta

```bash
git branch
```

Deberías ver `* main` (la rama de producción).

### Ver la Configuración del Repositorio

```bash
git config --local --list
```

### Ver los Últimos Cambios Antes de Hacer Pull

```bash
# Ver qué cambios hay en GitHub que no tienes localmente
git fetch origin
git log HEAD..origin/main --oneline
```

## Notas Importantes

1. **Rama de Producción**: Siempre trabaja con la rama `main` en producción. No hagas cambios directamente en producción.

2. **Configuración Local**: La configuración de usuario y email es solo para este repositorio, no afecta otros proyectos Git en el servidor.

3. **SSH Keys**: Como ambos proyectos (desarrollo y producción) están en el mismo servidor, puedes reusar la misma clave SSH. No necesitas generar una nueva.

4. **Flujo de Trabajo**:
   - Desarrollas y pruebas en la carpeta de desarrollo
   - Haces `git push` a la rama `gabriel` desde desarrollo
   - En GitHub, haces merge de `gabriel` → `main`
   - En producción, haces `git pull origin main` para traer los cambios

5. **Seguridad**: Nunca hagas commits directamente en producción. Los cambios deben venir de la rama de desarrollo mediante merge en GitHub.

6. **Sincronización**: Para actualizar producción después de hacer merge en GitHub:
   ```bash
   cd /ruta/produccion/addons
   git checkout main
   git pull origin main
   ```

## Solución de Problemas

### Error: "Permission denied (publickey)"

- Verifica que la clave SSH esté agregada correctamente en GitHub
- Verifica que la clave esté en `~/.ssh/id_ed25519` (misma clave que usas en desarrollo)
- Prueba la conexión con: `ssh -T git@github.com`

### Error: "Host key verification failed"

- Ejecuta: `ssh-keyscan github.com >> ~/.ssh/known_hosts`

### Error: "fatal: not a git repository"

- Asegúrate de estar en el directorio correcto de producción
- Si no está inicializado, ejecuta: `git init`

### Error: "Your branch is behind 'origin/main'"

- Esto es normal después de hacer merge en GitHub. Ejecuta: `git pull origin main`

### Verificar que la Clave SSH Está Configurada Correctamente

```bash
# Ver el fingerprint de tu clave (debe ser el mismo que en desarrollo)
ssh-keygen -lf ~/.ssh/id_ed25519.pub

# Comparar con el que aparece en GitHub (en la página de SSH keys)
# Debe coincidir con la clave que usas en desarrollo
```

