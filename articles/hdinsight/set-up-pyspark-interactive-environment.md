---
title: Narzędzia usługi Azure HDInsight — Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
keywords: VScode, narzędzia Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interakcyjnych zapytań
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: cd0eb03c80a2a379cc84144b0e86000e40753774
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066497"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code

Poniższe kroki pokazują, w jaki sposób skonfigurować konfigurację środowiska interakcyjnego PySpark w programie VS Code.

Używamy **python/pip** polecenie, aby skompilować środowiska wirtualnego w ścieżce głównej. Jeśli chcesz użyć innej wersji, musisz zmienić domyślną wersją **python/pip** polecenia ręcznie. Więcej informacji, zobacz [alternatywy aktualizacji](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj [Python](https://www.python.org/downloads/) i [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Zainstaluj język Python z [ https://www.python.org/downloads/ ](https://www.python.org/downloads/).
   + Instalovat modul pip z [ https://pip.pypa.io/en/stable/installing ](https://pip.pypa.io/en/stable/installing/). (Jeśli nie została zainstalowana ze instalację języka Python)
   + Sprawdź poprawność języka Python i narzędzie pip są instalowane pomyślnie za pomocą następujących poleceń. (Opcjonalnie)
 
        ![Wersja narzędzia pip języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zaleca się ręczne instalowanie języka Python, zamiast domyślnej wersji systemu MacOS.


2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.
   
   ```
   pip install virtualenv
   ```

3. W przypadku systemu Linux tylko zainstalować wymagane pakiety, uruchamiając poniższych poleceń, jeśli wystąpi komunikat o błędzie.
   
    ![Wersja narzędzia pip języka Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Uruchom ponownie program VS Code, a następnie wrócić do edytora skryptów, w którym działa **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla programu VS Code: [Film wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md)
* [Tworzenie i przesyłanie aplikacji Apache Spark Scala przy użyciu Azure Toolkit for IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Apache Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Uruchamianie zapytań usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Apache Zeppelin](./interactive-query/hdinsight-connect-hive-zeppelin.md)
