# Clasificador de perros y gatos

Demo de TensorFlow.js que clasifica la imagen de una cámara web como gato o perro. Los artefactos del modelo (`model.json` y los dos archivos `group1-shard*.bin`) se recuperaron del proyecto upstream [`ringa-tech/clasificador-perros-gatos`](https://github.com/ringa-tech/clasificador-perros-gatos) después de verificar por SHA-256 que los shards locales son idénticos.

## Uso local en Windows

1. Abra PowerShell en esta carpeta.
2. Inicie un servidor local: `.\.venv\Scripts\python.exe -m http.server 8000`
3. Abra [http://localhost:8000](http://localhost:8000) en un navegador moderno.
4. Cuando el navegador lo solicite, permita el acceso a la cámara. La cámara requiere `localhost` (o HTTPS); no abra `index.html` directamente con `file://`.

La página muestra el estado de carga del modelo y los errores de cámara. Use **Cambiar camara** para alternar entre cámaras disponibles. Detenga el servidor con `Ctrl+C`.

## Entrenamiento y exportación opcionales

No hace falta entrenar para usar el demo: los artefactos recuperados y verificados ya están incluidos. Para reproducir o reemplazar el modelo, use `Clasificador_de_perros_y_gatos.ipynb` con el entorno y dependencias de `requirements.txt`; esa ruta descarga Cats vs Dogs, entrena el modelo y debe exportar un modelo TensorFlow.js compatible (`model.json` y sus shards) antes de servir la página.
