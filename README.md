# PEC3
análisis de variantes minoritarias utilizando un conjunto reducido de datos genómicos - PEC3 omicas
En este informe se presenta un análisis detallado de datos de secuenciación de ADN realizado mediante la plataforma Galaxy. El objetivo principal fue evaluar la calidad de las lecturas generadas, alinear estas secuencias al genoma humano de referencia hg19 y detectar variantes genéticas presentes en la muestra analizada. Se realizaron controles de calidad rigurosos utilizando FASTQC, seguido del alineamiento con BWA-MEM y análisis de variantes con FreeBayes y SnpEff para su anotación funcional. Los resultados obtenidos muestran una alta calidad técnica y biológica de las muestras, así como una cantidad significativa de variantes genéticas con potencial impacto en la función génica. Este trabajo demuestra la eficacia del flujo de trabajo utilizado para la obtención y análisis de datos genómicos fiables, sentando las bases para futuros estudios genéticos y funcionales.
**Preprocesado de los datos**
Para identificar qué muestra debo descargar, utilicé R Studio y ejecuté el siguiente código:
      myseed <- sum(utf8ToInt("dianagutierrez"))  # Por ejemplo, para otro usuario sería mariamartindiez
      set.seed(myseed) 
      sample_id <- sample(x = 0:9, size = 1)
      print(sample_id)  # Esto imprime un número, por ejemplo 9, que corresponde a la muestra a usar

Como resultado, obtuve el número 9, por lo que debo trabajar con los archivos sampleDat9_1.fq y sampleDat9_2.fq. Para cargar estos archivos en Galaxy, realicé lo siguiente: En la interfaz de Galaxy, fui al panel lateral izquierdo y seleccioné la opción "Get Data". Dentro de ese menú, elegí la función "Upload File from your computer". A continuación, seleccioné los archivos sampleDat9_1.fq y sampleDat9_2.fq desde mi ordenador y los subí al entorno de Galaxy.

Una vez cargados, los archivos estarán disponibles para ser usados en los análisis posteriores. Y a más a más los adjunto aquí en la carpeta de github para tenerlos guardados. 

**Evaluación y control de calidad de la lectura en Galaxy**
Se realizaron análisis de calidad con FASTQC para las muestras sampleDat9_1.fq y sampleDat9_2.fq en Galaxy. Los informes HTML mostraron que ambas muestras tienen una calidad de secuencia alta, con puntuaciones Phred promedio superiores a 30, lo que indica datos fiables para el análisis de variantes. El contenido GC de las lecturas presenta una distribución normal y coherente, con una ligera desviación hacia un mayor porcentaje de GC, sin indicios de contaminación o sesgos fuertes. Otros indicadores como el contenido de bases 'N', la longitud de lecturas, niveles de duplicación, secuencias sobre-representadas y contenido de adaptadores también se encontraron dentro de los rangos esperados, confirmando la buena calidad y diversidad de las secuencias. En conjunto, estos resultados aseguran que las muestras son adecuadas para continuar con los análisis posteriores.

**Alineamiento con el genoma de referencia**
Se realizó el alineamiento de las lecturas contra el genoma de referencia hg19 usando la herramienta BWA-MEM en Galaxy, generando un archivo BAM con las lecturas alineadas. Este archivo fue ordenado por coordenadas con SortSam para facilitar su análisis. Posteriormente, con Samtools idxstats se obtuvo la distribución de lecturas mapeadas por cromosoma, observándose alineamientos en todos los cromosomas principales (chr1 a chr22, X, Y y MT). La mayor concentración de lecturas se encontró en los cromosomas más grandes (chr1, chr2 y chr3), probablemente por su tamaño y nivel de expresión. Se detectaron muy pocas lecturas en regiones no asignadas o aleatorias, indicando una buena especificidad del mapeo. El resumen estadístico generado con Samtools flagstat mostró una alta calidad de alineamiento, con un 99.78% de lecturas mapeadas y un 98.95% de lecturas correctamente emparejadas (properly paired), lo que confirma la precisión y confiabilidad del mapeo realizado.

**Identificación de variantes**
Se generó un archivo pileup a partir del archivo BAM ordenado para evaluar variantes de secuencia, cobertura y detectar SNPs e indels. Este archivo fue filtrado dos veces con criterios de cobertura mínima (≥10) y calidad mínima (Phred ≥20), separando un archivo solo con variantes y otro con todas las posiciones. Luego, se utilizó la herramienta FreeBayes, un detector bayesiano de variantes genéticas, para identificar variantes y asignarles una puntuación de calidad, aplicándola sobre el archivo BAM ordenado. Los resultados mostraron un total de 29,813 variantes genéticas (SNPs, indels, etc.) en comparación con el genoma de referencia hg19. El archivo resultante contiene columnas clave como:

