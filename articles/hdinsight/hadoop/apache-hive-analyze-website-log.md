---
title: Korzystanie z programu Hive z usługą Hadoop do analizy dziennika witryny sieci Web - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystanie z programu Hive z usługą HDInsight do analizy dzienników witryn sieci Web. Będzie użyć pliku dziennika jako dane wejściowe do tabeli HDInsight, a następnie użyć HiveQL do wykonywania zapytań o dane.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 53a0560d3bc5a52069d5829b9c3bd353e0c37ef3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Korzystanie z programu Hive z HDInsight opartych na systemie Windows do analizowania dzienników witryn sieci Web
Dowiedz się, jak używać HiveQL z usługą HDInsight do analizy dzienników z witryny sieci Web. Analiza dziennika witryny sieci Web może służyć do segmentowania odbiorców w oparciu podobne działania, kategoryzowanie odwiedzający przez demograficznych i sprawdzić zawartość one widoku, witryn sieci Web, które pochodzą z i tak dalej.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight opartych na systemie Windows. HDInsight jest dostępna tylko w systemie Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

W tym przykładzie używasz klastra usługi HDInsight do analizy pliki dziennika witryny sieci Web, aby uzyskać wgląd w częstotliwość odwiedzanych witryny sieci Web z zewnętrznymi witrynami sieci Web w ciągu jednego dnia. Można również generować podsumowanie błędów witryny sieci Web, których użytkowników. Omawiane kwestie:

* Nawiązać połączenia z magazynem obiektów Blob Azure, który zawiera pliki dziennika witryny sieci Web.
* Tworzenie tabel programu HIVE do badania tych dzienników.
* Tworzenie zapytań HIVE do analizowania danych.
* Nawiązywanie połączenia z HDInsight (używając do pobierania analizowanych danych ODBC (ODBC) za pomocą programu Microsoft Excel.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Wymagania wstępne
* Musi mieć zainicjowana klastra usługi Hadoop w usłudze Azure HDInsight. Aby uzyskać instrukcje, zobacz [klastrów usługi HDInsight udostępniania](../hdinsight-hadoop-provision-linux-clusters.md).
* Musi mieć programu Microsoft Excel 2013 lub zainstalowany program Excel 2010.
* Musi mieć [sterownik Microsoft Hive ODBC](http://www.microsoft.com/download/details.aspx?id=40886) do importowania danych z gałęzi do programu Excel.

## <a name="to-run-the-sample"></a>Aby uruchomić przykładowy
1. Z [portalu Azure](https://portal.azure.com/), z tablicy startowej (jeśli został przypięty klaster istnieje), kliknij Kafelek klastra, na którym chcesz uruchomić przykład.
2. W bloku klastra w obszarze **szybkie linki**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie z **pulpit nawigacyjny klastra** bloku, kliknij przycisk **pulpit nawigacyjny klastra usługi HDInsight**. Pulpit nawigacyjny można również otworzyć bezpośrednio przy użyciu następującego adresu URL:

         https://<clustername>.azurehdinsight.net

    Po wyświetleniu monitu uwierzytelniania przy użyciu nazwy użytkownika administratora i hasło, które zostały użyte podczas inicjowania obsługi klastra.
3. Na stronie sieci web, który zostanie otwarty, kliknij **Getting Started galerii** kartę, a następnie w obszarze **rozwiązań z przykładowymi danymi** kategorii, kliknij przycisk **analiza dziennika witryny sieci Web** próbki.
4. Postępuj zgodnie z instrukcjami na stronie sieci web zakończenie próbki.

## <a name="next-steps"></a>Kolejne kroki
Poniższy przykład spróbuj: [analizowanie danych czujnika przy użyciu Hive z usługą HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
