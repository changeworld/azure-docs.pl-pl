---
title: Apache Storm topologie z Visual Studio i C# — Azure HDInsight
description: Dowiedz się, jak utworzyć topologie Burzy w języku C#. Tworzenie topologii liczby wyrazów w programie Visual Studio przy użyciu narzędzi Hadoop dla programu Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612295"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Tworzenie topologii języka C# dla usługi Apache Storm przy użyciu narzędzi usługi Data Lake dla programu Visual Studio

Dowiedz się, jak utworzyć topologię usługi Apache Storm języka C# przy użyciu narzędzi usługi Azure Data Lake (Apache Hadoop) dla programu Visual Studio. Ten dokument przechodzi przez proces tworzenia projektu Storm w programie Visual Studio, testowania go lokalnie i wdrażania go w apache Storm w klastrze usługi Azure HDInsight.

Dowiesz się również, jak tworzyć topologie hybrydowe, które używają składników C# i Java.

Topologie języka C# używają platformy .NET 4.5 i używają mono do uruchamiania w klastrze HDInsight. Aby uzyskać informacje o potencjalnych niezgodnościach, zobacz [Zgodność mono](https://www.mono-project.com/docs/about-mono/compatibility/). Aby użyć topologii języka C#, `Microsoft.SCP.Net.SDK` należy zaktualizować pakiet NuGet używany przez projekt do wersji 0.10.0.6 lub nowszej. Wersja pakietu musi być również zgodna z wersją główną systemu Storm zainstalowanego w usłudze HDInsight.

| Wersja HDInsight | Wersja Apache Storm | SCP.NET wersja | Domyślna wersja mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Wymagania wstępne

Klaster Burzy Apache w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Burza** dla **typu klastra**.

## <a name="install-visual-studio"></a>Instalacja programu Visual Studio

Topologie języka C# można tworzyć za pomocą SCP.NET przy użyciu [programu Visual Studio.](https://visualstudio.microsoft.com/downloads/) Instrukcje w tym miejscu używać visual studio 2019, ale można również użyć wcześniejszych wersji programu Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalowanie narzędzi usługi Data Lake dla programu Visual Studio

