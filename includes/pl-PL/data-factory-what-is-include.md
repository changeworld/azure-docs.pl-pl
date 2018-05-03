
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory to usługa integracji danych. Umożliwia ona tworzenie przepływów pracy obsługujących dane w chmurze. Przepływ pracy jest implementowany w postaci co najmniej jednego *potoku*. Potoki służą do organizowania i automatyzowania przenoszenia oraz przekształcania danych. Potoki mogą wykonywać następującą sekwencję w odniesieniu do danych:

1. Pozyskiwanie danych z różnych magazynów danych.
2. Przekształcanie lub przetwarzanie danych przy użyciu usług obliczeniowych, takich jak:
    - Azure HDInsight Hadoop
    - platforma Spark
    - Azure Data Lake Analytics
    - Azure Machine Learning
3. Publikowanie danych wyjściowych w magazynach danych.
    - Miejscem docelowym publikowania może być usługa Azure SQL Data Warehouse — dane będą używane przez aplikacje analizy biznesowej. 

Usługa Data Factory umożliwia planowanie potoków.

