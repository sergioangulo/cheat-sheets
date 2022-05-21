# Probemas tícos con data
- insuficientes (muy pocos)
- no representativos (sesgo: muestral, no respuesta, etc)
- mala calidad (ruidoso)
- características irrelevantes

## Cuando limpiar datos de entrenamiento
1. Si instancias son valores atípicos, podriamos:
    - eliminar
    - arreglar a mano
2. Si hay muchos valores nulos:
    - ignorar el atributo
    - Ignorar instancias
    - Rellenar valores (media, valores a izq., valores a derecha, etc)
    - Entrenar 2 modelos, uno con la característica y otro sin ella.

# Problemas típicos con algoritmos:
- sobreajuste (generalizar en exceso con datos conocidos): modelo demasiado complejo para la cantiddad y el ruido de lods datos de entrenamiento
- Como soluciono sobreajuste:
    - simplifique modelo : ej. regularizacion
    - reuna mas data de entrenamiento
    - reduzca el ruido:
        - solucione errores
        - eliminar atípicos     


  