Aby zainstalować narzędzia usługi Data Lake dla programu Visual Studio, wykonaj kroki opisane w [programie Wprowadzenie do korzystania z narzędzi usługi Data Lake dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Instalowanie oprogramowania Java

Po przesłaniu topologii storm z programu Visual Studio SCP.NET generuje plik zip, który zawiera topologię i zależności. Java jest używany do tworzenia tych plików zip, ponieważ używa formatu, który jest bardziej kompatybilny z klastrów opartych na Linuksie.

1. Zainstaluj zestaw Java Developer Kit (JDK) 7 lub nowszym w środowisku programistycznym. Możesz uzyskać Oracle JDK od [Oracle](https://openjdk.java.net/). Można również użyć [innych dystrybucji Java](/java/azure/jdk/).

2. Ustaw `JAVA_HOME` zmienną środowiskową na katalog zawierający język Java.

3. Ustaw `PATH` zmienną środowiskową `%JAVA_HOME%\bin` tak, aby zawierała katalog.

Można utworzyć i uruchomić następującą aplikację konsoli języka C#, aby sprawdzić, czy java i JDK są poprawnie zainstalowane:

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

## <a name="apache-storm-templates"></a>Szablony Burzanych Apache

Narzędzia usługi Data Lake dla programu Visual Studio zawierają następujące szablony:

| Project type (Typ projektu) | Demonstracje |
| --- | --- |
| Aplikacja Storm |Pusty projekt topologii burzy. |
| Storm Azure SQL Writer Sample |Jak pisać do usługi Azure SQL Database. |
| Storm Azure Cosmos DB Reader Sample |Jak czytać z usługi Azure Cosmos DB. |
| Storm Azure Cosmos DB Writer Sample |Jak pisać do usługi Azure Cosmos DB. |
| Przykład czytnika zdarzeń storm |Jak czytać z usługi Azure Event Hubs. |
| Przykład modułu zapisującego storm EventHub |Jak pisać w centrum zdarzeń platformy Azure. |
| Storm HBase Reader Przykład |Jak czytać z HBase na klastrach HDInsight. |
| Storm HBase Writer Przykład |Jak zapisywać do bazy danych HBase w klastrach HDInsight. |
| Próbka hybrydowa storm |Jak korzystać ze składnika Java. |
| Próbka burzy |Podstawowa topologia liczby słów. |

> [!WARNING]  
> Nie wszystkie szablony działają z systemem HDInsight opartym na systemie Linux. Pakiety NuGet używane przez szablony mogą nie być zgodne z mono. Aby zidentyfikować potencjalne problemy, zobacz [Zgodność mono](https://www.mono-project.com/docs/about-mono/compatibility/) i użyj [analizatora przenośności .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

W krokach w tym dokumencie służy podstawowy typ projektu aplikacji storm do utworzenia topologii.

### <a name="apache-hbase-templates"></a>Szablony Apache HBase

Szablony czytnika i modułu zapisującego bazy danych HBase używają interfejsu API HBase REST, a nie interfejsu API języka Java HBase, do komunikowania się z bazą danych HBase w klastrze HDInsight.

### <a name="eventhub-templates"></a>Szablony EventHub

> [!IMPORTANT]  
> Składnik wylewki EventHub oparty na języku Java dołączony do szablonu programu EventHub Reader może nie działać z usługą Storm w wersji 3.5 lub nowszej. Zaktualizowana wersja tego składnika jest dostępna w [usłudze GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Przykładowa topologia, która używa tego składnika i współpracuje z programem Storm w programie HDInsight 3.5, zobacz [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Tworzenie topologii języka C#

Aby utworzyć projekt topologii języka C# w programie Visual Studio:

1. Otwórz program Visual Studio.

1. W oknie **Start** wybierz pozycję **Utwórz nowy projekt**.

1. W oknie **Utwórz nowy projekt** przewiń do i wybierz **pozycję Aplikacja burzowa**, a następnie wybierz pozycję **Dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę programu Project** w programie *WordCount*, przejdź do lub utwórz ścieżkę katalogu **lokalizacji** dla projektu, a następnie wybierz pozycję **Utwórz**.

    ![Aplikacja Storm, Konfigurowanie nowego okna dialogowego projektu, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Po utworzeniu projektu powinny być wyświetlane następujące pliki:

* *Program.cs*: Definicja topologii dla projektu. Domyślna topologia, która składa się z jednego wylewki i jednej śruby, jest tworzona domyślnie.

* *Spout.cs*: przykładowa wylewka emitujaliczne liczby.

* *Bolt.cs:* przykładowa śruba, która utrzymuje liczbę liczb emitowanych przez wylewkę.

Podczas tworzenia projektu, NuGet pobiera najnowszy [pakiet SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Zaimplementuj wylewka

Następnie dodaj kod wylewki, który jest używany do odczytywania danych w topologii ze źródła zewnętrznego. Ten wylewka losowo emituje zdanie do topologii.

1. Otwórz *Spout.cs*. Główne składniki wylewki to:

   * `NextTuple`: Wywoływane przez burzę, gdy wylewka może emitować nowe krotki.

   * `Ack`(tylko topologia transakcyjna): Uchwyty potwierdzeń uruchamiane przez inne składniki w topologii dla krotek wysyłanych z dziobka. Potwierdzanie krotki pozwala wylewka wiedzieć, że został pomyślnie przetworzony przez składniki podrzędne.

   * `Fail`(tylko topologia transakcyjna): Uchwyty krotek, które nie przetwarzają innych składników w topologii. Implementowanie `Fail` metody umożliwia ponowne emitowanie krotki, dzięki czemu można go ponownie przetworzyć.

2. Zastąp `Spout` zawartość klasy następującym tekstem:

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

### <a name="implement-the-bolts"></a>Zaimplementuj śruby

Teraz utwórz dwie śruby Storm w tym przykładzie:

1. Usuń istniejący plik *Bolt.cs* z projektu.

2. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **nowy element**. Z listy wybierz **storm bolt**i wprowadź *Splitter.cs* jako nazwę. W kodzie nowego pliku zmień nazwę obszaru `WordCount`nazw na . Następnie powtórz ten proces, aby utworzyć drugą śrubę o nazwie *Counter.cs*.

   * *Splitter.cs*: Implementuje śrubę, która dzieli zdania na poszczególne wyrazy i emituje nowy strumień słów.

   * *Counter.cs:* Implementuje śrubę, która zlicza każde słowo i emituje nowy strumień słów i liczbę dla każdego wyrazu.

     > [!NOTE]  
     > Te śruby odczytywania i zapisu do strumieni, ale można również użyć śruby do komunikowania się ze źródłami, takimi jak baza danych lub usługa.

3. Otwórz *Splitter.cs*. Domyślnie ma tylko jedną `Execute`metodę: . Metoda `Execute` jest wywoływana, gdy śruba odbiera krotki do przetwarzania. W tym miejscu można odczytywać i przetwarzać krotek przychodzących i emitować krotek wychodzących.

4. Zastąp `Splitter` zawartość klasy następującym kodem:

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

5. Otwórz *Counter.cs*i zastąp zawartość klasy następującym kodem:

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

### <a name="define-the-topology"></a>Definiowanie topologii

Wylewki i śruby są rozmieszczone na wykresie, który określa sposób przepływu danych między komponentami. W przypadku tej topologii wykres jest następujący:

![Diagram rozmieszczenia wylewki i komponentów śrubowych, topologia burzy](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Dziobek emituje zdania, które są dystrybuowane do wystąpień śruby Rozdzielacz. Śruba rozdzielacza dzieli zdania na słowa, które są rozdzielane do śruby Licznik.

Ponieważ Counter wystąpienie przechowuje liczbę wyrazów lokalnie, chcesz upewnić się, że określone wyrazy przepływu do tego samego counter wystąpienia śruby. Każde wystąpienie śledzi określone słowa. Ponieważ śruba Rozdzielacz nie utrzymuje żadnego stanu, to naprawdę nie ma znaczenia, które wystąpienie rozdzielacza odbiera zdanie.

Otwórz *Program.cs*. Ważną metodą `GetTopologyBuilder`jest , który jest używany do definiowania topologii, która jest przesyłana do Storm. Zastąp `GetTopologyBuilder` zawartość następującego kodu, aby zaimplementować topologię opisaną wcześniej:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

## <a name="submit-the-topology"></a>Prześlij topologię

Teraz możesz przesłać topologię do klastra HDInsight.

1. Przejdź do **programu View** > **Server Explorer**.

1. Kliknij prawym przyciskiem myszy **pozycję Azure**, wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure...** i ukończ proces logowania.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Prześlij do burzy w programie HDInsight**.

1. W oknie dialogowym **Przesyłanie topologii** w obszarze listy rozwijanej **Klaster burzowy** wybierz klaster Burza w programie HDInsight, a następnie wybierz pozycję **Prześlij**. Można sprawdzić, czy przesyłanie zakończyło się pomyślnie, przeglądając okienko **Dane wyjściowe.**

    Po pomyślnym przesłaniu topologii powinno pojawić się okno **Widok topologii burzy** dla klastra. Wybierz topologię **wordcount** z listy, aby wyświetlić informacje o topologii uruchamiania.

    ![Okno widoku topologii burzy, klaster HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Topologie **burzy** można również wyświetlić w **Eksploratorze serwera**. Rozwiń pozycję **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy grupę Burza w klastrze HDInsight, a następnie wybierz polecenie **Wyświetl topologie burzy**.

    Aby wyświetlić informacje o komponentach w topologii, wybierz komponent na diagramie.

1. W sekcji **Podsumowanie topologii** wybierz pozycję **Zabij,** aby zatrzymać topologię.

    > [!NOTE]  
    > Topologie burzowe będą nadal działać, dopóki nie zostaną dezaktywowane lub klaster zostanie usunięty.

## <a name="transactional-topology"></a>Topologia transakcyjna

Poprzednia topologia jest nietransakcyjna. Składniki w topologii nie implementują funkcji do odtwarzania komunikatów. Na przykład topologii transakcyjnej utwórz projekt i wybierz **próbkę burzy** jako typ projektu.

Topologie transakcyjne implementują następujące elementy do obsługi odtwarzania danych:

* **Buforowanie metadanych:** wylewka musi przechowywać metadane dotyczące emitowanych danych, aby dane mogły zostać pobrane i wyemitowane ponownie w przypadku wystąpienia błędu. Ponieważ dane emitowane przez próbkę jest mały, nieprzetworzone dane dla każdej krotki są przechowywane w słowniku do odtworzenia.

* **Ack:** Każda śruba w `this.ctx.Ack(tuple)` topologii można wywołać, aby potwierdzić, że pomyślnie przetworzone krotki. Gdy wszystkie śruby zostały potwierdzone krotki, `Ack` wywoływana jest metoda wylewki. Metoda `Ack` umożliwia wylewka, aby usunąć dane, które zostały zapisane w pamięci podręcznej do powtórki.

* **Nie powiedzie się:** Każda śruba może wywołać, `this.ctx.Fail(tuple)` aby wskazać, że przetwarzanie nie powiodło się dla krotki. Błąd jest propagowany `Fail` do metody wylewki, gdzie krotka może być odtwarzane przy użyciu buforowanych metadanych.

* **Identyfikator sekwencji:** Podczas emitowania krotki można określić unikatowy identyfikator sekwencji. Ta wartość identyfikuje krotki do`Ack` `Fail`powtarzania ( i ) przetwarzania. Na przykład wylewka w projekcie **Burza przykładowa** używa następującego wywołania metody podczas emitowania danych:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Ten kod emituje krotek, który zawiera zdanie do strumienia domyślnego, `lastSeqId`z wartością identyfikator sekwencji zawarte w . W tym `lastSeqId` przykładzie jest zwiększany dla każdej krotki emitowanej.

Jak pokazano w projekcie **Próbkowanie burzy,** czy składnik jest transakcyjny można ustawić w czasie wykonywania, na podstawie konfiguracji.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia hybrydowa z językami C# i Java

Można również użyć narzędzia usługi Data Lake dla programu Visual Studio do tworzenia topologii hybrydowych, gdzie niektóre składniki są C# i inne są Java.

Na przykład topologii hybrydowej utwórz projekt i wybierz przykład **hybrydowy storm**. Ten typ przykładu pokazuje następujące pojęcia:

* **Wylewka Java** i **śruba C#:** Zdefiniowane w `HybridTopology_javaSpout_csharpBolt` klasie.

  Wersja transakcyjna jest `HybridTopologyTx_javaSpout_csharpBolt` zdefiniowana w klasie.

* **Wylewka C#** i **śruba Java:** Zdefiniowana w `HybridTopology_csharpSpout_javaBolt` klasie.

  Wersja transakcyjna jest `HybridTopologyTx_csharpSpout_javaBolt` zdefiniowana w klasie.

  > [!NOTE]  
  > Ta wersja pokazuje również, jak używać kodu Clojure z pliku tekstowego jako składnika Java.

Aby przełączyć topologię używaną podczas przesyłania `[Active(true)]` projektu, przesuń instrukcję do topologii, której chcesz użyć, przed przesłaniem jej do klastra.

> [!NOTE]  
> Wszystkie wymagane pliki Java są dostarczane w ramach tego projektu w folderze *JavaDependency.*

Podczas tworzenia i przesyłania topologii hybrydowej należy wziąć pod uwagę następujące kwestie:

* Służy `JavaComponentConstructor` do tworzenia wystąpienia klasy Java dla wylewki lub śruby.

* Służy `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` do serializacji danych do lub ze składników Java z obiektów Java do JSON.

* Podczas przesyłania topologii do serwera należy użyć opcji **Dodatkowe konfiguracje,** aby określić **ścieżki plików Java**. Określona ścieżka powinna być katalogiem zawierającym pliki JAR zawierające klasy Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET wersji 0.9.4.203 wprowadza nową klasę i metodę specjalnie do pracy z wylewką Centrum zdarzeń (wylewką Java, która odczytuje z centrów zdarzeń). Podczas tworzenia topologii korzystającej z wylewki Centrum zdarzeń (na przykład przy użyciu **przykładowego szablonu storm eventhub reader),** użyj następujących interfejsów API:

* `EventHubSpoutConfig`class: Tworzy obiekt zawierający konfigurację składnika dziobka.

* `TopologyBuilder.SetEventHubSpout`metoda: Dodaje składnik wylewki centrum zdarzeń do topologii.

> [!NOTE]  
> Nadal należy użyć `CustomizedInteropJSONSerializer` do serializacji danych produkowanych przez wylewka.

## <a name="use-configurationmanager"></a>Użyj funkcji ConfigurationManager

Nie **używaj ConfigurationManager** do pobierania wartości konfiguracji ze składników śruby i wylewki. Może to spowodować wyjątek wskaźnika null. Zamiast tego należy przekazać konfigurację projektu do topologii burzy jako pary kluczy i wartości w kontekście topologii. Każdy składnik, który opiera się na wartości konfiguracji musi pobrać je z kontekstu podczas inicjowania.

Poniższy kod pokazuje, jak pobrać te wartości:

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
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Jeśli używasz `Get` metody do zwrócenia wystąpienia składnika, należy upewnić `Context` `Dictionary<string, Object>` się, że przekazuje zarówno i parametry do konstruktora. Poniższy przykład jest `Get` podstawową metodą, która prawidłowo przekazuje te wartości:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak zaktualizować SCP.NET

Najnowsze wersje uaktualnienia pakietu pomocy technicznej SCP.NET za pośrednictwem nuget. Gdy nowa aktualizacja jest dostępna, otrzymasz powiadomienie o uaktualnieniu. Aby ręcznie sprawdzić dostępność uaktualnienia, wykonaj następujące kroki:

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.

2. W menedżerze pakietów wybierz pozycję **Aktualizacje**. Jeśli dostępna jest aktualizacja pakietu pomocy technicznej SCP.NET, jest on wyświetlany na liście. Wybierz **pozycję Aktualizuj** dla pakietu, a następnie w oknie **dialogowym Podgląd zmian** wybierz przycisk **OK,** aby go zainstalować.

> [!IMPORTANT]  
> Jeśli projekt został utworzony z wcześniejszą wersją SCP.NET, która nie używała NuGet, należy wykonać następujące kroki, aby zaktualizować do nowszej wersji:
>
> 1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.
> 2. Za pomocą pola **Wyszukaj** wyszukaj, a następnie dodaj `Microsoft.SCP.Net.SDK` do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Rozwiązywanie typowych problemów z topologią

### <a name="null-pointer-exceptions"></a>Wyjątki wskaźnika null

Podczas korzystania z topologii języka C# z klastrem HDInsight opartym na systemie Linux, składniki śruby i wylewki, które używają **ConfigurationManager** do odczytu ustawień konfiguracji w czasie wykonywania może zwrócić wyjątki wskaźnika null.

Konfiguracja projektu jest przekazywana do topologii burzy jako para kluczy i wartości w kontekście topologii. Można go pobrać z obiektu słownika, który jest przekazywany do składników po ich zainicjowaniu.

Aby uzyskać więcej informacji, zobacz [użyj ConfigurationManager](#use-configurationmanager) sekcji tego dokumentu.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Podczas korzystania z topologii języka C# z klastrem HDInsight opartym na systemie Linux może wystąpić następujący błąd:

`System.TypeLoadException: Failure has occurred while loading a type.`

Ten błąd występuje, gdy używasz pliku binarnego, który nie jest zgodny z wersją platformy .NET, którą obsługuje mono.

W przypadku klastrów HDInsight opartych na systemie Linux upewnij się, że projekt używa plików binarnych skompilowanych dla platformy .NET 4.5.

### <a name="test-a-topology-locally"></a>Testowanie topologii lokalnie

Chociaż jest to łatwe do wdrożenia topologii w klastrze, w niektórych przypadkach może być konieczne przetestowanie topologii lokalnie. Użyj następujących kroków, aby uruchomić i przetestować topologii przykład w tym artykule lokalnie w środowisku programistycznym.

> [!WARNING]  
> Lokalne testy działają tylko dla podstawowych, C#-tylko topologie. Nie można użyć lokalnych testów dla topologii hybrydowej lub topologii, które używają wielu strumieni.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Właściwości**. We właściwościach projektu. Następnie zmień **typ wyjścia** na aplikacja **konsoli**.

   ![Aplikacja HDInsight Storm, właściwości projektu, typ wyjścia](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Pamiętaj, aby zmienić **typ danych wyjściowych** z powrotem do **biblioteki klas** przed wdrożeniem topologii w klastrze.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **nowy element**. Wybierz **opcję Klasa**i wprowadź *LocalTest.cs* jako nazwę klasy. Na koniec wybierz pozycję **Dodaj**.

1. Otwórz *LocalTest.cs*i dodaj następującą `using` instrukcję u góry:

    ```csharp
    using Microsoft.SCP;
    ```

1. Użyj następującego kodu jako `LocalTest` zawartości klasy:

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

    Poświęć chwilę, aby przeczytać komentarze do kodu. Ten kod `LocalContext` służy do uruchamiania składników w środowisku programistycznym. Utrzymuje strumień danych między składnikami do plików tekstowych na dysku lokalnym.

1. Otwórz *Program.cs*i dodaj do `Main` metody następujący kod:

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

1. Zapisz zmiany, a następnie wybierz **F5** lub wybierz **debugowanie** > **rozpocznij debugowanie,** aby rozpocząć projekt. Powinno pojawić się okno konsoli i stan dziennika w miarę postępu testów. Po `Tests finished` wyświetleniu wybierz dowolny klawisz, aby zamknąć okno.

1. Użyj **Eksploratora Windows,** aby zlokalizować katalog zawierający projekt. (Na przykład: *\\C: Użytkownicy\\\<your_user_name>\\\\źródło\\repo\\WordCount WordCount*.) Następnie w tym katalogu otwórz *bin*, a następnie wybierz pozycję *Debug*. Powinny być widoczne pliki tekstowe, które zostały wyprodukowane podczas testów: *sentences.txt*, *counter.txt*i *splitter.txt*. Otwórz każdy plik tekstowy i sprawdź dane.

   > [!NOTE]  
   > Dane ciągu są zachowywane jako tablica wartości dziesiętnych w tych plikach. Na przykład `[[97,103,111]]` w pliku **splitter.txt** reprezentuje słowo *temu*.

> [!NOTE]  
> Pamiętaj, aby ustawić **typ projektu** z powrotem do **biblioteki klas** we właściwościach projektu przed wdrożeniem w klastrze Storm w programie HDInsight.

### <a name="log-information"></a>Rejestrowanie informacji

Informacje ze składników topologii można łatwo `Context.Logger`rejestrować za pomocą programu . Na przykład następujące polecenie tworzy wpis dziennika informacyjnego:

`Context.Logger.Info("Component started");`

Zarejestrowane informacje można przeglądać z **dziennika usługi Hadoop**, który znajduje się w **Eksploratorze serwera**. Rozwiń wpis dla klastra Storm on HDInsight, a następnie rozwiń dziennik **usługi Hadoop**. Na koniec wybierz plik dziennika do wyświetlenia.

> [!NOTE]  
> Dzienniki są przechowywane na koncie magazynu platformy Azure, który jest używany przez klaster. Aby wyświetlić dzienniki w programie Visual Studio, należy zalogować się do subskrypcji platformy Azure, która jest właścicielem konta magazynu.

### <a name="view-error-information"></a>Wyświetlanie informacji o błędzie

Aby wyświetlić błędy, które wystąpiły w uruchomionej topologii, należy wykonać następujące kroki:

1. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy klaster Burza w programie HDInsight i wybierz polecenie **Wyświetl topologie burzy**.

   W przypadku **wylewki** i **śruby**kolumna **Ostatni błąd** zawiera informacje o ostatnim błędzie.

2. Wybierz identyfikator **wylewki** lub **identyfikator śruby** dla składnika, w który znajduje się błąd. Na stronie szczegółów są wyświetlane dodatkowe informacje o **błędach** w sekcji Błędy u dołu strony.

3. Aby uzyskać więcej informacji, wybierz **port** z sekcji **Executors** na stronie, aby wyświetlić dziennik pracownika Burzy w ciągu ostatnich kilku minut.

### <a name="errors-submitting-topologies"></a>Błędy przesyłania topologii

Jeśli natkniesz się na błędy przesyłania topologii do usługi HDInsight, można znaleźć dzienniki składników po stronie serwera, które obsługują przesyłanie topologii w klastrze HDInsight. Aby pobrać te dzienniki, użyj następującego polecenia z wiersza polecenia:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Zastąp *sshuser* kontem użytkownika SSH dla klastra. Zamień *nazwę klastra* na nazwę klastra HDInsight. Aby uzyskać więcej `scp` `ssh` informacji na temat używania i korzystania z funkcji HDInsight, zobacz [Używanie funkcji SSH z hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Zgłoszenia mogą zakończyć się niepowodzeniem z wielu powodów:

* JDK nie jest zainstalowany lub nie jest w ścieżce.
* Wymagane zależności java nie są uwzględniane w przesłaniu.
* Zależności są niezgodne.
* Nazwy topologii są duplikowane.

Jeśli plik dziennika *hdinsight-scpwebapi.out* zawiera `FileNotFoundException`plik dziennika , wyjątek może być spowodowany następującymi warunkami:

* JDK nie jest w ścieżce w środowisku programistycznym. Sprawdź, czy JDK jest zainstalowany w `%JAVA_HOME%/bin` środowisku programistycznym i to jest w ścieżce.
* Brakuje zależności Java. Upewnij się, że w ramach przesyłania znajdują się wszystkie wymagane pliki .jar.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać przykład przetwarzania danych z Centrów zdarzeń, zobacz [Przetwarzanie zdarzeń z usługi Azure Event Hubs z burzą w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Na przykład topologii języka C#, która dzieli dane strumienia na wiele strumieni, zobacz [przykład Burzy języka C#.](https://github.com/Blackmist/csharp-storm-example)

Aby dowiedzieć się więcej informacji na temat tworzenia topologii języka C#, zobacz [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Aby uzyskać więcej sposobów pracy z programem HDInsight i większą liczrą storm na przykładach HDInsight, zobacz następujące dokumenty:

**Microsoft SCP.NET**

* [Przewodnik po programowaniu protokołu SCP dla usługi Apache Storm w usłudze Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm w usłudze HDInsight**

* [Wdrażanie topologii usługi Apache Storm i zarządzanie nimi w usłudze Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Przykładowa topologia usługi Apache Storm w usłudze Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop na HDInsight**

* [Co to jest Gałąź apache i hiveQl w usłudze Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Usługa Apache HBase w usłudze HDInsight**

* [Korzystanie z bazy danych Apache HBase w usłudze Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
