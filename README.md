name: Actualizar datos de Mega Liga Pro

on:
  workflow_dispatch:
  schedule:
    - cron: "27 8 * * *"

permissions:
  contents: write

jobs:
  update-data:
    runs-on: ubuntu-latest

    steps:
      - name: Descargar repositorio
        uses: actions/checkout@v4

      - name: Instalar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Ejecutar actualización de datos
        run: node update-data.mjs

      - name: Verificar cambios
        id: verify-changed-files
        run: |
          if git diff --quiet; then
            echo "changed=false" >> "$GITHUB_OUTPUT"
          else
            echo "changed=true" >> "$GITHUB_OUTPUT"
          fi

      - name: Guardar cambios actualizados
        if: steps.verify-changed-files.outputs.changed == 'true'
        run: |
          git config user.name "mega-liga-bot"
          git config user.email "mega-liga-bot@users.noreply.github.com"
          git add data/snapshot-data.js
          git commit -m "Actualizar datos automáticos"
          git push# .github-workflows-update-data.yml
