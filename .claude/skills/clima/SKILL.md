---
name: clima
description: Consulta la temperatura y el estado del clima actual de una ciudad (por defecto Cochabamba, Bolivia) usando wttr.in. Usar cuando el usuario pida el clima, la temperatura o el tiempo de una ciudad.
argument-hint: "[ciudad, país]"
allowed-tools: Bash(curl:*), PowerShell(curl.exe:*)
---

# Clima

Obtén el clima actual de la ciudad indicada en `$ARGUMENTS`.

**Ciudad por defecto: `Cochabamba,Bolivia`.** Úsala siempre que el usuario no nombre explícitamente otra ciudad (argumentos vacíos, "¿qué clima hace?", "¿cómo está el tiempo?", etc.). No intentes adivinar la ubicación por IP ni por otro contexto, y no preguntes qué ciudad: asume Cochabamba.

## Pasos

1. Arma la ciudad: reemplaza espacios por `+` (ej. `La Paz, Bolivia` → `La+Paz,Bolivia`).
2. Consulta wttr.in (en Windows usa `curl.exe`, no el alias `curl` de PowerShell):

   ```
   curl.exe -s "https://wttr.in/<CIUDAD>?format=%l:+%t+(sensación+%f)+%C+humedad:%h+viento:%w&lang=es"
   ```

3. Si la respuesta está vacía, trae un error o contiene `Unknown location`, dile al usuario que no se encontró la ciudad o que el servicio no responde. No inventes datos.
4. Responde en español, en una o dos líneas:
   - Ciudad, temperatura, sensación térmica, estado del cielo, humedad y viento.
   - Indica que la fuente es wttr.in.

## Notas

- wttr.in actualiza cada 15–30 min aprox.; consultas más frecuentes devuelven el mismo dato.
- Para pronóstico de 3 días (solo si el usuario lo pide), usa `https://wttr.in/<CIUDAD>?T&lang=es`.
