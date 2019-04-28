---
title: Topologii Apache Storm przy użyciu programu Visual Studio i języka C# — Azure HDInsight
description: Dowiedz się, jak tworzenie topologii Storm języka C#. Utwórz do topologii zliczania wyrazów proste w programie Visual Studio przy użyciu narzędzi usługi Hadoop dla programu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: ''
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 11/27/2017
ms.date: 04/01/2019
ms.author: v-yiso
ms.openlocfilehash: 14aa45808f44f7ca6fe34b70ef282a99f230bf0d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125225"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Opracowywanie topologii języka C# dla Apache Storm przy użyciu narzędzi Data Lake tools for Visual Studio

Dowiedz się, jak utworzyć C# topologii Apache Storm przy użyciu narzędzi Azure Data Lake (Apache Hadoop) dla programu Visual Studio. W tym dokumencie przedstawiono proces tworzenia projektu systemu Storm w programie Visual Studio, testowanie jej lokalnie i wdrażania go do systemu Apache Storm w klastrze Azure HDInsight.

Poznasz również sposób tworzyć hybrydowe topologie, korzystających z języka C# i składników Java.

> [!NOTE]
> Podczas procedury w tym dokumencie zależą od środowisku deweloperskim Windows z programem Visual Studio, skompilowanych projektów możliwych do przesłania do klastra systemu Linux albo systemem Windows HDInsight. Opartą na systemie Linux klastrów utworzonych po 28 października 2016 roku, obsługują tylko topologii platformy SCP.NET.

Aby użyć topologii C# z klastrem opartych na systemie Linux, należy zaktualizować pakiet Microsoft.SCP.Net.SDK NuGet używany w projekcie do wersji 0.10.0.6 lub nowszej. Wersja pakietu musi być również zgodna z wersją główną systemu Storm zainstalowanego w usłudze HDInsight.

| HDInsight w wersji | Wersja systemu Apache Storm | Wersja platformy SCP.NET | Domyślna wersja narzędzia Mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(tylko na podstawie Windows HDInsight) | Nie dotyczy |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> Topologie C# w klastrach opartych na systemie Linux muszą korzystać z platformy .NET 4.5 i używać platformy Mono, aby mogły działać w klastrze usługi HDInsight. Sprawdź [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/) aby przewidzieć potencjalne niezgodności.

## <a name="install-visual-studio"></a>Instalacja programu Visual Studio

Możesz tworzyć topologie języka C# przy użyciu platformy SCP.NET, przy użyciu jednej z następujących wersji programu Visual Studio:

* Program Visual Studio 2012 z aktualizacją Update 4

