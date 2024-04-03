# SNYK-NODE-EXPRESS

## Proyecto de Seguridad con Snyk y Lodash:
- Este proyecto muestra cómo utilizar Snyk para identificar y corregir vulnerabilidades en una aplicación Node.js que utiliza la biblioteca Lodash versión 4.17.19. 
- Snyk es una herramienta de seguridad que ayuda a encontrar y solucionar vulnerabilidades en bibliotecas de código abierto.

### Requisitos:
- Node.js instalado en tu sistema.
- Un proyecto Node.js existente que utilice Lodash versión 4.17.19.
- Acceso a la línea de comandos.
- iniciar sesión en snyk.
- Account settings. 
- Generar token.
- Crear un secret sobre el repositorio y guardar la KEY de snyk.

### Instalaciones:

```
npm init -y
npm install express
npm install lodash@4.17.19
```

### Procedimiento:
- Luego de crear el secret y encontrarse logeuado en snyk crear el pipeline correspondiente:

```
name: Snyk

on:
   push:
    branches:
      - main

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v4

      - name: Run Snyk to check for vulnerabilities
        id: snyk
        continue-on-error: true
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --all-projects --severity-threshold=high --json > snyk_report.json

      - name: Archive vulnerability report
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: snyk_report
          path: snyk_report.json
          
      - name: Fail on vulnerability
        if: steps.snyk.outcome == 'failure'
        run: exit 1
```
- Desde snyk corregir las vulnerabilidades y hacer el pull request para que se agreguen al pipeline la actualización de los paquetes vulnerables detectados por snyk.

