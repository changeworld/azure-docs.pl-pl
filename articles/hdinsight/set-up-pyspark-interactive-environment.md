---
title: Usługa Azure HDInsight Tools — Konfigurowanie środowiska interakcyjne PySpark dla kodu programu Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą narzędzi HDInsight Azure dla programu Visual Studio Code tworzenie i przesyłanie zapytań i skryptów.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407235"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurowanie środowiska interakcyjne PySpark dla programu Visual Studio Code

Poniższe kroki opisano sposób instalowania pakietów języka Python, uruchamiając **HDInsight: PySpark interakcyjne**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Konfigurowanie środowiska interakcyjne PySpark na macOS i Linux
Jeśli używasz **python 3.x**, należy użyć polecenia **pip3** dla następujących kroków:

1. Upewnij się, że **Python** i **pip** są zainstalowane.
 
    ![wersja narzędzia pip Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instalacja oprogramowania Jupyter.
    ```
    sudo pip install jupyter
    ```
   W systemie Linux i macOS może pojawić następujący komunikat o błędzie:

   ![Błąd 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Zainstaluj **libkrb5 deweloperów** (dla systemu Linux tylko). Może pojawić następujący komunikat o błędzie:

   ![Błąd 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Zainstaluj **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Upewnij się, że **ipywidgets** jest poprawnie zainstalowany, wykonując następujące czynności:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Zainstaluj jądra otoki](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Zainstaluj jądra otoki. Uruchom **pip Pokaż sparkmagic**. Dane wyjściowe zawierają ścieżki dla **sparkmagic** instalacji. 

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

    Dla jądra dostępne: 
    - **python2** i **pysparkkernel** odpowiadają **python 2.x**. 
    - **python3** i **pyspark3kernel** odpowiadają **python 3.x**. 

8. Uruchom ponownie kodzie VS, a następnie wróć do edytora skryptów, który działa **HDInsight: PySpark interakcyjne**.

## <a name="next-steps"></a>Kolejne kroki

### <a name="demo"></a>Demonstracja
* HDInsight dla wersji programu VS kodu: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Narzędzie Azure HDInsight dla kodu programu Visual Studio](hdinsight-for-vscode.md)
* [Zestaw narzędzi platformy Azure dla IntelliJ umożliwia tworzenie i przesyłanie aplikacji Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem usługi SSH przy użyciu zestawu narzędzi Azure for IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi Azure for IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Użyj narzędzia HDInsight Tools w zestawie narzędzi Azure dla programu Eclipse tworzenie aplikacji Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Użyj narzędzia HDInsight Tools for IntelliJ z Hortonworks piaskownicy](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin ](hdinsight-connect-hive-zeppelin.md)
