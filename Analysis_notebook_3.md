# Análisis temporal de la vulgaridad en letras españolas

## Hipótesis retomada
Las letras recientes contienen una carga vulgar mayor que las décadas previas. Usamos el tópico 5 del modelo LDA como proxy cuantitativo de vocabulario vulgar.

## Técnicas aplicadas
- **Exploración temporal**: estadísticas descriptivas por año y media móvil a 5 años (fig. abajo).
- **Modelos de series temporales**:
  - SARIMAX (1,1,1)(0,1,1,5) con partición train/test en 2012; las métricas RMSE/MAE se muestran en la celda y en la figura.
  - Holt-Winters (ETS con tendencia amortiguada) como alternativa de suavizado.
  - Pronóstico SARIMAX con toda la serie para 8 años futuros.
- **Comparación por períodos**: agregados por década para detectar saltos estructurales (`results/topic5_decadas.png`).
- **Análisis de volatilidad**: retornos, retornos al cuadrado y PACF para justificar el uso de GARCH (`results/topic5_retornos.png`, `results/topic5_retornos_cuadrado.png`, `results/topic5_pacf_retornos.png`).
- **Modelado GARCH**: ajuste automático con `arch.arch_model` para medir clústeres de varianza (salidas en notebook, celda de la sección "Análisis de volatilidad").

### Figuras clave
![Topic 5 anual](results/topic5_evolucion.png)
![Train/Test SARIMAX](results/sarimax_train_test.png)
![SARIMAX vs Holt-Winters](results/comparacion_sarimax_holtwinters.png)
![Pronóstico futuro SARIMAX](results/sarimax_forecast_full_series.png)
![Décadas topic 5](results/topic5_decadas.png)
![Retornos topic 5](results/topic5_retornos.png)
![Retornos² topic 5](results/topic5_retornos_cuadrado.png)
![PACF retornos](results/topic5_pacf_retornos.png)

## Resultados y visualizaciones clave
1. **Tendencia creciente**: la media anual del tópico 5 crece de forma sostenida desde los años 90, con picos pronunciados después de 2015 (`topic5_evolucion.png`).
2. **Modelos SARIMAX vs Holt-Winters**: ambos capturan la tendencia, pero SARIMAX obtiene menores errores (RMSE ≈ 0.11 frente a valores mayores en Holt-Winters) y reproduce mejor el cambio de pendiente en el conjunto de prueba (`sarimax_train_test.png`, `comparacion_sarimax_holtwinters.png`). Sin embargo, estos resultados no son muy relevantes ya que el topico vulgar se dispara a partir de la década de los 2000 y resulta casi imposible predecir un aumento de estas características tan solo con los datos anteriores.
3. **Pronósticos futuros**: el modelo completo proyecta probabilidades elevadas que se estabilizan por encima de los niveles de la década de 2000, con intervalos de confianza amplios pero siempre positivos (`sarimax_forecast_full_series.png`).
4. **Decadas**: la fracción de canciones con topic_5 > 0.15 se duplica entre los 90 y los 2010, confirmando un cambio estructural (`topic5_decadas.png`).
5. **Volatilidad**: los retornos muestran años muy tranquilos alternados con "ráfagas" de cambios fuertes; el GARCH(1,1) detecta varianza condicional persistente, lo que respalda describir episodios de choques y relajación.

## Discusión de la hipótesis
Los resultados apoyan la hipótesis. Todos los indicadores (series anuales, decadales y pronósticos) muestran un nivel de vulgaridad significativamente mayor en las décadas recientes. Aunque existen periodos de volatilidad, incluso los mínimos recientes superan a los máximos históricos anteriores a 2000.

## Limitaciones y mejoras
- **Datos**: solo ~80 observaciones anuales; limita la robustez de modelos de volatilidad y de estacionalidad compleja.
- **Indicador único**: el análisis se centra en un único tópico; combinar múltiples tópicos o medidas de sentimiento podría ofrecer una visión más rica.
- **Modelado**: no se incluyen variables externas en esta versión; la notebook "modelos_3" explora variantes con métricas demográficas que podrían incorporarse aquí.
- **Resultados reproducibles**: tras ejecutar nuevamente las celdas con guardado automático, es necesario verificar que las figuras se regeneran en `results/`.

