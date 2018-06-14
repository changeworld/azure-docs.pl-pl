---
title: Topologii Apache Storm i Visual Studio C# - Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia topologii Storm w języku C#. Utworzyć topologię, liczba proste programu word w Visual Studio za pomocą narzędzia usługi Hadoop dla programu Visual Studio.
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
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 1e35d795fb65c837f7a4152920f701da5bf8f506
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421434"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Tworzenie topologii C# dla Apache Storm przy użyciu narzędzi Data Lake tools dla programu Visual Studio

Informacje o sposobie tworzenia topologii Storm C# za pomocą narzędzi usługi Azure Data Lake (Hadoop) dla programu Visual Studio. Ten dokument przeprowadzi Cię przez proces tworzenia projektu Storm w programie Visual Studio, testowanie go lokalnie i wdrażania go do systemu Apache Storm w klastrze usługi HDInsight Azure.

Możesz również Dowiedz się, jak tworzyć hybrydowe topologie, korzystających z języka C# i składniki Java.

> [!NOTE]
> Podczas czynności opisane w tym dokumencie zależą od środowiska projektowego systemu Windows w programie Visual Studio, skompilowany projekt może zostać przesłane do klastra z systemem Linux lub HDInsight opartych na systemie Windows. Topologie SCP.NET obsługują tylko opartych na systemie Linux klastrów utworzonych po 28 października 2016 roku.

Aby użyć topologii C# z klastrem opartych na systemie Linux, musisz zaktualizować pakiet Microsoft.SCP.Net.SDK NuGet używany w projekcie do wersji 0.10.0.6 lub nowszej. Wersja pakietu musi być również zgodna z wersją główną systemu Storm zainstalowanego w usłudze HDInsight.

| Wersja usługi HDInsight | Wersja platformy STORM | Wersja SCP.NET | Domyślna wersja Mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(tylko w usłudze HDInsight opartych na systemie Windows) | Nie dotyczy |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> Topologie C# w klastrach opartych na systemie Linux muszą korzystać z platformy .NET 4.5 i używać platformy Mono, aby mogły działać w klastrze usługi HDInsight. Sprawdź [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/) dla potencjalnych niezgodności.

## <a name="install-visual-studio"></a>Instalacja programu Visual Studio

Topologie języka C# z SCP.NET można tworzyć przy użyciu jednej z następujących wersji programu Visual Studio:

