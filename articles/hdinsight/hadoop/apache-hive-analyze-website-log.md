---
title: Korzystanie z programu Hive z usługą Hadoop do analizy dzienników witryny sieci Web — Azure HDInsight
description: Dowiedz się, jak wykorzystać technologię Hive z HDInsight do analizowania dzienników witryn sieci Web. Będzie używany plik dziennika jako dane wejściowe do tabeli usługi HDInsight, a za pomocą języka HiveQL do wykonywania zapytań o dane.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: c0a15bacd3aaf97a3caa54ee8bf70a9c4cf54663
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233602"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Używanie technologii Hive z HDInsight z systemem Windows do analizy dzienników z witryn sieci Web
Dowiedz się, jak za pomocą języka HiveQL z HDInsight do analizy dzienników w witrynie sieci Web. Analiza dziennika witryny sieci Web może służyć do segmentuj odbiorców w oparciu o podobnych działań, osoby odwiedzające witrynę na kategorie według dane demograficzne oraz dowiedzieć się zawartość one widoku, pochodzą z witryn sieci Web i tak dalej.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie działają tylko z klastrami HDInsight z systemem Windows. HDInsight jest dostępna tylko na Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

W tym przykładzie za pomocą klastra usługi HDInsight można analizować pliki dziennika witryny sieci Web można pobrać danych dotyczących częstotliwości odwiedzin witryny sieci Web z zewnętrznych witryn sieci Web w ciągu dnia. Możesz również wygenerować podsumowania błędów witryn sieci Web, które użytkowników. Omawiane kwestie:

* Połączyć z usługą Azure Blob storage, który zawiera pliki dziennika witryny sieci Web.
* Tworzenie tabel programu HIVE do wykonywania zapytań tych dzienników.
* Tworzenie zapytań programu HIVE do analizowania danych.
* Nawiązywanie połączenia z HDInsight (przy użyciu open database connectivity (ODBC), aby pobrać analizowanych danych za pomocą programu Microsoft Excel.

![USŁUGI HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Wymagania wstępne
* Należy aprowizowany klaster Hadoop w usłudze Azure HDInsight. Aby uzyskać instrukcje, zobacz [Inicjowanie obsługi klastrów HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Konieczne jest posiadanie programu Microsoft Excel 2013 lub programu Excel 2010 jest zainstalowany.
* Konieczne jest posiadanie [sterownika Microsoft Hive ODBC](https://www.microsoft.com/download/details.aspx?id=40886) do importowania danych z programu Hive w programie Excel.

## <a name="to-run-the-sample"></a>Aby uruchomić przykład
1. Z [witryny Azure portal](https://portal.azure.com/), na tablicy startowej (jeśli został przypięty istnieje klastra), kliknij Kafelek klastra, na którym chcesz uruchomić próbki.
2. W bloku klastra w obszarze **szybkich łączy**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie z **pulpit nawigacyjny klastra** bloku kliknij **klastra HDInsight Pulpit nawigacyjny**. Możesz też bezpośrednio Otwórz pulpit nawigacyjny, korzystając z następującego adresu URL:

         https://<clustername>.azurehdinsight.net

    Po wyświetleniu monitu uwierzytelniania przy użyciu nazwy użytkownika administratora i hasło, którego użyto podczas aprowizowania klastra.
3. Ze strony internetowej, która zostanie otwarta, kliknij przycisk **Getting Started galerii** kartę, a następnie w obszarze **rozwiązania z przykładowymi danymi** kategorię, kliknij przycisk **analiza dziennika witryny sieci Web** próbki.
4. Postępuj zgodnie z instrukcjami na stronie sieci web zakończenie próbki.

## <a name="next-steps"></a>Kolejne kroki
Wypróbuj poniższy przykład: [analizowanie danych czujnika przy użyciu technologii Hive z HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
