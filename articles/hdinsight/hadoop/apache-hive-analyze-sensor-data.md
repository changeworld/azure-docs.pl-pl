---
title: Analizowanie danych czujnika przy użyciu Hive i Hadoop - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i analizowanie danych czujnika przy użyciu konsoli zapytania programu Hive z usługą HDInsight (Hadoop), a następnie wizualizacji danych w programie Microsoft Excel z PowerView.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb3dc93d7cb741a8a3099abe13d00f40c9639705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398967"
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analizowanie danych czujnika przy użyciu konsoli zapytania Hive na platformie Hadoop w usłudze HDInsight

Dowiedz się, jak i analizowanie danych czujnika przy użyciu konsoli zapytania programu Hive z usługą HDInsight (Hadoop), a następnie wizualizacji danych w programie Microsoft Excel za pomocą Power View.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight opartych na systemie Windows. HDInsight jest dostępna tylko w systemie Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).


W tym przykładzie służy do przetwarzania danych historycznych i zidentyfikować problemy z systemami grzejników i klimatyzacja Hive. W szczególności zidentyfikować systemy nie będą mogli w sposób niezawodny utrzymać stałej temperatury przy wykonywaniu następujących zadań:

* Tworzenie tabel programu HIVE wykonać zapytania o dane przechowywane w plikach plik wartości rozdzielanych przecinkami (CSV).
* Tworzenie zapytań HIVE do analizowania danych.
* Aby pobrać analizowanych danych, należy użyć programu Microsoft Excel do nawiązania połączenia usługi HDInsight.
* W celu wizualizacji danych, użyj Power View.

![Diagram architektury rozwiązania](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi HDInsight (Hadoop): zobacz [klastrów utworzyć Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) informacji o tworzeniu klastra.
* Programu Microsoft Excel 2013

  > [!NOTE]
  > Program Microsoft Excel jest używana do wizualizacji danych z [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Sterownik ODBC firmy Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Aby uruchomić przykładowy

1. W przeglądarce sieci web przejdź do następującego adresu URL: 

         https://<clustername>.azurehdinsight.net

    Element `<clustername>` należy zastąpić nazwą klastra usługi HDInsight.

    Po wyświetleniu monitu uwierzytelniania przy użyciu nazwy użytkownika administratora i hasło użyte podczas inicjowania obsługi administracyjnej tego klastra.

2. Na stronie sieci web, który zostanie otwarty, kliknij **Getting Started galerii** kartę, a następnie w obszarze **rozwiązań z przykładowymi danymi** kategorii, kliknij przycisk **analizy danych czujnika** próbki.

    ![Pobieranie rozpoczęte galerii](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Postępuj zgodnie z instrukcjami na stronie sieci web zakończenie próbki.
