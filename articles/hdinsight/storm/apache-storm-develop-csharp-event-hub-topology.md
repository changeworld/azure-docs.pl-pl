---
title: Przetwarzanie zdarzeń z usługi Event Hubs przy użyciu systemu Storm — Azure HDInsight
description: Dowiedz się, jak przetwarzać dane z usługi Azure Event Hubs w topologii Storm języka C# utworzone w programie Visual Studio, za pomocą narzędzi HDInsight dla programu Visual Studio.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: dd1a46ea008ce5f8fb02dd468b27494d231717f0
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483932"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (C#)

Dowiedz się, jak pracować z usługą Azure Event Hubs z [Apache Storm](https://storm.apache.org/) na HDInsight. Ten dokument używa topologii Storm języka C#, aby odczytywać i zapisywać dane z usługi Event Hubs

> [!NOTE]  
> Wersja języka Java tego projektu, zobacz [przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

W krokach w tym dokumencie używane platformy SCP.NET, pakiet NuGet, która ułatwia tworzenie topologii języka C# i składników do użytku z systemem Storm w HDInsight.

> [!IMPORTANT]  
> Podczas procedury w tym dokumencie zależą od środowisku deweloperskim Windows z programem Visual Studio, skompilowany projekt można przesyłać do platformy Storm w klastrze HDInsight, który korzysta z systemu Linux. Opartą na systemie Linux klastrów utworzonych po 28 października 2016 roku, obsługują tylko topologii platformy SCP.NET.

HDInsight 3.4 i większe wykorzystanie platformy Mono, aby uruchomić topologii języka C#. W przykładzie użyto w tym dokumencie współpracuje z HDInsight 3.6. Jeśli planujesz tworzenie własnych rozwiązań .NET dla HDInsight, sprawdź [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentu, aby przewidzieć potencjalne niezgodności.

### <a name="cluster-versioning"></a>Przechowywanie wersji klastra

Pakiet Microsoft.SCP.Net.SDK NuGet, których używasz dla projektu musi być zgodna wersja główna systemu STORM zainstalowanego w HDInsight. HDInsight w wersji 3.5 i 3.6 używać systemu Storm 1.x, więc używasz 1.0.x.x wersji platformy SCP.NET przy użyciu tych klastrów.

> [!IMPORTANT]  
> W przykładzie w tym dokumencie oczekuje HDInsight 3.5 i 3.6 klastra.
>
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. 

Topologie języka C# muszą również wskazywać .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Jak pracować z usługą Event Hubs

Firma Microsoft udostępnia zestaw składników Java, które mogą służyć do komunikacji z usługą Event Hubs pochodzących z topologii Storm. Można znaleźć pliku archiwum (plik JAR) Java, który zawiera HDInsight 3.6 zgodnej wersji tych składników na [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> A składniki są zapisywane w języku Java, możesz łatwo używać ich z topologii C#.

W tym przykładzie używane są następujące składniki:

* __Uzyskiwać za__: Odczytuje dane z usługi Event Hubs.
* __EventHubBolt__: Zapisuje dane do usługi Event Hubs.
* __EventHubSpoutConfig__: Umożliwia skonfigurowanie uzyskiwać za.
* __EventHubBoltConfig__: Umożliwia skonfigurowanie EventHubBolt.

### <a name="example-spout-usage"></a>Przykład użycia spout

Platformy SCP.NET udostępnia metody do dodawania uzyskiwać za do topologii. Te metody należy dodać spout niż dodawanie składników Java przy użyciu metod ogólnych. Poniższy przykład przedstawia sposób tworzenia spout przy użyciu __SetEventHubSpout__ i **EventHubSpoutConfig** metod dostarczonych przez platformy SCP.NET:

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

W poprzednim przykładzie tworzona nowego składnika elementu spout o nazwie __uzyskiwać za__i konfiguruje go do komunikowania się z Centrum zdarzeń. Wskazówka równoległości dla składnika jest równa liczbie partycji w zdarzeniu Centrum. To ustawienie umożliwia Storm do utworzenia wystąpienia składnika dla każdej partycji.

### <a name="example-bolt-usage"></a>Przykład użycia elementu bolt

Użyj **JavaComponmentConstructor** metodę, aby utworzyć wystąpienie elementu bolt. Poniższy przykład pokazuje, jak utworzyć i skonfigurować nowe wystąpienie klasy **EventHubBolt**:

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
> W tym przykładzie użyto wyrażenia Clojure przekazywany jako ciąg, zamiast **JavaComponentConstructor** utworzyć **EventHubBoltConfig**, tak jak w przykładzie spout. Każda metoda działa. Użyj metody, która działa najlepiej dla Ciebie.

## <a name="download-the-completed-project"></a>Pobieranie ukończone projektu

Możesz pobrać pełną wersję projektu utworzonego w tym artykule [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Jednakże nadal należy podać ustawienia konfiguracji, wykonując kroki opisane w tym artykule.

### <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm na HDInsight. Zobacz [tworzenie technologii Apache Hadoop clusters, przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz **Storm** dla **typ klastra**.

    > [!WARNING]  
    > W przykładzie użyto w tym dokumencie wymaga systemu Storm w HDInsight w wersji 3.5 i 3.6. To nie zadziała ze starszymi wersjami HDInsight, ze względu na istotne zmiany nazwy klasy. Wersja tego przykładu, który współdziała z starsze klastry dla [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Centrum zdarzeń platformy Azure](../../event-hubs/event-hubs-create.md).

* [Zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [Narzędzia HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 lub nowszy na swoje Środowisko deweloperskie. Zestaw JDK pliki do pobrania są dostępne z [Oracle](https://aka.ms/azure-jdks).

  * **JAVA_HOME** zmiennej środowiskowej musi wskazywać katalog zawierający języka Java.
  * **%JAVA_HOME%/bin** katalog musi znajdować się w ścieżce.

## <a name="download-the-event-hubs-components"></a>Pobierz składniki usługi Event Hubs

Pobierz spout usługi Event Hubs i elementu bolt składnika z [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Utwórz katalog o nazwie `eventhubspout`, a następnie zapisz plik w katalogu.

## <a name="configure-event-hubs"></a>Skonfiguruj usługę Event Hubs

Event Hubs to źródło danych, w tym przykładzie. Informacje przedstawione w sekcji "Tworzenie Centrum zdarzeń" [Rozpoczynanie pracy z usługą Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po utworzeniu Centrum zdarzeń, wyświetlić **EventHub** ustawienia w witrynie Azure portal i wybierz pozycję **zasady dostępu współdzielonego**. Wybierz **+ Dodaj** można dodać następujące zasady:

   | Name (Nazwa) | Uprawnienia |
   | --- | --- |
   | Składnik zapisywania |Wysyłanie |
   | Czytnik |Nasłuchiwanie |

    ![Okno zasady dostępu zrzut ekranu udziału](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Wybierz **czytnika** i **moduł zapisujący** zasad. Skopiuj i Zapisz wartość klucza podstawowego dla obu zasad, ponieważ te wartości są używane później.

## <a name="configure-the-eventhubwriter"></a>Konfigurowanie EventHubWriter

1. Jeśli nie już zainstalowano najnowszą wersję narzędzi HDInsight tools for Visual Studio, zobacz [rozpoczęcie korzystania z narzędzi HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Pobierz rozwiązanie z [eventhub storm hybrydowy](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. W **EventHubWriter** otwarty projekt **App.config** pliku. Użyj informacji z Centrum zdarzeń, które zostały wcześniej skonfigurowane do wypełnienia wartości następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |Składnik zapisywania (Jeśli używasz inną nazwę dla zasad o *wysyłania* uprawnienie, zamiast tego użyj.) |
   | EventHubPolicyKey |Klucz zasad składnika zapisywania. |
   | EventHubNamespace |Przestrzeń nazw zawierająca Centrum zdarzeń. |
   | EventHubName |Twoja nazwa Centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w Centrum zdarzeń. |

4. Zapisz i Zamknij **App.config** pliku.

## <a name="configure-the-eventhubreader"></a>Konfigurowanie EventHubReader

1. Otwórz **EventHubReader** projektu.

2. Otwórz **App.config** plik **EventHubReader**. Użyj informacji z Centrum zdarzeń, które zostały wcześniej skonfigurowane do wypełnienia wartości następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |Czytnik (Jeśli używasz inną nazwę dla zasad o *nasłuchiwania* uprawnienie, zamiast tego użyj.) |
   | EventHubPolicyKey |Klucz zasad czytnika. |
   | EventHubNamespace |Przestrzeń nazw zawierająca Centrum zdarzeń. |
   | EventHubName |Twoja nazwa Centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w Centrum zdarzeń. |

3. Zapisz i Zamknij **App.config** pliku.

## <a name="deploy-the-topologies"></a>Wdrażanie topologii

1. Z **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **EventHubReader** projektu, a następnie wybierz **Prześlij do systemu Storm w HDInsight**.

    ![Zrzut ekranu z Eksploratora rozwiązań Prześlij do systemu Storm w HDInsight wyróżniony](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na **przesyłanie topologii** okno dialogowe, wybierz opcję usługi **klastra Storm**. Rozwiń **dodatkowe konfiguracje**, wybierz opcję **ścieżki pliku Java**, wybierz opcję **...** i wybierz katalog, który zawiera plik JAR, który został wcześniej pobrany. Na koniec kliknij **przesyłania**.

    ![Zrzut ekranu przedstawia topologię, okno dialogowe](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Po przesłaniu topologii **podglądu topologii Storm** pojawia się. Zaznacz, aby wyświetlić informacje o topologii **EventHubReader** topologii, w okienku po lewej stronie.

    ![Zrzut ekranu przedstawiający podgląd topologie Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Z **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **EventHubWriter** projektu, a następnie wybierz **Prześlij do systemu Storm w HDInsight**.

5. Na **przesyłanie topologii** okno dialogowe, wybierz opcję usługi **klastra Storm**. Rozwiń **dodatkowe konfiguracje**, wybierz opcję **ścieżki pliku Java**, wybierz opcję **...** i wybierz katalog, który zawiera plik JAR pobrane wcześniej. Na koniec kliknij **przesyłania**.

6. Po przesłaniu topologii, Odśwież listę topologii, w **podglądu topologii Storm** Aby sprawdzić, czy obu topologiach są uruchomione w klastrze.

7. W **podglądu topologii Storm**, wybierz opcję **EventHubReader** topologii.

8. Aby otworzyć składnik podsumowania dla elementu bolt, kliknij dwukrotnie **LogBolt** składnik na diagramie.

9. W **Executors** wybierz jedno z łączy w **portu** kolumny. Spowoduje to wyświetlenie informacji rejestrowanych przez składnik. Zarejestrowane informacje są podobne do następującego tekstu:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

Aby zatrzymać topologii, wybierz każdej topologii w **podglądu topologii Storm**, następnie kliknij przycisk **Kill**.

![Zrzut ekranu z systemu Storm topologii przeglądarki, z wyróżnionym przyciskiem polecenia Kill](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie mają przedstawiono sposób użycia spout usługi Event Hubs dla języka Java i elementu bolt od topologii języka C# do pracy z danymi w usłudze Azure Event Hubs. Aby dowiedzieć się więcej na temat tworzenia topologii języka C#, zobacz następujące tematy:

* [Opracowywanie topologii języka C# dla Storm Apache na HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Podręcznik programowania punkt połączenia usługi](apache-storm-scp-programming-guide.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](apache-storm-example-topology.md)