* Visual Studio 2013 z aktualizacją Update 4 lub [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Program Visual Studio 2015 lub [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Program Visual Studio 2017 (w każdej wersji)

## <a name="install-data-lake-tools-for-visual-studio"></a>Install Data Lake tools for Visual Studio

Aby zainstalować narzędzia Data Lake tools dla programu Visual Studio, wykonaj kroki opisane w [rozpoczęcie korzystania z narzędzi Data Lake tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Zainstalować środowisko Java

Gdy prześlesz topologii Storm za pomocą programu Visual Studio, platformy SCP.NET generuje plik zip, który zawiera topologii i zależności. Java służy do tworzenia tych plików zip, ponieważ używa ona formatu, który jest bardziej zgodne z klastrów opartych na systemie Linux.

1. Instalowanie języka Java Developer Kit (JDK) 7 lub nowszy na swoje Środowisko deweloperskie. Możesz uzyskać JDK Oracle z [Oracle](https://aka.ms/azure-jdks). Można również użyć [inne dystrybucje Java](https://openjdk.java.net/).

2. `JAVA_HOME` Zmiennej środowiskowej musi wskazywać katalog zawierający języka Java.

3. `PATH` Musi zawierać zmienną środowiskową `%JAVA_HOME%\bin` katalogu.

Aby sprawdzić, czy języka Java i zestawu JDK są poprawnie zainstalowane, można użyć następujących aplikacji konsolowej C#:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Szablony systemu Apache Storm

Data Lake tools for Visual Studio zapewniają następujące szablony:

| Project type (Typ projektu) | Demonstracje |
| --- | --- |
| Aplikacja STORM |Pusty projekt topologii systemu Storm. |
| Przykładowy składnik zapisywania usługi Azure SQL STORM |Jak napisać do usługi Azure SQL Database. |
| Przykład odczytywania usługi Azure Cosmos DB STORM |Jak odczytać z usługi Azure Cosmos DB. |
| Przykładowy składnik zapisywania usługi Azure Cosmos DB STORM |Jak napisać do usługi Azure Cosmos DB. |
| Przykładowe czytnika Centrum zdarzeń platformy STORM |Jak odczytać z usługi Azure Event Hubs. |
| Przykładowy składnik zapisywania usługi EventHub STORM |Jak napisać do usługi Azure Event Hubs. |
| Przykładowe bazy danych HBase czytnika STORM |Jak można odczytać z bazy danych HBase w klastrach HDInsight. |
| Przykładowe bazy danych HBase zapisywania STORM |Jak można zapisać do bazy danych HBase w klastrach HDInsight. |
| Przykład hybrydowego systemu STORM |Jak używać składników Java. |
| Przykładowe STORM |Podstawowej topologii zliczania wyrazów. |

> [!WARNING]  
> Nie wszystkie szablony pracować HDInsight opartych na systemie Linux. Pakiety NuGet służące przez Szablony mogą być niezgodne z platformy Mono. Sprawdź [zgodności platformy Mono](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentów i użyj [narzędzia .NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) do identyfikowania potencjalnych problemów.

W krokach w tym dokumencie umożliwia Podstawowa aplikacja Storm projektu typu utworzyć topologię.

### <a name="apache-hbase-templates-notes"></a>Informacje o szablonach bazy danych Apache HBase

Szablony czytników i składników zapisywania bazy danych HBase, użyj interfejsu API REST HBase nie bazy danych HBase interfejsu API języka Java, do komunikowania się z bazą danych HBase w klastrze HDInsight.

### <a name="eventhub-templates-notes"></a>Informacje o szablonach usługi EventHub

> [!IMPORTANT]
> Składnika elementu spout EventHub opartych na języku Java, zawartych w szablonie czytnika Centrum zdarzeń mogą nie działać z systemu Storm w HDInsight w wersji 3.5 lub nowszej. Zaktualizowaną wersję tej części znajduje się w temacie [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Przykładową topologię, która używa tej wartości składników i współpracuje z systemu Storm w HDInsight 3.5, zobacz [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Tworzenie topologii C#

1. Otwórz program Visual Studio, wybierz **pliku** > **New**, a następnie wybierz pozycję **projektu**.

2. Z **nowy projekt** okna, rozwiń węzeł **zainstalowane** > **szablony**i wybierz **usługi Azure Data Lake**. Z listy szablonów wybierz **aplikacja Storm**. W dolnej części ekranu podaj **WordCount** jako nazwa aplikacji.

    ![Zrzut ekranu nowego projektu okna](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Po utworzeniu projektu, musisz mieć następujące pliki:

   * **Program.cs**: Ten plik definiuje topologii dla Twojego projektu. Domyślna topologia, który składa się z jednego spout i bolt jeden jest domyślnie tworzone.

   * **Spout.cs**: Spout przykładu, który emituje liczb losowych.

   * **Bolt.cs**: Przykład bolt, który śledzi liczbę cyfr wyemitowane przez spout.

     Podczas tworzenia projektu NuGet pobiera najnowsze [pakietu platformy SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementowanie spout

1. Otwórz **Spout.cs**. Elementy spout są używane do odczytywania danych w topologii z zewnętrznego źródła. Główne składniki dla spout są:

   * **NextTuple**: Wywoływane przez Storm gdy spout może emitować nowe krotek.

   * **Potwierdzenia** (tylko w przypadku topologii transakcyjnych): Obsługuje inicjowane przez inne składniki w topologii dla krotek wysyłane z spout potwierdzeń. Potwierdzenie krotki pozwala spout wiedzieć, że jej został pomyślnie przetworzony przez składniki podrzędne.

   * **Niepowodzenie** (tylko w przypadku topologii transakcyjnych): Obsługuje krotek, które są niepowodzenie przetwarzania innych składników w topologii. Implementacja metody niepowodzenie pozwala emitować spójna kolekcja znajdująca się ponownie, dzięki czemu mogą być przetwarzane ponownie.

2. Zastąp zawartość **elementu Spout** klasy z następującym tekstem: Ta spout losowo emituje zdanie do topologii.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementowanie bolt

1. Usuń istniejącą **Bolt.cs** pliku z projektem.

2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Dodaj** > **nowy element**. Wybierz z listy, **elementu Bolt systemu Storm**, a następnie wprowadź **Splitter.cs** jako nazwę. Powtórz ten proces, aby utworzyć drugiego elementu bolt o nazwie **Counter.cs**.

   * **Splitter.cs**: Implementuje elementu bolt, który dzieli zdania na poszczególne wyrazy oraz emituje nowy strumień słów.

   * **Counter.cs**: Implementuje elementu bolt, który zlicza każdego wyrazu, a następnie generuje nowy strumień słowa i liczbę dla każdego wyrazu.

     > [!NOTE]
     > Te elementy bolt odczytu i zapisu do strumieni, ale można również użyć elementu bolt do komunikowania się ze źródłami, takimi jak bazy danych lub usługi.

3. Otwórz **Splitter.cs**. Domyślnie ma tylko jedną metodę: **Wykonaj**. Metody Execute jest wywoływana, gdy element bolt odbiera krotki do przetworzenia. W tym miejscu możesz przeczytać przetwarzania krotek przychodzących i emitować krotek ruchu wychodzącego.

4. Zastąp zawartość **rozdzielacz** klasy z następującym kodem:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Otwórz **Counter.cs**i zastąp jego zawartość klasy z następującym kodem:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Zdefiniuj topologię

Elementy spout i bolt są rozmieszczone na wykresie, który definiuje sposób przepływu danych między składnikami. Ta topologia wykresu jest następująca:

![Diagram przedstawiający sposób rozmieszczenia elementów](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Zdania są emitowane przez spout i są dystrybuowane do wystąpienia elementu bolt rozdzielacza. Bolt rozdzielacz przerywa zdania w wyrazy, które są dystrybuowane do elementu bolt licznika.

Ponieważ wyrazów są przechowywane lokalnie w wystąpieniu licznika, chcesz upewnić się, że określone słowa flow to samo wystąpienie elementu bolt licznika. Każde wystąpienie przechowuje informacje o konkretnych słów. Ponieważ bolt rozdzielacz przechowuje bez stanu, tak naprawdę nie ma znaczenia którego wystąpienia rozdzielacza odbiera które zdanie.

Otwórz **Program.cs**. Metoda ważne jest **GetTopologyBuilder**, która jest używana do definiowania topologię, która zostanie przesłana do systemu Storm. Zastąp zawartość **GetTopologyBuilder** przy użyciu następującego kodu, aby wdrożyć topologię opisanych powyżej:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Przesyłanie topologii

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Prześlij do systemu Storm w HDInsight**.

   > [!NOTE]
   > Po wyświetleniu monitu wprowadź poświadczenia dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do tego, który zawiera Storm w klastrze HDInsight.

2. Wybierz usługi Storm w klastrze HDInsight z **klastra Storm** listy rozwijanej, a następnie wybierz **przesyłania**. Można monitorować, jeśli przesyłanie zakończy się za pomocą **dane wyjściowe** okna.

3. Po pomyślnym przesłaniu topologii **topologii Storm** dla klastra powinna zostać wyświetlona. Wybierz **WordCount** topologii z listy, aby wyświetlić informacje o uruchomionej topologii.

   > [!NOTE]
   > Można również wyświetlić **topologii Storm** z **Eksploratora serwera**. Rozwiń **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy platformy Storm w klastrze HDInsight, a następnie wybierz **wyświetl topologie Storm**.

    Aby wyświetlić informacje o składnikach w topologii, kliknij dwukrotnie składnik na diagramie.

4. Z **podsumowanie topologii** wyświetlić, kliknij przycisk **Kill** zatrzymać topologię.

   > [!NOTE]
   > Topologie STORM nadal działać, dopóki nie zostaną one wyłączone lub klaster jest usuwany.

## <a name="transactional-topology"></a>Topologia transakcyjne

Poprzednie topologia nie jest transakcyjna. Składniki w topologii nie należy implementować funkcje odtwarzanie wiadomości. Na przykład topologii transakcyjnych. utworzenie projektu i wybierz **przykładowe Storm** jako typ projektu.

Topologie transakcyjnych zaimplementować następujące polecenie, aby obsługiwać powtarzania danych:

* **Buforowanie metadanych**: Spout muszą być przechowywane metadane na temat danych emitowanych tak, aby można je pobrać i emitowane ponownie, jeśli wystąpi błąd danych. Ponieważ dane wyemitowane przez próbki jest mała, dane pierwotne dotyczące każda krotka znajduje się w słownik na potrzeby powtarzania.

* **Potwierdzenia**: Każdy element bolt w topologii można wywołać `this.ctx.Ack(tuple)` można potwierdzić, że pomyślnie przetworzył spójnej kolekcji. Gdy wszystkie elementy bolt potwierdzeniu krotki, `Ack` wywoływana jest metoda spout. `Ack` Metoda umożliwia spout usunąć dane, które zostało w pamięci podręcznej powtórzeń.

* **Niepowodzenie**: Każdy element bolt może wywołać `this.ctx.Fail(tuple)` do wskazania, że przetwarzanie nie powiodło się dla krotki. Błąd propaguje do `Fail` metoda spout, gdzie można odtworzyć spójna kolekcja znajdująca się przy użyciu buforowanych metadanych.

* **Identyfikator sekwencji**: Podczas emitowania krotki, można określić identyfikator unikatowy ciąg. Ta wartość określa spójnej kolekcji dla przetwarzania powtarzania (Ack i kończyć się niepowodzeniem). Na przykład spout w **przykładowe Storm** projekt używa następujących podczas emitowania danych:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Ten kod emituje spójną kolekcją, która zawiera zdanie w strumieniu domyślną wartość Identyfikatora sekwencji, które są zawarte w **lastSeqId**. W tym przykładzie **lastSeqId** są zwiększane dla każdego krotki emitowane.

Jak pokazano w **przykładowe Storm** projektu, czy składnik jest transakcyjna można ustawić w czasie wykonywania, na podstawie konfiguracji.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia hybrydowych przy użyciu języka C# i Java

Data Lake tools for Visual Studio umożliwia również tworzyć hybrydowe topologie, gdzie niektóre składniki znajdujące się języka C#, a inne dotyczą środowiska Java.

Na przykład topologii hybrydowego utworzyć projekt, a następnie wybierz pozycję **przykład hybrydowego Storm**. Ten typ przykładowy pokazuje następujące pojęcia:

* **Java spout** i  **C# bolt**: Zdefiniowane w **HybridTopology_javaSpout_csharpBolt**.

    * Transakcyjne wersja jest zdefiniowany w **HybridTopologyTx_javaSpout_csharpBolt**.

* **C#spout** i **bolt języka Java**: Zdefiniowane w **HybridTopology_csharpSpout_javaBolt**.

    * Transakcyjne wersja jest zdefiniowany w **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Ta wersja również pokazano, jak użyć Clojure kodu z pliku tekstowego jako składnik języka Java.


Aby przełączyć topologię, która jest używana, gdy zostanie przesłana projektu, Przenieś `[Active(true)]` instrukcji topologii, którego chcesz użyć, przed przesłaniem jej do klastra.

> [!NOTE]
> Wszystkie pliki języka Java, które są wymagane są dostarczane w ramach tego projektu w **JavaDependency** folderu.

Podczas tworzenia i przesyłania topologii hybrydowego, należy wziąć pod uwagę następujące czynności:

* Użyj **JavaComponentConstructor** można utworzyć wystąpienia klasy Java dla spout lub elementu bolt.

* Użyj **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** do serializowania danych do lub z składników Java z obiektów Java do formatu JSON.

* Podczas przesyłania topologii do serwera, należy użyć **dodatkowe konfiguracje** opcję, aby określić **ścieżki pliku Java**. Określona ścieżka powinna być katalog, który zawiera pliki JAR, które zawierają swojej klasy języka Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Wersja platformy SCP.NET 0.9.4.203 wprowadza nową klasę i metodę przeznaczone specjalnie do pracy z spout Centrum zdarzeń (Java spout, która odczytuje z usługi Event Hubs). Kiedy tworzysz topologię, która używa spout Centrum zdarzeń, należy użyć następujących metod:

* **EventHubSpoutConfig** klasy: Tworzy obiekt, który zawiera konfigurację składnika elementu spout.

* **TopologyBuilder.SetEventHubSpout** metody: Dodaje składnika elementu spout Centrum zdarzeń do topologii.

> [!NOTE]
> Nadal należy użyć **CustomizedInteropJSONSerializer** do serializowania danych wytworzonych przez spout.

## <a id="configurationmanager"></a>Użyj ConfigurationManager

Nie używaj **ConfigurationManager** można pobrać wartości konfiguracji z elementu bolt i elementu spout składników. Może to spowodować wyjątek wskaźnika o wartości null. Zamiast tego konfigurację dla Twojego projektu jest przekazywana do topologii systemu Storm jako para klucza i wartości w kontekście topologii. Każdy składnik, który opiera się na wartości konfiguracji musi pobrać je z kontekstu podczas inicjowania.

Poniższy kod przedstawia sposób pobierania tych wartości:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Jeśli używasz `Get` metodę, aby zwrócić wystąpienia składnika, należy upewnić się, że przekazuje zarówno `Context` i `Dictionary<string, Object>` parametrów konstruktora. Poniższy przykład to podstawowy `Get` metodę, która poprawnie przekazuje te wartości:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak zaktualizować platformy SCP.NET

Najnowsze wersje platformy SCP.NET obsługuje uaktualnienie pakietu za pośrednictwem pakietu NuGet. Gdy dostępna jest nowa aktualizacja, otrzymasz powiadomienia o uaktualnieniu. Aby ręcznie sprawdzić, czy uaktualnienie, wykonaj następujące kroki:

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.

2. Wybierz z Menedżera pakietów **aktualizacje**. Jeśli aktualizacja jest dostępna, jest ona wyświetlana. Kliknij przycisk **aktualizacji** pakietu go zainstalować.

> [!IMPORTANT]
> Jeśli projekt został utworzony we wcześniejszej wersji platformy SCP.NET, że nie używasz NuGet, należy wykonać poniższe kroki, aby zaktualizować do nowszej wersji:
>
> 1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.
> 2. Za pomocą **wyszukiwania** pola, Wyszukaj, a następnie dodaj **Microsoft.SCP.Net.SDK** do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Rozwiązywanie typowych problemów z topologii

### <a name="null-pointer-exceptions"></a>Wyjątki wskaźnika o wartości null

Korzystając z wirtualnej topologii języka C# z klastrem HDInsight opartych na systemie Linux, elementu bolt i elementu spout składniki, które używają **ConfigurationManager** można odczytać ustawień konfiguracji w czasie wykonywania mogą zwracać wyjątki wskaźnika o wartości null.

Konfiguracja projektu jest przekazywany do topologii Storm jako para klucza i wartości w kontekście topologii. Mogą być pobierane z obiektu słownik, który jest przekazywany do składników, gdy są one inicjowane.

Aby uzyskać więcej informacji, zobacz [ConfigurationManager](#configurationmanager) części tego dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Jeśli używasz topologii C# z klastrem HDInsight opartych na systemie Linux, może wystąpić następujący błąd:

    System.TypeLoadException: Failure has occurred while loading a type.

Ten błąd występuje, gdy używasz plik binarny, który nie jest zgodna z wersją platformy .NET, który obsługuje platformy Mono.

W przypadku klastrów HDInsight opartych na systemie Linux upewnij się, że projekt korzysta z plików binarnych skompilowanych dla platformy .NET 4.5.

### <a name="test-a-topology-locally"></a>Testowanie topologii lokalnie

Chociaż jest łatwa do wdrożenia topologii do klastra, w niektórych przypadkach może być konieczne test topologii lokalnie. Wykonaj następujące kroki, aby uruchomić i przetestować przykładową topologię w tym samouczku lokalnie w środowisku programistycznym.

> [!WARNING]
> Lokalne testowanie działa tylko w przypadku basic, C# — tylko topologii. Nie można użyć lokalnego testowania hybrydowe topologie lub topologie, wykorzystujące wiele strumieni.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **właściwości**. We właściwościach projektu należy zmienić **typ danych wyjściowych** do **aplikację Konsolową**.

    ![Zrzut ekranu przedstawiający właściwości projektu z wyróżnionym typem danych wyjściowych](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Pamiętaj, aby zmienić **typ danych wyjściowych** do **biblioteki klas** przed wdrożeniem topologii do klastra.

2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Dodaj** > **nowy element**. Wybierz **klasy**, a następnie wprowadź **LocalTest.cs** jako nazwę klasy. Na koniec kliknij **Dodaj**.

3. Otwórz **LocalTest.cs**i dodaj następującą **przy użyciu** instrukcji u góry:

    ```csharp
    using Microsoft.SCP;
    ```

4. Użyj poniższego kodu, zawartość, **LocalTest** klasy:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Poświęć chwilę na przeczytanie komentarze w kodzie. Ten kod używa **LocalContext** można uruchamiać składniki w środowisku deweloperskim, a będzie się powtarzał strumienia danych między składnikami w plikach tekstowych na dysku lokalnym.

1. Otwórz **Program.cs**i dodaj następujące polecenie, aby **Main** metody:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Zapisz zmiany, a następnie kliknij przycisk **F5** lub wybierz **debugowania** > **Rozpocznij debugowanie** Aby uruchomić projekt. Okno konsoli należy pojawiają się i rejestrowanie stanu, postępu testów. Gdy **testy Zakończono** zostanie wyświetlony, naciśnij dowolny klawisz, aby zamknąć okno.

3. Użyj **Eksplorator Windows** można zlokalizować katalogu, który zawiera projekt. Na przykład: **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. W tym katalogu, otwórz **Bin**, a następnie kliknij przycisk **debugowania**. Powinien zostać wyświetlony pliki tekstowe, które zostały utworzone, gdy testy zostały uruchomione: sentences.txt counter.txt i splitter.txt. Otwórz każdy plik tekstowy i sprawdzać dane.

   > [!NOTE]
   > Dane ciągu są utrwalane jako tablica wartości dziesiętnych w tych plikach. Na przykład \[[97,103,111]] w **splitter.txt** pliku ma postać wyrazu *i*.

> [!NOTE]
> Pamiętaj ustawić **typ projektu** do **biblioteki klas** przed wdrożeniem platformy Storm w klastrze HDInsight.

### <a name="log-information"></a>Informacje w Dzienniku

Można łatwo rejestrować informacje ze składników topologii, za pomocą `Context.Logger`. Na przykład następujące polecenie tworzy wpis informacyjny w dzienniku:

```csharp
Context.Logger.Info("Component started");
```

Zarejestrowane informacje mogą być wyświetlane z **dziennik usługi Hadoop**, który znajduje się w **Eksploratora serwera**. Rozwiń pozycję dla usługi Storm w klastrze HDInsight, a następnie rozwiń **dziennik usługi Hadoop**. Na koniec wybierz plik dziennika, aby wyświetlić.

> [!NOTE]
> Dzienniki są przechowywane na koncie usługi Azure storage, które jest używane przez klaster. Aby wyświetlić dzienniki w programie Visual Studio, musisz zarejestrować się do subskrypcji platformy Azure, który jest właścicielem konta magazynu.

### <a name="view-error-information"></a>Wyświetl informacje o błędzie

Aby wyświetlić błędy, które miały miejsce w uruchomionej topologii, wykonaj następujące kroki:

1. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy systemu Storm w klastrze HDInsight i wybierz **topologii Storm widoku**.

2. Aby uzyskać **elementu Spout** i **Bolts**, **ostatni błąd** kolumna zawiera informacje dotyczące ostatniego błędu.

3. Wybierz **identyfikator elementu Spout** lub **identyfikator elementu Bolt** składnika, który zawiera błąd na liście. Na stronie szczegółów, który jest wyświetlany, dodatkowe błąd informacji znajduje się w **błędy** sekcji w dolnej części strony.

4. Aby uzyskać więcej informacji, wybierz **portu** z **Executors** części strony, aby wyświetlić dziennik procesów roboczych systemu Storm przez ostatnie kilka minut.

### <a name="errors-submitting-topologies"></a>Błędy przesyłanie topologii

Jeśli wystąpią błędy przesyłania topologii do HDInsight, możesz znaleźć dzienników dla składników po stronie serwera, które obsługują przesyłanie topologii w klastrze usługi HDInsight. Aby pobrać te dzienniki, użyj następującego polecenia z wiersza polecenia:

    scp sshuser@clustername-ssh.azurehdinsight.cn:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Zastąp __sshuser__ przy użyciu konta użytkownika SSH dla klastra. Zastąp __clustername__ o nazwie klastra HDInsight. Aby uzyskać więcej informacji na temat korzystania z `scp` i `ssh` z HDInsight, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Zgłoszenia może zakończyć się niepowodzeniem kilka przyczyn:

* Zestaw JDK nie jest zainstalowany lub nie znajduje się w ścieżce.
* Wymagane zależności języka Java nie są uwzględnione w przesyłania.
* Niezgodnych zależnościach.
* Zduplikowane nazwy topologii.

Jeśli `hdinsight-scpwebapi.out` dziennik zawiera `FileNotFoundException`, może to być spowodowane przez następujące warunki:

* Zestaw JDK nie znajduje się w ścieżce w środowisku programistycznym. Sprawdź, czy zestaw JDK został zainstalowany w środowisku deweloperskim, a `%JAVA_HOME%/bin` znajduje się w ścieżce.
* Brak zależności języka Java. Upewnij się, że łącznie z plikami JAR wymagane w ramach złożenia.

## <a name="next-steps"></a>Kolejne kroki

Na przykład przetwarzanie danych z usługi Event Hubs, zobacz [przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu systemu Storm w HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Na przykład topologii C#, który dzieli przesyłanie strumieniowe danych na wiele strumieni zobacz [przykład Storm języka C#](https://github.com/Blackmist/csharp-storm-example).

Aby dowiedzieć się więcej informacji o tworzeniu topologii języka C#, zobacz [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Inne sposoby pracy z HDInsight i Storm więcej przykładów HDInsight można znaleźć w następujących dokumentach:

**Microsoft SCP.NET**

* [Podręcznik programowania punkt połączenia usługi](apache-storm-scp-programming-guide.md)

**Storm Apache na HDInsight**

* [Wdrażanie i monitorowanie topologii za pomocą Storm Apache na HDInsight](apache-storm-deploy-monitor-topology.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](apache-storm-example-topology.md)

**Apache Hadoop w HDInsight**

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](../hadoop/hdinsight-use-hive.md)
* [Use Apache Pig z platformą Apache Hadoop w HDInsight](../hadoop/hdinsight-use-pig.md)
* [Apache Hadoop MapReduce za pomocą technologii Apache Hadoop w HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Bazy danych Apache HBase na HDInsight**

* [Wprowadzenie do bazy danych Apache HBase na HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
