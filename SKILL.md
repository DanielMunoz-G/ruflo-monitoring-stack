---
name: ruflo-orchestration
description: RUFLO es el framework de orquestación de agentes para Claude. Orquesta múltiples agentes especializados en paralelo o secuencial para automatizar workflows complejos, reduciendo consumo de tokens y aumentando eficiencia. Usa esta skill cuando haya: labs complejos multi-componente, tareas que requieran agentes especializados, automatización de procesos IT, análisis paralelos, documentación simultánea, o cualquier workflow que beneficie de paralelización. TRIGGER AUTOMÁTICAMENTE para tareas complejas que podrían dividirse en sub-agentes.
compatibility: Multi-agent orchestration, parallel processing, distributed workflows, IT automation
---

# RUFLO: Agent Orchestration Framework for Claude

## Overview

RUFLO (anteriormente Claude Flow) es la plataforma de orquestación de agentes para Claude que permite desplegar enjambres de agentes especializados trabajando en paralelo, compartiendo memoria y votando decisiones. La idea es simple: un solo agente Claude es poderoso, pero 3-8 agentes especializados trabajando en paralelo hacen mucho más.

**Versión actual**: v3.5 con más de 250,000 líneas de código reescrito en TypeScript y kernels WASM en Rust.

---

## Por Qué RUFLO (vs Single-Agent)

### ❌ Enfoque Single-Agent (Ineficiente)
```
Tu tarea → Claude hace TODO
  ├─ Analiza código
  ├─ Escribe documentación  
  ├─ Genera tests
  └─ Valida resultados

Problema: Alto consumo de tokens, lento, sin especialización
```

### ✅ Enfoque RUFLO (Optimizado)
```
Tu tarea → RUFLO Orchestrator
  ├─ Agent A (Análisis) ]
  ├─ Agent B (Documentación) ] EN PARALELO
  ├─ Agent C (Tests) ]
  └─ Agent D (Validación)

Resultado: Menos tokens (-20-30%), más rápido, especializado
```

---

## Core Concepts

### 1. **Swarm (Enjambre)**
Un enjambre es un grupo de agentes especializados que se auto-organizan, aprenden de cada tarea, recuerdan entre sesiones, y se comunican de forma segura sin filtrar datos.

### 2. **Agent Specialization**
Cada agente se especializa:
- **Analyzer**: Entiende datos, identifica problemas
- **Coder**: Genera código y scripts
- **Documentor**: Escribe reportes y guías
- **Validator**: Valida calidad de resultados
- **Orchestrator**: Coordina todo

### 3. **Self-Learning**
RUFLO v3 introduce capacidades de aprendizaje neural que ningún otro framework de orquestación ofrece. Aprende de cada ejecución, previene olvido catastrófico de patrones exitosos, e inteligentemente enruta trabajo a expertos especializados.

### 4. **Memory & State**
- Memoria compartida entre agentes
- Cada agente recuerda su contexto
- Sintonía cruzada entre sesiones

---

## Cuándo Usar RUFLO

### ✅ USAR RUFLO Cuando:

1. **Labs/Proyectos Multi-Componente**
   - Instalar Prometheus + Grafana + Alertmanager simultáneamente
   - Configurar VirtualBox + redes + servicios
   - Deploy complejo con múltiples pasos

2. **Automatización de Procesos**
   - Pipeline: capturar → analizar → documentar → validar
   - Workflows que se repiten regularmente
   - Tareas que pueden paralelizarse

3. **Necesitas Especialización**
   - Un agente experto en análisis técnico
   - Otro en documentación clara
   - Otro en generación de código
   - Otro en validación/QA

4. **Reducir Consumo de Tokens**
   - Workflows que normalmente repiten contexto
   - Proyectos con muchos pasos secuenciales
   - Análisis paralelos sin comunicación constante

### ❌ NO USAR RUFLO Cuando:
- Preguntas simples ("¿Qué es...?")
- Tareas que requieren comunicación constante entre agentes
- Conversaciones breves

---

## Topologías de Agents

### 1. **Hierarchical (Recomendada para Principiantes)**
```
       Orchestrator (Queen)
            │
    ┌───────┼───────┐
    │       │       │
  Agent A  Agent B  Agent C
```
✅ Simple, fácil de entender
✅ Buena para proyectos pequeños
❌ El orchestrator es cuello de botella

### 2. **Mesh (Avanzada)**
```
Agent A ←→ Agent B
  ↓       ↓
Agent C ←→ Agent D
```
✅ Agentes se comunican directamente
✅ Paralela verdadera
❌ Más compleja

### 3. **Pipeline (Para Workflows Secuenciales)**
```
Agent A → Agent B → Agent C → Agent D
```
✅ Datos fluyen de forma clara
✅ Cada agente refina output del anterior

---

## Arquitectura RUFLO