- Chrom: cromosoma de la variante.

- Pos: posición genómica.

- Ref y Alt: bases de referencia y variantes alternativas.

- Qual: calidad de la variante.

- Info: datos adicionales sobre la variante.

Estos datos permiten afirmar la presencia de múltiples variantes genéticas detectadas en la muestra.

**Visualización de los resultados intermedios mediante un Genome Browser** (las capturas se encuentran en el informe en formato pdf adjunto al campus) que se vincula con Github. 

Para visualizar el alineamiento, utilicé el archivo BAM ordenado y accedí a tres herramientas desde Galaxy: UCSC Genome Browser: permite comparar la alineación directamente en el navegador. bam.iobio: ofrece una visualización rápida de estadísticas clave como tasa de mapeo y profundidad de cobertura. Interactive Genomics Viewer (IGV): una aplicación de escritorio para revisión detallada y cómoda del alineamiento.

Resultados principales
En UCSC observé alta densidad de cobertura, por ejemplo en el cromosoma 7. Con bam.iobio, las estadísticas fueron: 98.4% de pares de lecturas correctamente alineados y emparejados. 0.7% de lecturas singletons (un solo fragmento alineado), un valor bajo y aceptable. 0% de lecturas duplicadas, indicando ausencia de artefactos por PCR. 49.2% de lecturas alineadas en la hebra directa, mostrando una distribución equilibrada entre hebras.

Estos resultados confirman una excelente calidad en el alineamiento, con alta precisión, poca duplicación y distribución balanceada, lo que permite avanzar con confianza a análisis posteriores.

**Filtrado y anotación de variantes.**
Se utilizó la herramienta SnpEff para anotar las variantes detectadas por FreeBayes, agregando información biológica relevante como localización genómica, impacto funcional potencial y presencia en bases de datos.

Resultados principales
Número total de variantes tras filtrado: 29,895

Tipos de variantes detectadas:

MNP (polimorfismos de nucleótidos múltiples): 662

Inserciones (INS): 639

Deleciones (DEL): 963

Variantes mixtas (MIXED): 90

Anotaciones funcionales
Missense (cambios de aminoácido): 15,785 variantes

Nonsense (codones de parada prematuros): 282 variantes

Silent (sin cambios en aminoácidos): 15,003 variantes

Clasificación según regiones genómicas
Variantes con posible efecto proteico:

Missense: 16,107

Nonsense: 282

Variantes en regiones reguladoras y no codificantes:

3’ UTR: 5,359

5’ UTR (incluyendo inicios prematuros): 2,165

Regiones intrónicas: 72,506

Exones de transcritos no codificantes: 18,274

Regiones cercanas a sitios de splicing: 4,895

Análisis de indels
Inserciones y deleciones son mayoritariamente pequeñas (hasta 14 nucleótidos).

Alta frecuencia alélica media, indicando variantes presentes en proporción elevada en la muestra.

Análisis de sustituciones en SNPs
Transiciones (Ti): 31,472 (cambios purina ↔ purina o pirimidina ↔ pirimidina)

Transversiones (Tv): 13,495 (cambios purina ↔ pirimidina)

Razón Ti/Tv: 2.33, dentro del rango esperado (2.0–3.0), lo que sugiere alta calidad y plausibilidad biológica de las variantes.

Matriz de codones
Se observaron cambios frecuentes como:

ACG → ACC (382 veces): cambio sinónimo (treonina a treonina).

ATG → ATA (271 veces): cambio no sinónimo (inicio de traducción metionina a isoleucina), potencialmente disruptivo.

Conclusiones y Discusiones en el archivo adjunto de informe. 

Bibliografia
-	Output summary files - SnpEff & SnpSift. (n.d.). Retrieved January 19, 2025, from https://pcingola.github.io/SnpEff/snpeff/outputsummary/
-	The Galaxy Community. Galaxy Project: Galaxy User Documentation. Disponible en: https://galaxyproject.org/tutorials/ [consultado el 12 de junio de 2025].
-	The Galaxy Community. Galaxy Training Network. Disponible en: https://training.galaxyproject.org

