---
title: Narzędzia usługi Azure HDInsight — środowisko interaktywne PySpark dla Visual Studio Code
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi usługi Azure HDInsight dla Visual Studio Code.
keywords: Programu vscode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 54adb2ec61d77ea429f7da1b88aa9347c1addfe0
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736412"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w VS Code.

Używamy polecenia **Python/PIP** do kompilowania środowiska wirtualnego w ścieżce domowej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **Python/PIP** . Więcej szczegółów można znaleźć w temacie [Update-alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj język [Python](https://www.python.org/downloads/) i narzędzie [PIP](https://pip.pypa.io/en/stable/installing/).
   
   + Zainstaluj Język Python [https://www.python.org/downloads/](https://www.python.org/downloads/)z programu.
   + Zainstaluj program PIP [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/)z programu. (Jeśli nie jest zainstalowany w instalacji języka Python)
   + Weryfikowanie, czy język Python i PIP zostały pomyślnie zainstalowane przy użyciu następujących poleceń. Obowiązkowe
 
        ![Wersja PIP języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zalecane jest ręczne zainstalowanie języka Python zamiast używania domyślnej wersji programu MacOS.


2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.
   
   ```
   pip install virtualenv
   ```

3. Tylko w przypadku systemu Linux Zainstaluj wymagane pakiety, uruchamiając polecenia w postaci mieszkowej w przypadku wystąpienia komunikatu o błędzie.
   
    ![Wersja PIP języka Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Uruchom ponownie vs Code, a następnie wróć do edytora skryptów, w którym działa **Usługa HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Następne kroki

### <a name="demo"></a>Pokaz
* HDInsight dla VS Code: [Film wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Użyj narzędzia Azure HDInsight dla Visual Studio Code](hdinsight-for-vscode.md)
* [Używanie Azure Toolkit for IntelliJ do tworzenia i przesyłania Apache Spark aplikacji Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem protokołu SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Tworzenie aplikacji Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Apache Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Uruchamianie zapytań usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Apache Zeppelin](./interactive-query/hdinsight-connect-hive-zeppelin.md)
