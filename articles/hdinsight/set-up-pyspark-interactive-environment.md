---
title: Narzędzia usługi Azure HDInsight — Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code
description: Dowiedz się, jak używać narzędzi HDInsight systemu Azure dla programu Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: aeac28d77270cae120bf3b9f05d9825b01f16bb4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600510"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurowanie środowiska interakcyjnego PySpark dla programu Visual Studio Code

Poniższe kroki pokazują, jak zainstalować pakiety języka Python, uruchamiając **HDInsight: interakcyjnego PySpark**.


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

8. Uruchom ponownie program VS Code, a następnie wrócić do edytora skryptów, w którym działa **HDInsight: interakcyjnego PySpark**.

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla programu VS Code: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](hdinsight-for-vscode.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem protokołu SSH przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Narzędzia HDInsight znajdujące się w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Uruchamianie zapytań programu Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin ](hdinsight-connect-hive-zeppelin.md)
