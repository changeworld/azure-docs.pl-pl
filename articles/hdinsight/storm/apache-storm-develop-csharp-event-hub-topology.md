---
title: Przetwarzać zdarzeń z usługi Event Hubs dzięki platformie Storm - Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie przetwarzania danych z usługi Azure Event Hubs w topologii Storm C#, utworzony w programie Visual Studio za pomocą narzędzi HDInsight tools for Visual Studio.
services: hdinsight,notification hubs
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: ''
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 475a63937ed09b4ce875465c0eb3488ce92c38be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Zdarzenia procesu z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight (C#)

Dowiedz się, jak pracować z usługą Azure Event Hubs z systemu Apache Storm w usłudze HDInsight. Ten dokument używa topologii Storm C# do odczytywania i zapisywania danych z usługi Event Hubs

> [!NOTE]
> Wersja języka Java tego projektu, dla [przetwarzać zdarzeń z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Kroki opisane w tym dokumencie Użyj SCP.NET, pakietu NuGet, który ułatwia tworzenie topologii C# i składniki do użycia z systemu Storm w usłudze HDInsight.

> [!IMPORTANT]
> Podczas czynności opisane w tym dokumencie zależą od środowiska projektowego systemu Windows w programie Visual Studio, skompilowany projekt może zostać przesłane do systemu Storm w klastrze usługi HDInsight, który używa systemu Linux. Topologie SCP.NET obsługują tylko opartych na systemie Linux klastrów utworzonych po 28 października 2016 roku.

HDInsight 3.4 i większe wykorzystanie Mono uruchamiania topologie języka C#. Przykład używane w tym dokumencie współpracuje z 3,6 HDInsight. Jeśli planujesz tworzenie własnych rozwiązań .NET dla usługi HDInsight, sprawdź [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/) dokument dla potencjalnych niezgodności.

### <a name="cluster-versioning"></a>Przechowywanie wersji klastra

Pakiet Microsoft.SCP.Net.SDK NuGet używanych w przypadku projektu musi być zgodna wersja główna systemu STORM zainstalowany w usłudze HDInsight. HDInsight w wersji 3.5 i 3,6 używać systemu Storm 1.x, dlatego należy użyć 1.0.x.x wersji SCP.NET z tych klastrach.

> [!IMPORTANT]
> Przykład, w tym dokumencie oczekuje HDInsight w wersji 3.5 lub 3,6 klastra.
>
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Topologie języka C# muszą również wskazywać .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracować z usługą Event Hubs

Firma Microsoft udostępnia zestaw składników Java, które mogą służyć do komunikacji z usługą Event Hubs od topologii Storm. Można znaleźć pliku archiwum (JAR) Java, który zawiera 3,6 HDInsight zgodna wersja tych składników na [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Gdy składniki są napisane w języku Java, łatwo można je od topologii C#.

Następujące składniki są używane w tym przykładzie:

* __EventHubSpout__: odczytuje dane z usługi Event Hubs.
* __EventHubBolt__: zapisuje dane do usługi Event Hubs.
* __EventHubSpoutConfig__: umożliwia skonfigurowanie EventHubSpout.
* __EventHubBoltConfig__: umożliwia skonfigurowanie EventHubBolt.

### <a name="example-spout-usage"></a>Przykład użycia spout

SCP.NET udostępnia metody do dodawania EventHubSpout do topologii. Tych metod można ułatwić dodawanie spout niż przy użyciu metody ogólne do dodawania składnika Java. W poniższym przykładzie pokazano, jak utworzyć elementy spout przy użyciu __SetEventHubSpout__ i **EventHubSpoutConfig** metody udostępniane przez SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Poprzedni przykład tworzy nowy składnik spout o nazwie __EventHubSpout__i skonfiguruje je do komunikowania się z Centrum zdarzeń. Wskazówka równoległości dla składnika ma ustawioną wartość liczby partycji w zdarzeniu koncentratora. To ustawienie umożliwia Storm do utworzenia wystąpienia składnika dla każdej partycji.

### <a name="example-bolt-usage"></a>Przykład użycia bolt

Użyj **JavaComponmentConstructor** metodę w celu utworzenia wystąpienia elementy bolt. W poniższym przykładzie pokazano, jak utworzyć i skonfigurować nowe wystąpienie klasy **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> W tym przykładzie użyto wyrażenia Clojure przekazany jako ciąg, zamiast **JavaComponentConstructor** utworzyć **EventHubBoltConfig**, tak jak w przykładzie elementy spout. Każda metoda działa. Użyj metody tak najważniejsze dla Ciebie.

## <a name="download-the-completed-project"></a>Pobieranie ukończone projektu

Możesz pobrać pełną wersję utworzonego w ramach tego samouczka z projektu [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Jednakże nadal musisz podać ustawienia konfiguracji, wykonując kroki opisane w tym samouczku.

### <a name="prerequisites"></a>Wymagania wstępne

* [Systemu Apache Storm w klastrze usługi HDInsight w wersji 3.5 lub 3,6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > Przykład używane w tym dokumencie wymaga Storm w usłudze HDInsight w wersji 3.5 lub 3,6. To nie zadziała ze starszymi wersjami usługi hdinsight, ze względu na istotne zmiany nazwy klasy. Wersja tego przykładu, która współdziała z klastrami starsze, dla [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Centrum zdarzeń Azure](../../event-hubs/event-hubs-create.md).

* [Zestawu Azure .NET SDK](http://azure.microsoft.com/downloads/).

* [Narzędzia HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 lub nowszego środowiska deweloperskiego. Pobieranie JDK są dostępne z [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * **JAVA_HOME** zmiennej środowiskowej musi wskazywać katalog, który zawiera Java.
  * **%JAVA_HOME%/bin** katalog musi znajdować się w ścieżce.

## <a name="download-the-event-hubs-components"></a>Pobierz składniki usługi Event Hubs

Pobierz elementy spout centra zdarzeń i elementów bolt składnika z [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Utwórz katalog o nazwie `eventhubspout`i Zapisz plik do katalogu.

## <a name="configure-event-hubs"></a>Konfigurowanie usługi Event Hubs

Centra zdarzeń to źródło danych, w tym przykładzie. Skorzystaj z informacji w sekcji "Tworzenie Centrum zdarzeń" [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po utworzeniu Centrum zdarzeń wyświetlić **EventHub** ustawień na platformie Azure, portalu i wybierz pozycję **zasady dostępu współużytkowanego**. Wybierz **+ Dodaj** można dodać następujące zasady:

   | Name (Nazwa) | Uprawnienia |
   | --- | --- |
   | autor |Send |
   | czytelnik |Nasłuchuj |

    ![Zrzut ekranu udziału dostępu zasady okna](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Wybierz **czytnika** i **zapisywania** zasad. Skopiuj i Zapisz wartość klucza podstawowego dla obu zasad, jak te wartości są używane później.

## <a name="configure-the-eventhubwriter"></a>Skonfiguruj EventHubWriter

1. Jeśli nie został jeszcze zainstalowany najnowszej wersji narzędzia HDInsight tools for Visual Studio, zobacz [rozpocząć korzystanie z narzędzia HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Pobierz rozwiązania z [eventhub storm hybrydowy](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. W **EventHubWriter** otwarciu projektu **App.config** pliku. Użyj informacji z Centrum zdarzeń, który został wcześniej skonfigurowany, aby wypełnić wartości następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |Moduł zapisujący (jeśli użyto inną nazwę dla zasad o *wysyłania* uprawnień, zamiast tego użyj.) |
   | EventHubPolicyKey |Klucz dla modułu zapisującego zasad. |
   | EventHubNamespace |Przestrzeń nazw zawiera Centrum zdarzeń. |
   | EventHubName |Nazwę Centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w Centrum zdarzeń. |

4. Zapisz i Zamknij **App.config** pliku.

## <a name="configure-the-eventhubreader"></a>Skonfiguruj EventHubReader

1. Otwórz **EventHubReader** projektu.

2. Otwórz **App.config** plik **EventHubReader**. Użyj informacji z Centrum zdarzeń, który został wcześniej skonfigurowany, aby wypełnić wartości następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |Czytnik (Jeśli używasz inną nazwę dla zasad o *nasłuchiwania* uprawnień, zamiast tego użyj.) |
   | EventHubPolicyKey |Klucz dla zasad czytnika. |
   | EventHubNamespace |Przestrzeń nazw zawiera Centrum zdarzeń. |
   | EventHubName |Nazwę Centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w Centrum zdarzeń. |

3. Zapisz i Zamknij **App.config** pliku.

## <a name="deploy-the-topologies"></a>Topologie wdrażania

1. Z **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **EventHubReader** projekt i wybierz **przesyłania do systemu Storm w usłudze HDInsight**.

    ![Zrzut ekranu z Eksplorator rozwiązań z przesyłania do systemu Storm w usłudze HDInsight wyróżnione](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na **przesyłania topologii** okno dialogowe, wybierz użytkownika **klaster Storm**. Rozwiń węzeł **dodatkowe konfiguracje**, wybierz pozycję **ścieżki plików Java**, wybierz pozycję **...** i wybierz katalog, który zawiera plik JAR pobranego wcześniej. Na koniec kliknij **przesyłania**.

    ![Zrzut ekranu przedstawia topologię — okno dialogowe](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Po przesłaniu topologii **podglądu topologii Storm** pojawi się. Zaznacz, aby wyświetlić informacje o topologii **EventHubReader** topologię. w okienku po lewej stronie.

    ![Zrzut ekranu przedstawiający podgląd topologie Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Z **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **EventHubWriter** projekt i wybierz **przesyłania do systemu Storm w usłudze HDInsight**.

5. Na **przesyłania topologii** okno dialogowe, wybierz użytkownika **klaster Storm**. Rozwiń węzeł **dodatkowe konfiguracje**, wybierz pozycję **ścieżki plików Java**, wybierz pozycję **...** i wybierz katalog, który zawiera plik JAR pobranego wcześniej. Na koniec kliknij **przesyłania**.

6. Po przesłaniu topologii, Odśwież listę topologii w **podglądu topologii Storm** Aby sprawdzić, czy obu topologiach są uruchomione w klastrze.

7. W **podglądu topologii Storm**, wybierz pozycję **EventHubReader** topologii.

8. Aby otworzyć składnik podsumowania dla elementy bolt, kliknij dwukrotnie **LogBolt** składnika na diagramie.

9. W **modułów** wybierz jeden z linków w **portu** kolumny. Spowoduje to wyświetlenie informacji zarejestrowanych przez składnik. Zarejestrowane informacje jest podobny do następującego tekstu:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

Aby zatrzymać topologie, wybierz każdej topologii w **podglądu topologii Storm**, następnie kliknij przycisk **Kill**.

![Zrzut ekranu z Storm topologii podglądu, z wyróżnionym przyciskiem Kill](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie zapoznaniu spout centra zdarzeń w języku Java i zablokowanie od topologii C# do pracy z danymi w usłudze Azure Event Hubs. Aby dowiedzieć się więcej o tworzeniu topologie języka C#, zobacz następujące tematy:

* [Tworzenie topologii C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Podręcznik programowania punkt połączenia usługi](apache-storm-scp-programming-guide.md)
* [Przykładowe topologie dla systemu Storm w usłudze HDInsight](apache-storm-example-topology.md)