```
┌────────────────────────────────────────┐
│  Tu Solicitud                          │
└───────────────┬────────────────────────┘
                │
┌───────────────▼────────────────────────┐
│  RUFLO CLI / Router                    │
│  (Entiende lo que necesitas)           │
└───────────────┬────────────────────────┘
                │
┌───────────────▼────────────────────────┐
│  Orchestrator (Queen Agent)            │
│  (Divide en sub-tareas)                │
└────────┬───────────────┬───────────────┘
         │               │
    ┌────▼───┐      ┌────▼────┐
    │ Agent A │      │ Agent B  │  (En paralelo)
    │ Analyze │      │ Document │
    └────┬────┘      └────┬─────┘
         │               │
    ┌────▼───────────────▼─┐
    │  Memory Store        │  (Compartida)
    │  (Shared Context)    │
    └──────┬───────────────┘
           │
    ┌──────▼──────────────┐
    │ Aggregator          │
    │ (Junta resultados)  │
    └─────────────────────┘
```

---

## Instalación & Setup

### Paso 1: Instalar RUFLO

```bash
npm install -g @claude-flow/cli@3.6.10
# o si prefieres el nombre histórico:
npm install -g claude-flow@3.6.10
```

### Paso 2: Inicializar

```bash
# Con checkpoints locales (Git)
ruflo init

# Con GitHub integrado (Avanzado)
ruflo init --github
```

### Paso 3: Crear tu primer swarm

```bash
# Para un proyecto pequeño (3 agentes)
ruflo swarm create --name "mi-proyecto" --size 3 --topology hierarchical

# Ver agentes disponibles
ruflo agents list
```

---

## Ejemplo Práctico: Lab de Monitoreo

### Escenario
Necesitas:
1. Instalar Prometheus
2. Configurar Grafana
3. Escribir documentación
4. Crear checklist de validación

### Con RUFLO

```bash
ruflo swarm start --objective "Setup monitoring stack" \
  --agents 4 \
  --topology hierarchical
```

El swarm automáticamente:
- **Agent 1 (Automation)**: Genera scripts de instalación
- **Agent 2 (Config)**: Configura Prometheus + Grafana
- **Agent 3 (Documentation)**: Escribe guía paso-a-paso
- **Agent 4 (Validation)**: Crea checklist de validación

Todo en paralelo. Output final: workflow completo integrado.

### Resultado
```
✓ Installation scripts (Agent 1)
✓ Configuration files (Agent 2)
✓ Step-by-step documentation (Agent 3)
✓ Validation checklist (Agent 4)

→ Integrated monitoring-setup.md
```

---

## Comandos RUFLO Esenciales

```bash
# Ver status del swarm
ruflo swarm status

# Detener swarm
ruflo swarm stop

# Ver resultados
ruflo results show

# Memory del swarm
ruflo memory list

# Debugging
ruflo debug --verbose
```

---

## Optimization Tips

### 1. **Contexto Mínimo**
- Cada agente recibe SOLO su parte
- No repitas contexto completo
- Usa memoria compartida

### 2. **Especialización Clara**
- Define el rol de cada agente explícitamente
- No hagas que hagan demasiado

### 3. **Validación Final**
- Siempre incluye validator agent
- Checkea completitud y calidad

### 4. **Tamaño del Swarm**
- Empezar con 3 agentes
- Máximo 8 para proyectos complejos
- Más = overhead de coordinación

### 5. **Topología**
- Hierarchical: Principiantes
- Mesh: Cuando necesitas paralelización verdadera
- Pipeline: Workflows secuenciales claros

---

## Ejemplo Práctico: Tu Lab de Zabbix

### Solicitud
"Usa RUFLO para mi lab de Zabbix 7.0 en VirtualBox"

### Qué RUFLO hace

```
Orchestrator divide en:

1. Agent AUTOMATION
   → Genera docker-compose para Zabbix
   → Scripts de base de datos
   → Configuración de storage

2. Agent DOCUMENTER
   → Guía de instalación en español
   → Troubleshooting
   → Mejores prácticas

3. Agent CONFIGURATION
   → Templates de monitoreo
   → Alertas
   → Dashboards

4. Agent VALIDATOR
   → Test de conectividad
   → Verificación de métricas
   → Checklist post-instalación
```

### Output
```
zabbix-lab-complete/
├── docker-compose.yml
├── installation-guide.md
├── templates/
├── validation-checklist.txt
└── troubleshooting.md
```

---

## Anti-Patterns

❌ **Demasiados agentes**
- Máximo 3-5 para tareas pequeñas
- Máximo 8-10 para proyectos grandes

❌ **Sin definir roles claros**
- Cada agente debe saber exactamente qué hace

❌ **No validar output**
- Siempre incluye validación

❌ **Usar RUFLO para tareas simples**
- "¿Qué es ICMP?" → No necesita RUFLO

❌ **Ignorar memoria compartida**
- Aprovecha que los agentes comparten contexto

---

## Recursos Oficiales

- **GitHub**: https://github.com/ruvnet/ruflo
- **Docs**: https://github.com/ruvnet/ruflo/wiki
- **User Guide**: https://github.com/ruvnet/ruflo/blob/main/docs/USERGUIDE.md
- **Community**: GitHub Discussions en ruflo repo

---

## Siguiente Paso

Cuando me pidas algo complejo:

```
"Usa RUFLO para [proyecto]"
```

Yo automáticamente:
1. Identifico subtareas
2. Asigno agentes especializados
3. Ejecuto en paralelo
4. Valido resultados
5. Integro output final

¡Listo para optimizar tus workflows! 🚀

---

## Licencia

MIT © 2026 Daniel Muñoz
