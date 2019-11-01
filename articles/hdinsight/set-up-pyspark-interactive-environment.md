---
title: Środowisko interaktywne PySpark przy użyciu narzędzi usługi Azure HDInsight
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi usługi Azure HDInsight dla Visual Studio Code.
keywords: Programu vscode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241540"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w VS Code.

Używamy polecenia **Python/PIP** do kompilowania środowiska wirtualnego w ścieżce domowej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **Python/PIP** . Więcej szczegółów można znaleźć w temacie [Update-alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj język [Python](https://www.python.org/downloads/) i narzędzie [PIP](https://pip.pypa.io/en/stable/installing/).

   + Zainstaluj Język Python z programu [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Zainstaluj program PIP z [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (jeśli nie jest zainstalowany w instalacji języka Python).
   + Sprawdź, czy środowisko Python i PIP zostały pomyślnie zainstalowane, używając następujących poleceń. Obowiązkowe

        ![Sprawdzanie polecenia pip Version języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zalecane jest ręczne zainstalowanie języka Python zamiast używania domyślnej wersji programu macOS.

2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Inne pakiety

Jeśli zostanie wyświetlony komunikat o błędzie, Zainstaluj wymagane pakiety, uruchamiając następujące polecenia:

   ![Zainstaluj pakiet libkrb5 dla języka Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Uruchom ponownie VS Code, a następnie wróć do edytora skryptów, w którym działa usługa **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Następne kroki

### <a name="demo"></a>Pokaz
* HDInsight dla VS Code: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

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
