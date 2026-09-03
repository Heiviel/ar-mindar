# Instrucciones del proyecto

## Backup a GitHub

Este repo tiene un hook `Stop` (`.claude/settings.json`) que hace commit + push
automatico a `origin/main` (https://github.com/Heiviel/ar-mindar) al terminar
cada sesion de Claude Code, si hay cambios pendientes.

**Regla: solo se sube el codigo necesario para que la app funcione.**
Nunca se suben `models/*` ni `targets/*` (excepto los `.gitkeep`) — el usuario
los selecciona en local desde la propia app (ver "Modelo y marcador" en
`GUIA_USO.md`), no hace falta tenerlos en el repo. Estan excluidos en
`.gitignore`. Si en algun momento hace falta versionar un asset puntual,
preguntar al usuario antes de sacarlo del `.gitignore` (los GLB grandes
ademas chocan con el limite de 100MB por archivo de GitHub).
