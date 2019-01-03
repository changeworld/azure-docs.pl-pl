---
title: Narzędzia usługi Azure HDInsight — Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
keywords: VScode, narzędzia Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interakcyjnych zapytań
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: bf47915ba93a4a3a7dec338395cfe0ce6aa3cdf6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993845"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code

Poniższe kroki pokazują, jak zainstalować pakiety języka Python, uruchamiając **HDInsight: Interakcyjnego PySpark**.

## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Konfigurowanie środowiska interakcyjnego PySpark w systemach macOS i Linux
Jeśli używasz **python 3.x**, należy użyć polecenia **pip3** dla następujących kroków:

1. Upewnij się, że **Python** i **pip** są zainstalowane.
 
    ![Wersja narzędzia pip języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instalacja oprogramowania Jupyter.
    ```
    sudo pip install jupyter
    ```
   W systemie Linux i macOS, może zostać wyświetlony następujący komunikat o błędzie:

   ![Błąd 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Zainstaluj **libkrb5 dev** (dla tylko system Linux). Może zostać wyświetlony następujący komunikat o błędzie:

   ![Błąd 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Zainstaluj **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Upewnij się, że **ipywidgets** został poprawnie zainstalowany, wykonując następujące czynności:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Zainstaluj jądra otoki](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Zainstaluj jądra otoki. Uruchom **pip Pokaż sparkmagic**. Dane wyjściowe zawierają ścieżkę dla **sparkmagic** instalacji. 

    ![Lokalizacja sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Przejdź do lokalizacji, a następnie uruchom:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![Zainstaluj kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Sprawdź stan instalacji.

    ```
    jupyter-kernelspec list
    ```
    ![Lista kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Aby uzyskać dostępne jądra: 
    - **python2** i **pysparkkernel** odpowiadają **python 2.x**. 
    - **środowiska python3 jako** i **pyspark3kernel** odpowiadają **python 3.x**. 

8. Uruchom ponownie program VS Code, a następnie wrócić do edytora skryptów, w którym działa **HDInsight: Interakcyjnego PySpark**.

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla programu VS Code: [Film wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md)
* [Tworzenie i przesyłanie aplikacji Apache Spark Scala przy użyciu Azure Toolkit for IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Debugowanie aplikacji platformy Apache Spark, zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie narzędzia HDInsight w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Korzystanie z notesów Apache Zeppelin na HDInsight klastra Apache Spark](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Apache Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache ](hdinsight-connect-hive-zeppelin.md)