* Program Visual Studio 2012 z [aktualizacja 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 z [aktualizacja 4](http://www.microsoft.com/download/details.aspx?id=44921) lub [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)

* Program Visual Studio 2015 lub [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (dowolna wersja)

## <a name="install-data-lake-tools-for-visual-studio"></a>Zainstaluj Data Lake tools dla programu Visual Studio

Aby zainstalować usługi Data Lake tools dla programu Visual Studio, postępuj zgodnie z instrukcjami [rozpocząć korzystanie z usługi Data Lake tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalowanie języka Java

Podczas przesyłania topologii Storm z programu Visual Studio SCP.NET generuje plik zip, który zawiera topologii i zależności. Java służy do tworzenia tych plików zip, ponieważ używa ona formatu, który jest bardziej zgodne z opartą na systemie Linux klastrów.

1. Zainstaluj Java Developer Kit (JDK) 7 lub nowszego środowiska deweloperskiego. Możesz uzyskać JDK Oracle z [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Można również użyć [innych dystrybucje Java](http://openjdk.java.net/).

2. `JAVA_HOME` Zmiennej środowiskowej musi wskazywać katalog, który zawiera Java.

3. `PATH` Zmiennej środowiskowej musi zawierać `%JAVA_HOME%\bin` katalogu.

Aby zweryfikować, że Java i zestaw JDK są prawidłowo zainstalowane, można użyć następujących aplikacji konsolowej C#:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Szablony STORM

Narzędzia Data Lake dla programu Visual Studio zapewniają następujące szablony:

| Project type (Typ projektu) | Pokazuje |
| --- | --- |
| Aplikacja STORM |Pusty projekt topologii Storm. |
| Przykładowy składnik zapisywania usługi Azure SQL STORM |Jak można zapisać do bazy danych SQL Azure. |
| Przykładowe czytnika rozwiązania Cosmos Azure DB STORM |Jak można odczytać z bazy danych Azure rozwiązania Cosmos. |
| Przykładowy składnik zapisywania rozwiązania Cosmos Azure DB STORM |Jak napisać do bazy danych Azure rozwiązania Cosmos. |
| Przykładowe czytnika EventHub STORM |Jak można odczytać z usługi Azure Event Hubs. |
| Przykładowy składnik zapisywania EventHub STORM |Jak napisać usługi Azure Event hubs. |
| Przykładowe bazy danych HBase czytnika STORM |Jak można odczytać z bazy danych HBase w usłudze hdinsight. |
| Przykładowe bazy danych HBase zapisywania STORM |Sposób zapisywania do bazy danych HBase na klastrach usługi HDInsight. |
| Przykład hybrydowego STORM |Jak używać składnika Java. |
| Przykładowe STORM |Topologii liczby podstawowych programu word. |

> [!WARNING]
> Nie wszystkie szablony pracy z opartą na systemie Linux usługą HDInsight. Pakiety NuGet służące za pomocą szablonów nie może być zgodna z Mono. Sprawdź [Mono zgodności](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentów i użyj [.NET przenośność analizator](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) do identyfikowania potencjalnych problemów.

Instrukcje w tym dokumencie umożliwia Podstawowa aplikacja Storm projektu typu Tworzenie topologii.

### <a name="hbase-templates-notes"></a>Informacje o szablonach HBase

Szablony HBase i zapisu Użyj interfejsu API REST HBase, nie HBase API języka Java, do komunikowania się z bazą danych HBase w klastrze usługi HDInsight.

### <a name="eventhub-templates-notes"></a>Informacje o szablonach EventHub

> [!IMPORTANT]
> Składnik spout opartych na języku Java EventHub zawartych w szablonie czytnika EventHub nie mogą działać z Storm w usłudze HDInsight w wersji 3.5 lub nowszej. Zaktualizowana wersja tego składnika jest dostępna w [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Aby przykładową topologię, która używa tej składnika i współpracuje z systemu Storm w usłudze HDInsight 3.5, zobacz [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Tworzenie topologii C#

1. Otwórz program Visual Studio, wybierz **pliku** > **nowy**, a następnie wybierz **projektu**.

2. Z **nowy projekt** okna, rozwiń węzeł **zainstalowana** > **szablony**i wybierz **usługi Azure Data Lake**. Wybierz z listy szablonów **aplikacji Storm**. W dolnej części ekranu, wprowadź **WordCount** jako nazwę aplikacji.

    ![Zrzut ekranu nowego projektu okna](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Po utworzeniu projektu, musisz mieć następujące pliki:

   * **Plik program.cs**: plik definiuje topologii dla projektu. Domyślnie zostanie utworzona domyślną topologię, która składa się z jednego spout i jeden bolt.

   * **Spout.cs**: spout przykład, który emituje liczb losowych.

   * **Bolt.cs**: przykład bolt, który śledzi liczbę elementów emitowane przez elementy spout.

     Podczas tworzenia projektu NuGet pobierze najnowszą [pakietu SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementowanie spout

1. Otwórz **Spout.cs**. Elementach spout są używane do odczytywania danych w topologii z zewnętrznego źródła. Główne składniki spout są:

   * **NextTuple**: wywoływane przez Storm, gdy elementy spout może wyemitować krotek nowe.

   * **Potwierdzenia** (tylko w przypadku topologii transakcyjnych): obsługuje inicjowane przez inne składniki w topologii krotek wysyłane z spout potwierdzeń. Potwierdzeniem krotka umożliwia spout wiedzieć, że jej został pomyślnie przetworzony przez składniki podrzędne.

   * **Niepowodzenie** (tylko w przypadku topologii transakcyjnych): obsługuje krotek, które są niepowodzenie przetwarzania innych składników w topologii. Implementacja metody błędów pozwala na ponowne Emituj spójnej kolekcji, dzięki czemu mogą być przetwarzane ponownie.

2. Zastąp zawartość **Spout** klasy z następującym tekstem: ten spout losowo emituje zdanie w topologii.

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

### <a name="implement-the-bolts"></a>Implementowanie elementów bolt

1. Usuń istniejącą **Bolt.cs** plik z projektu.

2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Dodaj** > **nowy element**. Wybierz z listy, **Storm Bolt**, a następnie wprowadź **Splitter.cs** jako nazwy. Powtórz ten proces, aby utworzyć drugi bolt o nazwie **Counter.cs**.

   * **Splitter.cs**: implementuje elementu bolt, który dzieli zdania na poszczególnych wyrazów i emituje nowego strumienia słów.

   * **Counter.cs**: implementuje elementu bolt, który zlicza każdego wyrazu i emituje nowego strumienia słów i liczby dla każdego wyrazu.

     > [!NOTE]
     > Tych elementów bolt odczytu i zapisu do strumieni, ale umożliwia także bolt do komunikowania się ze źródeł, takich jak bazy danych lub usługi.

3. Otwórz **Splitter.cs**. Domyślnie ma tylko jedną metodę: **Execute**. Metody Execute jest wywoływane, gdy elementy bolt odbiera tuple do przetwarzania. W tym miejscu możesz przeczytać i przetwarzania przychodzących krotek i Emituj krotek wychodzących.

4. Zastąp zawartość **podziału** klasy następującym kodem:

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

5. Otwórz **Counter.cs**i Zastąp zawartość klasy następującym kodem:

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

Elementach spout i elementów bolt są rozmieszczone na wykresie, która określa, jak dane przepływają między składnikami. Ta topologia wykres jest w następujący sposób:

![Diagram przedstawiający sposób rozmieszczenia elementów](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Zdania są emitowane z spout i są dystrybuowane do wystąpień bolt podziału. Bolt podziału dzieli zdań w wyrazy, które są dystrybuowane do bolt licznika.

Ponieważ wyrazów jest przechowywanych lokalnie w wystąpieniu licznika, chcesz upewnij się, że określone wyrazy przepływać do tego samego wystąpienia licznika bolt. Każde wystąpienie przechowuje informacje o słów. Ponieważ bolt podziału przechowuje bez stanu, naprawdę nie ma znaczenia, którego wystąpienia rozdzielacza otrzymuje zdanie, które.

Otwórz **Program.cs**. Metoda ważne jest **GetTopologyBuilder**, które jest używane do definiowania topologia, z której zostało przesłane do systemu Storm. Zastąp zawartość **GetTopologyBuilder** z następujący kod do wdrożenia topologii opisanych powyżej:

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

## <a name="submit-the-topology"></a>Przedstawia topologię

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **przesyłania do systemu Storm w usłudze HDInsight**.

   > [!NOTE]
   > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do tego, który zawiera Storm w klastrze usługi HDInsight.

2. Wybierz Storm w klastrze usługi HDInsight z **klaster Storm** listy rozwijanej, a następnie wybierz **przesyłania**. Można monitorować, jeśli przesyłanie zakończy się pomyślnie, za pomocą **dane wyjściowe** okna.

3. Gdy topologia zostało pomyślnie przesłane, **topologii Storm** dla klastra powinna zostać wyświetlona. Wybierz **WordCount** topologii z listy, aby wyświetlić informacje o uruchomionej topologii.

   > [!NOTE]
   > Można również wyświetlić **topologii Storm** z **Eksploratora serwera**. Rozwiń węzeł **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy klaster Storm w usłudze HDInsight, a następnie wybierz **topologii Storm widoku**.

    Aby wyświetlić informacje o składnikach topologii, kliknij dwukrotnie składnik na diagramie.

4. Z **podsumowanie topologii** wyświetlić, kliknij przycisk **Kill** przestanie topologii.

   > [!NOTE]
   > Topologii STORM nadal uruchamiać, dopóki nie zostaną one wyłączone lub usunąć klastra.

## <a name="transactional-topology"></a>Topologia transakcyjne

Poprzednie topologia jest nietransakcyjnej. Składniki w topologii nie implementuje funkcje odtwarzanie wiadomości. Na przykład transakcyjne topologii Utwórz projekt i wybierz **próbki Storm** jako typ projektu.

Topologie transakcyjne zaimplementować następujące polecenie, aby obsługiwać powtarzania danych:

* **Buforowanie metadanych**: spout muszą być przechowywane metadane na temat danych emitowanych, dzięki czemu dane można je pobrać i wysyłanego ponownie, jeśli wystąpi błąd. Ponieważ danych emitowanych przez próbki jest mały, danych pierwotnych dla każdej krotki znajduje się w słowniku powtarzania.

* **Potwierdzenia**: każdego bolt topologii można wywołać `this.ctx.Ack(tuple)` potwierdzić, że pomyślnie przetworzył spójnych kolekcji. Gdy wszystkie elementów bolt prześledzone spójnej kolekcji, `Ack` wywoływana jest metoda spout. `Ack` Metoda pozwala spout do usunięcia danych, który był buforowany powtarzania.

* **Niepowodzenie**: każdego bolt można wywołać `this.ctx.Fail(tuple)` wskazująca, że przetwarzanie nie powiodło się dla spójnych kolekcji. Błąd propaguje do `Fail` metody spout, gdzie można odtworzyć spójna kolekcja znajdująca się przy użyciu buforowanych metadanych.

* **Identyfikator sekwencji**: podczas emitowania krotka, można określić identyfikator unikatowy ciąg. Ta wartość określa spójnej kolekcji dla przetwarzania powtórzeń (Ack i błędów). Na przykład spout w **próbki Storm** projektu są używane następujące podczas emitowania danych:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Ten kod emituje spójnych kolekcji zawierający zdania do domyślny strumień, z identyfikatorem sekwencji zawartych w **lastSeqId**. Na przykład **lastSeqId** jest zwiększany dla każdego krotki wysyłanego.

Jak pokazano w **próbki Storm** projektu, czy składnik jest transakcyjna można ustawić w czasie wykonywania na podstawie konfiguracji.

## <a name="hybrid-topology-with-c-and-java"></a>Hybrydowych topologii C# i Java

Data Lake tools dla Visual Studio umożliwia również tworzyć hybrydowe topologie, gdzie są niektóre składniki C#, a niektóre Java.

Na przykład topologii hybrydowego Utwórz projekt i wybierz **przykład hybrydowego Storm**. Ten typ przykładowych pokazano następujące kwestie:

* **Java spout** i **C# bolt**: zdefiniowanych w **HybridTopology_javaSpout_csharpBolt**.

    * Transakcyjne wersja jest zdefiniowany w **HybridTopologyTx_javaSpout_csharpBolt**.

* **C# spout** i **Java bolt**: zdefiniowanych w **HybridTopology_csharpSpout_javaBolt**.

    * Transakcyjne wersja jest zdefiniowany w **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Ta wersja także przedstawiono sposób użycia Clojure kodu z pliku tekstowego jako składnik Java.


Aby przełączyć topologię, która jest używana po przesłaniu projektu, przenieść `[Active(true)]` instrukcji topologii, którego chcesz użyć, przed przesłaniem go do klastra.

> [!NOTE]
> Wszystkie pliki języka Java, które są wymagane są dostarczane jako część tego projektu w **JavaDependency** folderu.

Podczas tworzenia i przesyłania topologii hybrydowe, należy wziąć pod uwagę następujące:

* Użyj **JavaComponentConstructor** można utworzyć wystąpienia klasy Java dla spout lub elementów bolt.

* Użyj **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** do serializowania danych do lub wychodzący składniki Java z obiektów języka Java na format JSON.

* Podczas przesyłania topologii do serwera, należy użyć **dodatkowe konfiguracje** opcję, aby określić **ścieżki plików Java**. Określona ścieżka powinna być katalog zawierający pliki JAR, które zawierają klas Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Wersja SCP.NET 0.9.4.203 wprowadza nowe klasy i metody specjalnie z myślą o pracy z spout Centrum zdarzeń (Java spout, służącą do odczytywania z usługi Event Hubs). Podczas tworzenia topologię, która używa spout Centrum zdarzeń, należy użyć następujących metod:

* **EventHubSpoutConfig** klasy: tworzy obiekt zawierający konfigurację dla składnika spout.

* **TopologyBuilder.SetEventHubSpout** metody: dodaje składnik spout Centrum zdarzeń do topologii.

> [!NOTE]
> Możesz nadal korzystać **CustomizedInteropJSONSerializer** do serializowania danych produkowane przez elementy spout.

## <a id="configurationmanager"></a>Użyj program Configuration Manager

Nie używaj **ConfigurationManager** można pobrać wartości konfiguracji z bolt i spout składników. W ten sposób może spowodować wyjątek pustego wskaźnika. Zamiast tego konfiguracji projektu jest przekazywany do topologii Storm jako pary kluczy i wartości w kontekście topologii. Każdego składnika, który korzysta z wartości konfiguracji należy pobrać je z kontekstu podczas inicjowania.

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

Jeśli używasz `Get` metodę, aby zwrócić wystąpienia składnika, należy upewnić się, że przekazuje zarówno `Context` i `Dictionary<string, Object>` parametrów konstruktora. Poniższy przykład jest podstawowy `Get` metodę, która poprawnie przekazuje te wartości:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak zaktualizować SCP.NET

Najnowsze wersje SCP.NET obsługuje uaktualnienie pakietu przez pakiet NuGet. Dostępna jest nowa aktualizacja, pojawi się powiadomienie uaktualnienia. Aby ręcznie sprawdzić uaktualnienia, wykonaj następujące kroki:

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet**.

2. Wybierz z Menedżera pakietów, **aktualizacje**. Czy jest dostępna aktualizacja, są one dostępne. Kliknij przycisk **aktualizacji** dla pakietu go zainstalować.

> [!IMPORTANT]
> Jeśli projekt został utworzony przy użyciu starszej wersji SCP.NET, który nie używa NuGet, należy wykonać następujące kroki, aby zaktualizować do nowszej wersji:
>
> 1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet**.
> 2. Przy użyciu **wyszukiwania** pola, Wyszukaj, a następnie dodaj **Microsoft.SCP.Net.SDK** do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Rozwiązywanie typowych problemów z topologii

### <a name="null-pointer-exceptions"></a>Wyjątki wskaźnika o wartości null

Gdy używasz topologii C# z klastrem usługi HDInsight opartej na systemie Linux, blokowania i spout składników, które używają **ConfigurationManager** można odczytać ustawień konfiguracji w czasie wykonywania mogą zwracać wyjątki wskaźnika o wartości null.

Konfiguracja dla projektu jest przekazywany do topologii Storm jako pary kluczy i wartości w kontekście topologii. Można go pobrać z obiekt słownika, który jest przekazywany do składników programu, gdy są one inicjowane.

Aby uzyskać więcej informacji, zobacz [ConfigurationManager](#configurationmanager) sekcji tego dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Używając topologii C# z klastrem usługi HDInsight opartej na systemie Linux, może wystąpić następujący błąd:

    System.TypeLoadException: Failure has occurred while loading a type.

Ten błąd występuje podczas używania pliku binarnego, który jest niezgodny z wersją programu .NET, który obsługuje Mono.

W przypadku klastrów usługi HDInsight opartej na systemie Linux upewnij się, że projekt korzysta z plików binarnych skompilowanego dla platformy .NET 4.5.

### <a name="test-a-topology-locally"></a>Testowanie topologii lokalnie

Chociaż jest to łatwa do wdrożenia topologii do klastra, w niektórych przypadkach może być konieczne test topologii lokalnie. Wykonaj następujące kroki, aby uruchomić i przetestować przykładową topologię w tym samouczku lokalnie w środowisku projektowania.

> [!WARNING]
> Testowanie lokalnym działa tylko dla basic, C# — tylko topologii. Nie można użyć lokalnego testowanie pod kątem hybrydowe topologie lub topologie, wykorzystujące wiele strumieni.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **właściwości**. We właściwościach projektu, należy zmienić **Output typu** do **aplikacji konsoli**.

    ![Zrzut ekranu przedstawiający właściwości projektu z wyróżnionym typem danych wyjściowych](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Pamiętaj, aby zmienić **Output typu** do **biblioteki klas** przed wdrożeniem topologii do klastra.

2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Dodaj** > **nowy element**. Wybierz **klasy**, a następnie wprowadź **LocalTest.cs** jak nazwa klasy. Na koniec kliknij **Dodaj**.

3. Otwórz **LocalTest.cs**i dodaj następującą **przy użyciu** instrukcji u góry:

    ```csharp
    using Microsoft.SCP;
    ```

4. Użyj następującego kodu jako zawartość **LocalTest** klasy:

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

    Poświęć chwilę, aby zapoznaj się z artykułem komentarze w kodzie. Ten kod zawiera **LocalContext** do uruchamiania w środowisku programistycznym, a składniki będzie się powtarzał strumienia danych między składnikami w plikach tekstowych na dysku lokalnym.

1. Otwórz **Program.cs**i Dodaj następujący kod do **Main** metody:

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

2. Zapisz zmiany, a następnie kliknij przycisk **F5** lub wybierz **debugowania** > **Rozpocznij debugowanie** do uruchamiania projektu. Okno konsoli powinna wyświetlane i rejestrowania stanu postęp testów. Gdy **testy Zakończono** pojawia się, naciśnij dowolny klawisz, aby zamknąć okno.

3. Użyj **Eksploratora Windows** można zlokalizować katalogu, który zawiera projekt. Na przykład: **C:\Users\<your_user_name > \Documents\Visual 2013\Projects\WordCount\WordCount w Studio**. W tym katalogu, otwórz **Bin**, a następnie kliknij przycisk **debugowania**. Powinny pojawić się plikami tekstowymi, które zostały utworzone podczas testów: sentences.txt, counter.txt i splitter.txt. Otwórz każdy plik tekstowy i sprawdzać dane.

   > [!NOTE]
   > Ciąg danych będzie nadal występował jako tablica wartości dziesiętnych w tych plikach. Na przykład \[[97,103,111]] w **splitter.txt** plik jest słowo *i*.

> [!NOTE]
> Należy ustawić **typ projektu** do **biblioteki klas** przed wdrożeniem w Storm w klastrze usługi HDInsight.

### <a name="log-information"></a>Informacje w Dzienniku

Można łatwo rejestrowanie informacji z składniki topologii sieci, za pomocą `Context.Logger`. Na przykład następujące polecenie tworzy wpis informacyjny w dzienniku:

```csharp
Context.Logger.Info("Component started");
```

Zarejestrowane informacje mogą być wyświetlane z **dziennik usługi Hadoop**, który znajduje się w **Eksploratora serwera**. Rozwiń pozycję dla Storm w klastrze usługi HDInsight, a następnie rozwiń **dziennik usługi Hadoop**. Na koniec wybierz plik dziennika, aby wyświetlić.

> [!NOTE]
> Dzienniki są przechowywane na koncie magazynu Azure, który jest używany przez klaster. Aby wyświetlić dzienniki w programie Visual Studio, możesz zalogować się do subskrypcji platformy Azure, który jest właścicielem konta magazynu.

### <a name="view-error-information"></a>Wyświetl informacje o błędzie

Aby wyświetlić błędy, które wystąpiły w uruchomionej topologii, użyj następujących kroków:

1. Z **Eksploratora serwera**, kliknij prawym przyciskiem myszy klaster Storm w usłudze HDInsight i wybierz **topologii Storm widoku**.

2. Aby uzyskać **Spout** i **Bolts**, **ostatniego błędu** kolumna zawiera informacje o ostatnim błędzie.

3. Wybierz **Spout identyfikator** lub **identyfikator Bolt** składnika, który zawiera błąd na liście. Na stronie Szczegóły błędu wyświetlanych, dodatkowe informacje są wymienione w **błędy** sekcji u dołu strony.

4. Aby uzyskać więcej informacji, wybierz **portu** z **modułów** części strony, aby wyświetlić dziennik procesu roboczego Storm ostatnich kilka minut.

### <a name="errors-submitting-topologies"></a>Błędy przesyłania topologii

Jeśli wystąpią błędy przesyłania topologii do usługi HDInsight można znaleźć dzienniki dla składników po stronie serwera, które obsługi przesyłania topologii w klastrze usługi HDInsight. Aby pobrać te dzienniki, użyj następującego polecenia w wierszu polecenia:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Zastąp __sshuser__ przy użyciu konta użytkownika SSH dla klastra. Zastąp __clustername__ z nazwą klastra usługi HDInsight. Aby uzyskać więcej informacji na temat używania `scp` i `ssh` z usługą HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Przesyłanie może zakończyć się niepowodzeniem kilka przyczyn:

* JDK nie jest zainstalowany lub nie znajduje się w ścieżce.
* Wymagane zależności Java nie znajdują się w przesyłania.
* Niezgodne zależności.
* Zduplikowane nazwy topologii.

Jeśli `hdinsight-scpwebapi.out` dziennik zawiera `FileNotFoundException`, może to być spowodowane przez następujące warunki:

* Zestaw JDK, który nie jest w ścieżce na środowisko deweloperskie. Sprawdź, czy w środowisku programistycznym, który jest zainstalowany zestaw JDK `%JAVA_HOME%/bin` znajduje się w ścieżce.
* Brak zależności Java. Upewnij się, że łącznie z plikami JAR wymaganych w ramach złożenia.

## <a name="next-steps"></a>Kolejne kroki

Na przykład przetwarzania danych z usługi Event Hubs, zobacz [przetwarzać zdarzeń z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Na przykład topologii C#, która dzieli strumienia danych na wiele strumieni zobacz [przykład C# Storm](https://github.com/Blackmist/csharp-storm-example).

Aby dowiedzieć się więcej informacji o tworzeniu topologie języka C#, zobacz [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Inne sposoby pracy z usługą HDInsight i więcej Storm na próbkach HDInsight można znaleźć w następujących dokumentach:

**Microsoft SCP.NET**

* [Podręcznik programowania punkt połączenia usługi](apache-storm-scp-programming-guide.md)

**Apache Storm w usłudze HDInsight**

* [Wdrażanie i monitorowanie topologii z systemu Apache Storm w usłudze HDInsight](apache-storm-deploy-monitor-topology.md)
* [Przykładowe topologie dla systemu Storm w usłudze HDInsight](apache-storm-example-topology.md)

**Apache Hadoop w usłudze HDInsight**

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](../hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](../hadoop/hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Bazy danych Apache HBase w usłudze HDInsight**

* [Wprowadzenie do korzystania z bazy danych HBase w usłudze HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
