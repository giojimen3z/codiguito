# codiguito
estoe. sun codiguito

Genera un archivo en Go llamado `auxiliary_functions.go` que implemente funciones auxiliares para manejar repositorios, pipelines y secretos, asegurando idempotencia. 

### Especificaciones:
1. **Simulación en memoria:** Usa mapas en memoria para simular el estado de los recursos:
   - `processedRequests`: Mapa para rastrear solicitudes ya procesadas (`map[string]bool`).
   - `repos`: Mapa para simular repositorios creados (`map[string]bool`).
   - `pipelines`: Mapa para simular pipelines creados (`map[string]bool`).
   - `secrets`: Mapa anidado para simular secretos por repositorio (`map[string]map[string]string`).

2. **Funciones auxiliares para manejar repositorios:**
   - `checkRepoExists(repoName string) (bool, error)`: Verifica si un repositorio existe.
   - `createRepo(repoName string) error`: Crea un repositorio, pero lanza un error si ya existe.
   - `createRepoIfNotExists(repoName string) error`: Crea un repositorio solo si no existe, manejando idempotencia.

3. **Funciones auxiliares para manejar pipelines:**
   - `checkPipelineExists(repoName string) (bool, error)`: Verifica si un pipeline existe.
   - `createPipeline(repoName string) error`: Crea un pipeline, pero lanza un error si ya existe.
   - `createPipelineIfNotExists(repoName string) error`: Crea un pipeline solo si no existe.

4. **Funciones auxiliares para manejar secretos:**
   - `checkSecretExists(repoName, secretKey string) (bool, error)`: Verifica si un secreto existe.
   - `createSecret(repoName, key, value string) error`: Crea un secreto, pero lanza un error si ya existe.
   - `createSecretIfNotExists(repoName, key, value string) error`: Crea un secreto solo si no existe.

5. **Comportamiento de idempotencia:**
   - Si un recurso ya existe (repositorio, pipeline o secreto), la función no debe lanzar un error. Simplemente retorna sin realizar la operación.

Organiza estas funciones para que sean reutilizables y separadas del handler HTTP.

promp 2
Genera un archivo en Go llamado `main_handler.go` que implemente un servidor HTTP con un endpoint `/create-resources`.

### Especificaciones del endpoint:
1. **Solicitud JSON:**
   - `idempotency` (bool): Si es `true`, habilita idempotencia.
   - `id` (string, opcional): Identificador único para rastrear solicitudes idempotentes.
   - `repo_name` (string): Nombre del repositorio.
   - `secrets` (map[string]string): Clave-valor con secretos.

2. **Lógica del handler:**
   - Si `idempotency` es `true`:
     - Verifica que el campo `id` esté presente y no haya sido procesado previamente.
     - Si la solicitud ya fue procesada, responde con un mensaje: `"Request already processed"`.
     - Usa funciones auxiliares para evitar recrear recursos existentes.
   - Si `idempotency` es `false`:
     - Ignora el campo `id` y recrea todos los recursos sin verificar si ya existen.

3. **Funciones auxiliares para manejar recursos:**
   - Usa las funciones auxiliares previamente definidas (`createRepoIfNotExists`, `createPipelineIfNotExists`, `createSecretIfNotExists`) para manejar repositorios, pipelines y secretos.

4. **Respuesta JSON esperada:**
   - Indica el estado de cada operación:
     - `"Repo created successfully"` o `"Repo already exists"`.
     - `"Pipeline created successfully"` o `"Pipeline already exists"`.
     - Para cada secreto: `"Secret created successfully"` o `"Secret already exists"`.

5. **Ejemplo de entrada:**
```json
{
    "idempotency": true,
    "id": "unique-id-123",
    "repo_name": "my-repo",
    "secrets": {
        "key1": "value1",
        "key2": "value2"
    }
}

ejemplo respuesta
{
    "repo": "Repo created successfully",
    "pipeline": "Pipeline already exists",
    "secrets": {
        "key1": "Secret created successfully",
        "key2": "Secret already exists"
    }
}
