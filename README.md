# Proyecto Big Data - Fintech

## 📋 Descripción

Este proyecto implementa una solución de procesamiento de datos para una empresa fintech utilizando **Azure Data Factory** y **Azure Blob Storage**. El sistema procesa datos de transacciones, usuarios y onboarding para generar insights y métricas de negocio.

## 🏗️ Arquitectura

El proyecto está estructurado como una solución de Azure Data Factory con los siguientes componentes:

### Componentes Principales

- **Data Factory**: `fintech-bigdata-adf` - Factoría de datos principal
- **Pipeline**: `Pipeline` - Orquestador del flujo de procesamiento
- **Data Flow**: `Procesamiento` - Transformaciones y limpieza de datos
- **Linked Services**: Conexión a Azure Blob Storage
- **Datasets**: Definiciones de fuentes de datos

## 📊 Fuentes de Datos

### 1. Transacciones (`bt_users_transactions.csv`)
- **Campos**: user_id, transaction_dt, type, segment
- **Descripción**: Datos de transacciones de usuarios
- **Filtros aplicados**: 
  - Solo transacciones válidas (type ≤ 7 y segment == 1, o segment == 2)
  - Usuarios que comienzan con 'MLB'

### 2. Usuarios (`lk_users.csv`)
- **Campos**: user_id, name, email, address, birth_dt, phone, type, rubro
- **Descripción**: Información demográfica y de contacto de usuarios
- **Tipos**: Individuals y Sellers

### 3. Onboarding (`lk_onboarding.csv`)
- **Campos**: user_id, first_login_dt, habito, activacion, setup, return
- **Descripción**: Métricas de onboarding y engagement de usuarios

## 🔄 Proceso de Transformación

### Pipeline Principal
El pipeline ejecuta un Data Flow llamado "Procesamiento" que realiza las siguientes operaciones:

1. **Limpieza de Datos**:
   - Eliminación de filas inconsistentes
   - Filtrado de datos válidos
   - Eliminación de columnas innecesarias

2. **Transformaciones**:
   - Separación de usuarios por tipo (Individuals vs Sellers)
   - Conteo de transacciones por segmento
   - Cálculo de métricas de engagement

3. **Métricas Generadas**:
   - **Drop**: Tasa de abandono
   - **Setup**: Configuración de cuenta
   - **Hábito**: Frecuencia de uso
   - **Activación**: Activación de servicios

### Configuración de Procesamiento
- **Tipo de Compute**: General
- **Core Count**: 8
- **Timeout**: 12 horas
- **Trace Level**: Fine

## 📁 Estructura del Proyecto

```
tp-big-data/
├── dataflow/
│   └── fintech.json
├── dataset/
│   ├── onboarding.json
│   ├── transactions.json
│   └── users.json
├── factory/
│   └── itba-bigdata.json
├── linkedService/
│   └── AzureBlobStorage1.json
├── pipeline/
│   └── pipeline1.json
├── src/
│   ├── dataflow/
│   │   └── Procesamiento.json
│   ├── dataset/
│   │   ├── dropRes.json
│   │   ├── onboardingCsv.json
│   │   ├── usersCsv.json
│   │   └── usersTransactionsCsv.json
│   ├── factory/
│   │   ├── fintech-bigdata-adf.json
│   │   ├── fintech-bigdata.json
│   │   ├── fintech-bigdata-adf.json
│   │   └── TP-Factory-BigData.json
│   ├── linkedService/
│   │   └── AzureBlobStorage1.json
│   └── pipeline/
│       └── Pipeline.json
└── TP-Factory-BigData/
    ├── ARMTemplateForFactory.json
    ├── ARMTemplateParametersForFactory.json
    ├── globalParameters/
    └── linkedTemplates/
```

## 🚀 Despliegue

### Prerrequisitos
- Suscripción de Azure activa
- Azure Data Factory habilitado
- Azure Blob Storage configurado
- Datos de entrada cargados en el contenedor `bigdatacsv`

### Pasos de Despliegue

1. **Configurar Azure Blob Storage**:
   - Crear contenedor `bigdatacsv`
   - Cargar archivos CSV de entrada

2. **Desplegar Data Factory**:
   ```bash
   # Usar los templates ARM en TP-Factory-BigData/
   az deployment group create \
     --resource-group <resource-group> \
     --template-file TP-Factory-BigData/ARMTemplateForFactory.json \
     --parameters TP-Factory-BigData/ARMTemplateParametersForFactory.json
   ```

3. **Publicar Configuración**:
   - Usar `publish_config.json` para configurar parámetros globales
   - Ejecutar pipeline desde Azure Data Factory Studio

## 📈 Resultados

El procesamiento genera los siguientes outputs en Azure Blob Storage:

- **Datos Pre-procesados**: Versiones limpias de los datasets originales
- **Métricas de Engagement**: 
  - Tasa de drop por usuario
  - Métricas de setup y activación
  - Indicadores de hábito de uso
- **Datos Transformados**: Datasets enriquecidos con métricas calculadas

## 🔧 Configuración

### Parámetros Globales
- Ubicación: `eastus`
- Tipo de identidad: SystemAssigned
- Configuración de compute optimizada para procesamiento general

### Monitoreo
- Logs detallados habilitados (Trace Level: Fine)
- Timeout configurado para 12 horas
- Sin reintentos automáticos (retry: 0)

## 📝 Notas Técnicas

- **Formato de Fechas**: Las fechas se procesan en formato 'yyyy-MM-dd'
- **Filtros de Calidad**: Se aplican filtros para asegurar consistencia de datos
- **Particionamiento**: Se utiliza particionamiento hash para optimizar el procesamiento
- **Encoding**: UTF-8 para archivos CSV

---

**Desarrollado con Azure Data Factory** 🚀 