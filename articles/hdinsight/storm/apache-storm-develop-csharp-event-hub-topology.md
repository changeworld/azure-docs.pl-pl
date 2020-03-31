---
title: Przetwarzanie zdarzeń z centrów zdarzeń za pomocą usługi Storm — usługa Azure HDInsight
description: Dowiedz się, jak przetwarzać dane z usługi Azure Event Hubs za pomocą topologii burzy języka C# utworzonej w programie Visual Studio przy użyciu narzędzi HDInsight dla programu Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981634"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Przetwarzanie zdarzeń z usługi Azure Event Hubs za pomocą usługi Apache Storm w programie HDInsight (C#)

Dowiedz się, jak pracować z centrum zdarzeń platformy Azure z [usługi Apache Storm](https://storm.apache.org/) w usłudze HDInsight. Ten dokument używa topologii burzy języka C# do odczytu i zapisu danych z Centrum zdarzeń

> [!NOTE]  
> Aby uzyskać wersję java tego projektu, zobacz [Przetwarzanie zdarzeń z usługi Azure Event Hubs z usługą Apache Storm w programie HDInsight (Java).](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

## <a name="scpnet"></a>SCP.NET

Kroki w tym dokumencie używają SCP.NET, pakietu NuGet, który ułatwia tworzenie topologii języka C# i składników do użycia z Storm on HDInsight.

HDInsight 3.4 i więcej używać Mono do uruchamiania topologii języka C#. Przykład użyty w tym dokumencie działa z HDInsight 3.6. Jeśli planujesz tworzenie własnych rozwiązań platformy .NET dla usługi HDInsight, sprawdź dokument [zgodności mono](https://www.mono-project.com/docs/about-mono/compatibility/) pod kątem potencjalnych niezgodności.

### <a name="cluster-versioning"></a>Przechowywanie wersji klastra

Pakiet Microsoft.SCP.Net.SDK NuGet używany dla projektu musi być zgodny z główną wersją storm zainstalowaną w programie HDInsight. Usługa HDInsight w wersjach 3.5 i 3.6 używa storm 1.x, więc należy używać SCP.NET wersji 1.0.x.x z tymi klastrami.

Topologie języka C# musi również być ukierunkowane na .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracować z Centrami zdarzeń

Firma Microsoft udostępnia zestaw składników języka Java, które mogą służyć do komunikowania się z centrami zdarzeń z topologii storm. Plik archiwum Java (JAR) zawierający wersję tych składników zgodną z hdinsight 3.6 można znaleźć na poziomie [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Podczas gdy składniki są zapisywane w języku Java, można łatwo używać ich z topologii języka C#.

W tym przykładzie używane są następujące składniki:

* __EventHubSpout__: odczytuje dane z centrów zdarzeń.
* __EventHubBolt__: Zapisuje dane w centrach zdarzeń.
* __EventHubSpoutConfig__: Służy do konfigurowania EventHubSpout.
* __EventHubBoltConfig__: Służy do konfigurowania EventHubBolt.

### <a name="example-spout-usage"></a>Przykładowe użycie dziobka

SCP.NET udostępnia metody dodawania EventHubSpout do topologii. Te metody ułatwiają dodawanie dziobka niż przy użyciu ogólnych metod dodawania składnika Java. W poniższym przykładzie pokazano, jak utworzyć wylewkę przy użyciu __SetEventHubSpout__ i **EventHubSpoutConfig** metody dostarczone przez SCP.NET:

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

W poprzednim przykładzie tworzy nowy składnik wylewki o nazwie __EventHubSpout__i konfiguruje go do komunikowania się z centrum zdarzeń. Wskazówka równoległości dla składnika jest ustawiona na liczbę partycji w centrum zdarzeń. To ustawienie umożliwia storm utworzyć wystąpienie składnika dla każdej partycji.

### <a name="example-bolt-usage"></a>Przykładowe użycie śruby

Użyj **Metody JavaComponmentConstructor,** aby utworzyć wystąpienie śruby. W poniższym przykładzie pokazano, jak utworzyć i skonfigurować nowe wystąpienie **EventHubBolt:**

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
> W tym przykładzie użyto wyrażenie Clojure przekazywane jako ciąg, zamiast przy użyciu **JavaComponentConstructor** do tworzenia **EventHubBoltConfig**, jak w przykładzie dziobka nie. Każda z tych metod działa. Użyj metody, która najlepiej ci odpowiada.

## <a name="download-the-completed-project"></a>Pobierz ukończony projekt

Pełną wersję projektu utworzoną w tym artykule można pobrać z [gitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Jednak nadal należy podać ustawienia konfiguracji, wykonując kroki opisane w tym artykule.

### <a name="prerequisites"></a>Wymagania wstępne

* Klaster Burzy Apache w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Burza** dla **typu klastra**.

* [Centrum zdarzeń platformy Azure](../../event-hubs/event-hubs-create.md).

* [zestaw SDK platformy Azure .NET](https://azure.microsoft.com/downloads/).

* [Narzędzia HDInsight dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 lub nowsza w środowisku programistycznym. Pliki do pobrania JDK są dostępne w [oracle](https://aka.ms/azure-jdks).

  * **Zmienna** środowiskowa JAVA_HOME musi wskazywać katalog zawierający język Java.
  * Katalog **%JAVA_HOME%/bin** musi znajdować się w ścieżce.

## <a name="download-the-event-hubs-components"></a>Pobieranie składników Centrum zdarzeń

Pobierz wylewka centrum zdarzeń [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)i komponent śruby z programu .

Utwórz katalog `eventhubspout`o nazwie i zapisz plik w katalogu.

## <a name="configure-event-hubs"></a>Konfigurowanie centrów zdarzeń

Centra zdarzeń jest źródłem danych w tym przykładzie. Użyj informacji w sekcji "Tworzenie centrum zdarzeń" w [sekcji Wprowadzenie do centrów zdarzeń](../../event-hubs/event-hubs-create.md).

1. Po utworzeniu Centrum zdarzeń wyświetl ustawienia **usługi EventHub** w witrynie Azure portal i wybierz pozycję **Zasady dostępu współdzielonego**. Wybierz **+ Dodaj,** aby utworzyć następujące zasady:

   | Nazwa | Uprawnienia |
   | --- | --- |
   | Pisarz |Wysyłanie |
   | Czytnik |Nasłuchiwanie |

    ![Zrzut ekranu przedstawiający okno Zasady dostępu udostępniania](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Wybierz zasady **czytnika** i **modułu zapisującego.** Skopiuj i zapisz wartość klucza podstawowego dla obu zasad, ponieważ te wartości są używane później.

## <a name="configure-the-eventhubwriter"></a>Konfigurowanie programu EventHubWriter

1. Jeśli nie zainstalowano jeszcze najnowszej wersji narzędzi USŁUGI HDInsight dla programu Visual Studio, zobacz [Wprowadzenie do korzystania z narzędzi USŁUGI HDInsight dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Pobierz rozwiązanie z [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Otwórz **EventHubExample.sln**. W projekcie **EventHubWriter** otwórz plik **App.config.** Użyj informacji z centrum zdarzeń skonfigurowanego wcześniej, aby wypełnić wartość dla następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | Nazwa EventHubPolicyName |moduł zapisujący (jeśli użyto innej nazwy dla zasad z uprawnieniem *Wyślij,* użyj jej zamiast tego). |
   | EventHubPolicyKey (Klucz) |Klucz dla zasad modułu zapisującego. |
   | Obszar EventHubNamespace |Obszar nazw zawierający centrum zdarzeń. |
   | Nazwa eventhubname |Nazwa centrum zdarzeń. |
   | Liczba uczestników EventHub |Liczba partycji w centrum zdarzeń. |

4. Zapisz i zamknij plik **App.config.**

## <a name="configure-the-eventhubreader"></a>Konfigurowanie aplikacji EventHubReader

1. Otwórz projekt **EventHubReader.**

2. Otwórz plik **App.config** dla **aplikacji EventHubReader**. Użyj informacji z centrum zdarzeń skonfigurowanego wcześniej, aby wypełnić wartość dla następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | Nazwa EventHubPolicyName |reader (Jeśli użyto innej nazwy dla zasad z uprawnieniami *nasłuchiwania,* użyj jej zamiast tego). |
   | EventHubPolicyKey (Klucz) |Klucz dla zasad czytnika. |
   | Obszar EventHubNamespace |Obszar nazw zawierający centrum zdarzeń. |
   | Nazwa eventhubname |Nazwa centrum zdarzeń. |
   | Liczba uczestników EventHub |Liczba partycji w centrum zdarzeń. |

3. Zapisz i zamknij plik **App.config.**

## <a name="deploy-the-topologies"></a>Wdrażanie topologii

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **EventHubReader,** a następnie wybierz pozycję **Prześlij do burzy w programie HDInsight**.

    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań z wyróżnioną pozycją Prześlij do burzy w programie HDInsight](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. W oknie dialogowym **Przesyłanie topologii** wybierz **klaster burzowy**. Rozwiń węzeł **Konfiguracja dodatkowa**, wybierz **ścieżkę plików Java**, wybierz **...** i wybierz katalog zawierający pobrany wcześniej plik JAR. Na koniec kliknij przycisk **Prześlij**.

    ![Zrzut ekranu przedstawiający okno dialogowe Prześlij topologię](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Po przesłaniu topologii pojawi się **Przeglądarka topologii burzy.** Aby wyświetlić informacje o topologii, wybierz topologię **EventHubReader** w lewym okienku.

    ![Zrzut ekranu przedstawiający przeglądarkę Storm Topologies Viewer](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **EventHubWriter,** a następnie wybierz pozycję **Prześlij do burzy w programie HDInsight**.

5. W oknie dialogowym **Przesyłanie topologii** wybierz **klaster burzowy**. Rozwiń **pozycję Konfiguracje dodatkowe**, wybierz **ścieżkę plików Java**, wybierz **...** i wybierz katalog zawierający pobrany wcześniej plik JAR. Na koniec kliknij przycisk **Prześlij**.

6. Po przesłaniu topologii odśwież listę topologii w **przeglądarce Storm Topologies Viewer,** aby sprawdzić, czy obie topologie są uruchomione w klastrze.

7. W **przeglądarce Storm Topologies Viewer**wybierz topologię **EventHubReader.**

8. Aby otworzyć podsumowanie komponentu dla śruby, kliknij dwukrotnie komponent **LogBolt** na diagramie.

9. W sekcji **Executors** wybierz jedno z łączy w kolumnie **Port.** Wyświetla informacje zarejestrowane przez składnik. Zarejestrowane informacje są podobne do następującego tekstu:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

Aby zatrzymać topologie, wybierz każdą topologię w **przeglądarce topologii burzy,** a następnie kliknij przycisk **Zabij**.

![Zrzut ekranu przedstawiający przeglądarkę topologii burzy z wyróżnionym przyciskiem Kill](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się, jak używać wylewki i śruby usługi Java Event Hubs z topologii języka C# do pracy z danymi w usłudze Azure Event Hubs. Aby dowiedzieć się więcej na temat tworzenia topologii języka C#, zobacz następujące elementy:

* [Tworzenie topologii języka C# dla usługi Apache Storm w programie HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Przewodnik po programowaniu SCP](apache-storm-scp-programming-guide.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](apache-storm-example-topology.md)
