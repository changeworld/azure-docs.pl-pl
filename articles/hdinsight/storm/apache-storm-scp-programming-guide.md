---
title: Podręcznik programowania platformy SCP.NET dla usługi Storm w usłudze Azure HDInsight
description: Dowiedz się, jak utworzyć przy użyciu platformy SCP.NET. Na podstawie NET topologii systemu Storm do użytku z systemem Storm w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: c85074a2b26a79dbf5e464972e7f82b5955d15f1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692471"
---
# <a name="scp-programming-guide"></a>Podręcznik programowania punkt połączenia usługi
Punkt połączenia usługi jest platformę kompilacji w czasie rzeczywistym, niezawodne i spójne i aplikacji przetwarzania danych o wysokiej wydajności. Jest on oparty na [Apache Storm](https://storm.incubator.apache.org/) — zaprojektowane przez społeczności OSS systemu przetwarzania strumienia. STORM został zaprojektowany przez Nathana Marza, i została open source w serwisie Twitter. Wykorzystuje [Apache ZooKeeper](https://zookeeper.apache.org/), innego projektu Apache, aby włączyć wysoce niezawodne rozproszone Zarządzanie koordynacji i stanu. 

Nie tylko projekt SCP przenoszone systemu Storm w Windows, ale projektu dodano także rozszerzenia i dostosowywanie na potrzeby ekosystemu Windows. Rozszerzenia .NET, środowisko programistyczne i bibliotek, dostosowanie zawiera wdrożenie oparte na Windows. 

Rozszerzenie i dostosowanie odbywa się w taki sposób, że nie musimy rozwidlenie projektów OSS i mogliśmy wykorzystać pochodnej ekosystemów korzystających z systemu Storm.

## <a name="processing-model"></a>Przetwarzania modelu
Dane w punkcie SCP ma formę ciągłe strumienie krotek. Zazwyczaj kolekcje przepływać do niektórych kolejki najpierw, a następnie pobrana i przekształcenia przez logikę biznesową hostowaną wewnątrz topologii systemu Storm, na koniec dane wyjściowe można go przesyłać potokiem jako krotki do innego systemu punkt połączenia usługi, lub można ich zatwierdzić do magazynów np. baz danych lub rozproszonego systemu plików Podobnie jak program SQL Server.

![Diagram kolejki, wprowadzając dane do przetwarzania, co źródeł danych magazynu danych](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm — Topologia aplikacji definiuje Graf obliczeń. Każdy węzeł w topologii zawiera logikę przetwarzania, a linki między węzłami wskazują przepływu danych. Węzły, aby wstawić dane wejściowe do topologii są nazywane _spouts_, który może służyć do sekwencji danych. Dane wejściowe mogą znajdować się w dzienniki w pliku, baza danych transakcyjnych, licznik wydajności systemu itp. Węzły z przepływami zarówno dane wejściowe i wyjściowe są nazywane _bolts_, które jest rzeczywistych danych, filtrowania i opcje i agregacji.

Punkt połączenia usługi obsługuje wszelkich starań, co najmniej jednokrotne i dokładnie — raz przetwarzania danych. W transmisji strumieniowej przetwarzania aplikacji rozproszonej różne błędy może się zdarzyć podczas przetwarzania danych, takich jak awaria sieci, błąd maszyny lub błędu w kodzie użytkownika itd. Co najmniej jednokrotne przetwarzania gwarantuje, że wszystkie dane będą przetwarzane przez odtworzenie automatycznie tych samych danych, gdy wystąpił błąd co najmniej raz. Przetwarzanie co najmniej jednokrotne jest proste i niezawodne i odpowiada również wiele aplikacji. Jednak jeśli aplikacja wymaga dokładnego zliczania, co najmniej jednokrotne przetwarzania jest niewystarczająca od tych samych danych potencjalnie może być odtwarzany w topologii aplikacji. W takim przypadku zgadza się — po przetwarzania zaprojektowano w celu upewnić się, że wynik jest poprawna, nawet gdy danych może być odtwarzany i przetworzyć wiele razy.

Punkt połączenia usługi umożliwia deweloperom platformy .NET do tworzenia aplikacji do przetwarzania danych czasu rzeczywistego, korzystając w języku Java maszyny wirtualnej (JVM) z systemem Storm w sposób niewidoczny. .NET i JVM komunikują się za pośrednictwem lokalnego gniazda TCP. Zasadniczo każdy Spout/Bolt to para procesu .NET i Java gdzie logikę użytkownika działa w procesie .NET jako wtyczkę.

Aby utworzyć aplikację do przetwarzania danych na podstawie punkt połączenia usługi, potrzebne są podjęcia kilku czynności:

* Projektowanie i implementację elementów Spout, aby pobrać dane z kolejki.
* Projektowanie i implementowanie Bolt do przetwarzania danych wejściowych i Zapisz danych zewnętrzne magazyny takie jak bazy danych.
* Projektowanie topologii, a następnie przesyłać i uruchomić topologię. Topologia definiuje wierzchołków i dane między wierzchołków. Punkt połączenia usługi będzie wykonać specyfikacji topologii i wdróż go na klaster Storm, w którym każdy wierzchołek jest uruchamiany w jednym węźle logiczne. Tryb failover i skalowania będzie można uwzględnić w harmonogramie zadań systemu Storm.

Ten dokument używa kilka prostych przykładów, aby przedstawiają sposób tworzenia aplikacji przetwarzania danych przy użyciu punktu połączenia usługi.

## <a name="scp-plugin-interface"></a>Interfejs wtyczki punkt połączenia usługi
Wtyczki punkt połączenia usługi (lub aplikacje) są exe autonomicznych, które można jednocześnie uruchomić w programie Visual Studio w fazie opracowywania i być podłączane do potoku Storm po wdrożeniu w środowisku produkcyjnym. Zapisywanie wtyczki punkt połączenia usługi jest w taki sam jak pisanie innych standardowych Windows konsoli aplikacji. Platforma platformy SCP.NET deklaruje niektóre interfejs dla elementu spout/bolt i kod wtyczki użytkownika należy zaimplementować te interfejsy. Głównym celem tego projektu jest to, że użytkownik może skoncentrować się na ich własnych firm warunki logiczne i pozostawienie niezmienionej innych rzeczy, które mają być obsługiwane przez platformę platformy SCP.NET.

Kod wtyczki użytkownika powinny implementować jeden z następujących typów interfejsów, zależy od tego, czy topologia jest transakcyjna nietransakcyjnej oraz tego, czy składnik jest spout lub bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin jest wspólny interfejs dla wszystkich rodzajów wtyczek. Obecnie jest fikcyjnego interfejsu.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout jest interfejs spout nietransakcyjnej.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Gdy `NextTuple()` nosi C\# kod użytkownika może emitować jeden lub więcej krotek. Jeśli nie ma nic do emisji, ta metoda powinna zwrócić bez emitowania niczego. Należy zauważyć, że `NextTuple()`, `Ack()`, i `Fail()` wszystkie noszą nazwę w pętli w pojedynczych wątkach w języku C\# procesu. W przypadku krotek, nie można wyemitować jest uprzejmy mieć NextTuple uśpienia przez krótki czas (na przykład 10 ms), tak aby nie tracić dużo procesora CPU.

`Ack()` i `Fail()` są wywoływane tylko wtedy, gdy jest to mechanizm potwierdzenia jest włączany w specyfikacji pliku. `seqId` Służy do identyfikowania spójną kolekcją, która jest potwierdzony lub nie powiodło się. Dlatego po włączeniu potwierdzenia w topologii nietransakcyjnych w Spout należy użyć następujących funkcji emitowanie:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Jeśli potwierdzenia nie jest obsługiwany w topologii nietransakcyjnej `Ack()` i `Fail()` może pozostać pusta funkcja.

`parms` Parametr wejściowy w tych funkcji jest pusty słownik, jest on zarezerwowany do użytku w przyszłości.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt jest interfejs nietransakcyjnej bolt.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Po udostępnieniu nowej krotki `Execute()` funkcja jest wywoływana, aby go przetworzyć.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout jest interfejs spout transakcyjnych.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Podobnie jak w przypadku ich nietransakcyjnej zaradczych strony `NextTx()`, `Ack()`, i `Fail()` wszystkie noszą nazwę w pętli w pojedynczych wątkach w języku C\# procesu. W przypadku danych do emisji jest uprzejmy mieć `NextTx` uśpienia krótki przedział czasu (w 10 ms), tak aby nie tracić dużo procesora CPU.

`NextTx()` jest wywoływana, aby uruchomić nowej transakcji, parametr out `seqId` służy do identyfikowania transakcji, który jest również używany w `Ack()` i `Fail()`. W `NextTx()`, użytkownik może emitować dane do strony języka Java. Dane są przechowywane w dozorcy do obsługi powtarzania. Pojemność dozorcy jest ograniczona, użytkownik powinien Emituj tylko metadanych, nie zbiorczo dane transakcyjne spout.

STORM będą oparte na metodzie powtórzeń transakcji automatycznie, jeśli jej nie powiedzie się, więc `Fail()` nie powinien być wywoływany w przypadku normalnych. Ale jeśli punkt połączenia usługi można sprawdzić metadanych wyemitowane przez spout transakcyjnych, może wywołać `Fail()` po metadanych jest nieprawidłowy.

`parms` Parametr wejściowy w tych funkcji jest pusty słownik, jest on zarezerwowany do użytku w przyszłości.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt jest interfejs transakcyjnych bolt.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` jest wywoływana, gdy istnieje nowy krotki otrzymywanych elementy bolt. `FinishBatch()` jest wywoływana, gdy ta transakcja zostanie zakończona. `parms` Parametr wejściowy jest zarezerwowana do użytku w przyszłości.

Topologia transakcyjne jest bardzo ważnym pojęciem — `StormTxAttempt`. Ma dwa pola, `TxId` i `AttemptId`. `TxId` Służy do identyfikowania określonej transakcji, a dla danej transakcji, może istnieć wiele prób Jeśli transakcja nie powiedzie się i jest odtwarzany. Platformy SCP.NET tworzy nowy obiekt ISCPBatchBolt do przetwarzania każdej `StormTxAttempt`— podobnie jak w języku Java działanie systemu Storm. Celem tego projektu jest obsługuje transakcji równoległych przetwarzania. Użytkownik należy go przechowywać w uwadze, jeśli próba transakcji, odpowiedni obiekt ISCPBatchBolt jest niszczony i bezużyteczne.

## <a name="object-model"></a>Model obiektów
Platformy SCP.NET udostępnia także prosty zestaw obiektów kluczy dla deweloperów do programu za pomocą. Są one **kontekstu**, **stan klientów**, i **SCPRuntime**. Zostały one omówione w pozostałej części tej sekcji.

### <a name="context"></a>Kontekst
Kontekst udostępnia środowisko uruchomionej aplikacji. Każde wystąpienie ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) ma odpowiednie wystąpienie kontekstu. Funkcje udostępniane przez kontekst można podzielić na dwie części: (1) statycznej części, które jest dostępne w całej C\# przetwarzania, (2) części dynamicznej, która jest dostępna tylko dla określonego wystąpienia kontekstu.

### <a name="static-part"></a>Część statyczne
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` znajduje się w celu dziennika.

`pluginType` Służy do wskazywania typ wtyczki C\# procesu. Jeśli C\# proces jest uruchamiany w trybie lokalnego testu (bez Java), typ wtyczki jest `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` podano można pobrać parametrów konfiguracji po stronie języka Java. Parametry są przekazywane ze strony Java podczas C\# dodatek został zainicjowany. `Config` Parametry są podzielone na dwie części: `stormConf` i `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` to parametry zdefiniowane przez system Storm i `pluginConf` jest parametrów zdefiniowanych przez punkt połączenia usługi. Na przykład:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` znajduje się pobrać kontekstu topologii, jest najbardziej użyteczna dla składników za pomocą wielu równoległości. Oto przykład:

    //demo how to get TopologyContext info
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)                      
    {
        Context.Logger.Info("TopologyContext info:");
        TopologyContext topologyContext = Context.TopologyContext;                    
        Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());          
        taskIndex = topologyContext.GetThisTaskIndex();
        Context.Logger.Info("taskIndex: {0}", taskIndex);
        string componentId = topologyContext.GetThisComponentId();                    
        Context.Logger.Info("componentId: {0}", componentId);
        List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
        Context.Logger.Info("taskNum: {0}", componentTasks.Count);                    
    }

### <a name="dynamic-part"></a>Część dynamicznego
Następujące interfejsy są odpowiednie do wystąpienia kontekstu. Wystąpienie kontekstu jest tworzony przez platformę platformy SCP.NET i przekazywane do kodu użytkownika:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Dla obsługi potwierdzenia nietransakcyjnej spout jest dostępna w następującej metody:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Dla obsługi potwierdzenia nietransakcyjnej elementu bolt, należy go jawnie `Ack()` lub `Fail()` otrzymał spójnej kolekcji. I podczas emitowania krotki nowe, musi on również określać kotwic nowe spójnej kolekcji. Znajdują się następujące metody.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` udostępnia usługi metadanych, generowania sekwencji monotonicznej i koordynacji bez oczekiwania. Abstrakcje wyższego poziomu współbieżności rozproszonej mogą być wbudowane w `StateStore`, w tym rozproszonych blokad, rozproszonych kolejek, barier i transakcji usługi.

Punkt połączenia usługi, aplikacje mogą używać `State` obiektu, aby zachować niektóre informacje w [Apache ZooKeeper](https://zookeeper.apache.org/), szczególnie w przypadku topologii transakcyjnych. Robi, jeśli transakcyjnych spout ulega awarii, uruchom ponownie go można pobrać niezbędne informacje z dozorcy i ponownie uruchomić potok.

`StateStore` Obiekt ma przede wszystkim tych metod:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State` Obiekt ma przede wszystkim tych metod:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Dla `Commit()` metody, gdy simpleMode jest ustawiona na wartość true, usuwa odpowiedniego ZNode w dozorcy. W przeciwnym razie spowoduje usunięcie bieżącego ZNode i dodanie nowego węzła w PRZYDZIELONY\_ścieżki.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime zawiera następujących dwóch metod:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` Służy do inicjowania środowiska wykonawczego punkt połączenia usługi. W przypadku tej metody C\# procesu łączy się po stronie Java, a następnie pobiera parametry konfiguracji i topologii kontekstu.

`LaunchPlugin()` Służy do uruchamiał pętli przetwarzania komunikatów. W tym pętli C\# wtyczki odbiera komunikaty formularza po stronie Java (w tym sygnały krotek i kontroli) i następnie przetwarzanie wiadomości, być może podczas wywoływania metody interfejsu zapewniają przez kod użytkownika. Parametr wejściowy metody `LaunchPlugin()` jest delegat, który może zwrócić obiekt, który implementuje interfejs ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Dla ISCPBatchBolt, możemy pobrać `StormTxAttempt` z `parms`i przy jego użyciu ocenić, czy jest powtórzonym próby. Sprawdzanie prób powtarzania często odbywa się na bolt zatwierdzenia i wspomniane `HelloWorldTx` przykład.

Ogólnie rzecz biorąc wtyczek SCP może działać w dwóch trybach tutaj:

1. Tryb lokalnego testu: W tym trybie wtyczek punkt połączenia usługi (C\# kodu użytkownika) uruchamiane wewnątrz programu Visual Studio w fazie opracowywania. `LocalContext` można w ten tryb, który udostępnia metody do serializacji emitowany krotek do lokalnych plików i odczytania ich pamięci.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Do trybu normalnego: W tym trybie wtyczek SCP uruchamianych przez proces java storm.
   
    Poniżej przedstawiono przykład uruchamiania wtyczki punkt połączenia usługi:
   
        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
   
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
   
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }

## <a name="topology-specification-language"></a>Topologia specyfikacja języka
Specyfikacja topologii połączenia usługi jest języka specyficznego dla domeny do opisu i Konfigurowanie topologii połączenia usługi. Jest on oparty na DSL Clojure firmy Storm (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) i zostanie rozszerzony przez punkt połączenia usługi.

Specyfikacje topologii można przesyłać bezpośrednio do klastra storm do wykonania za pomocą ***runspec*** polecenia.

Platformy SCP.NET dodał następujące funkcje do definiowania transakcyjnych topologii:

| **Nowe funkcje** | **Parametry** | **Opis** |
| --- | --- | --- |
| **tx-topolopy** |Nazwa topologii<br />spout mapy<br />Mapowanie elementu bolt |Definiuj transakcyjnych topologię o nazwie topologii &nbsp;spouts definicji mapy i mapy definicji elementów bolt |
| **scp-tx-spout** |exec — nazwa<br />args<br />Pola |Zdefiniuj spout transakcyjnych. Działa z aplikacji przy użyciu ***exec-name*** przy użyciu ***args***.<br /><br />***Pola*** jest pola danych wyjściowych spout |
| **scp-tx-batch-bolt** |exec — nazwa<br />args<br />Pola |Zdefiniuj transakcyjnych Bolt usługi Batch. Działa z aplikacji przy użyciu ***exec-name*** przy użyciu ***args.***<br /><br />Pola jest pól danych wyjściowych dla elementu bolt. |
| **scp-tx-commit-bolt** |exec — nazwa<br />args<br />Pola |Zdefiniuj bolt zatwierdzania transakcji. Działa z aplikacji przy użyciu ***exec-name*** przy użyciu ***args***.<br /><br />***Pola*** jest pól danych wyjściowych dla elementu bolt |
| **nontx topolopy** |Nazwa topologii<br />spout mapy<br />Mapowanie elementu bolt |Definiuj topologię nietransakcyjna o nazwie topologii&nbsp; spouts definicji mapy i mapy definicji elementów bolt |
| **scp-spout** |exec — nazwa<br />args<br />Pola<br />parameters |Zdefiniuj spout nietransakcyjnej. Działa z aplikacji przy użyciu ***exec-name*** przy użyciu ***args***.<br /><br />***Pola*** jest pola danych wyjściowych spout<br /><br />***Parametry*** są opcjonalne, używany do określenia niektóre parametry, takie jak "nontransactional.ack.enabled". |
| **scp-bolt** |exec — nazwa<br />args<br />Pola<br />parameters |Zdefiniuj nietransakcyjna Bolt. Działa z aplikacji przy użyciu ***exec-name*** przy użyciu ***args***.<br /><br />***Pola*** jest pól danych wyjściowych dla elementu bolt<br /><br />***Parametry*** są opcjonalne, używany do określenia niektóre parametry, takie jak "nontransactional.ack.enabled". |

Program SCP.NET następujące słowa kluczowe zdefiniowane:

| **słowa kluczowe** | **Opis** |
| --- | --- |
| **: Nazwa** |Zdefiniuj nazwę topologii |
| **: topologii** |Zdefiniuj topologię za pomocą poprzedniej funkcji i kompilacja w tych. |
| **: p** |Zdefiniuj wskazówki równoległości dla każdego spout lub bolt. |
| **: config** |Zdefiniuj parametr skonfigurować lub zaktualizować istniejące |
| **:schema** |Zdefiniuj schemat Stream. |

I często używane parametry:

| **Parametr** | **Opis** |
| --- | --- |
| **"plugin.name"** |Nazwa pliku exe wtyczki C# |
| **"plugin.args"** |argumenty wtyczki |
| **"output.schema"** |Schemat danych wyjściowych |
| **"nontransactional.ack.enabled"** |Włączenie potwierdzenia dla topologii nietransakcyjna |

Polecenie runspec jest wdrażany wraz z usługi bits, użycia takich jak:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***Dir zasobów*** parametr jest opcjonalny, musisz ją określić, gdy chcesz podłączyć C\# aplikacji, a ten katalog zawiera aplikacji, zależności i konfiguracji.

***Ścieżki klasy*** parametru jest również opcjonalne. Służy do określenia klasy Java, jeśli plik specyfikacji zawiera Java elementu Spout lub Bolt.

## <a name="miscellaneous-features"></a>Różne funkcje.
### <a name="input-and-output-schema-declaration"></a>Dane wejściowe i dane wyjściowe deklaracja schematu
Użytkownicy może emitować krotkami w języku C\# procesów, platformy musi serializuje spójna kolekcja znajdująca się na byte [], przeniesienie do strony Java i Storm spowoduje przeniesienie tego krotki do elementów docelowych. W międzyczasie w składnikach podrzędnych, C\# procesów będą otrzymywać krotek powrót po awarii z boku java i przekonwertować go do oryginalnego typu przez platformę, wszystkie te operacje są ukryte przez platformę.

Do obsługi serializacji i deserializacji, kod użytkownika musi zadeklarować schematu dane wejściowe i wyjściowe.

Schemat wejścia/wyjścia strumienia jest zdefiniowany jako słownik. Klucz jest StreamId. Wartość jest typów kolumn. Składnik może mieć wiele strumieni zadeklarowana.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }


Obiekt kontekstu mamy następujący interfejs API dodane:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Deweloperzy muszą upewnij się, że wyemitowanych krotek przestrzegają schemat zdefiniowany dla tego strumienia, w przeciwnym razie system zgłosi wyjątek czasu wykonywania.

### <a name="multi-stream-support"></a>Obsługa wielu strumienia
Punkt połączenia usługi obsługuje kod użytkownika do emisji lub odbieranie z wielu różnych strumieni w tym samym czasie. Wsparcie w obiekcie kontekstu widoczna w trakcie wpisywania metoda emitowanie przyjmuje parametr Identyfikatora opcjonalne strumienia.

Dodano dwie metody w obiekcie kontekstu platformy SCP.NET. Służą one do emitowania krotki lub krotek, aby określić StreamId. StreamId jest ciągiem i musi on być zgodny w obu C\# i specyfikacja definicji topologii.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Emitowanie do strumienia nieistniejące powoduje, że wyjątki środowiska uruchomieniowego.

### <a name="fields-grouping"></a>Grupowanie pól
Wbudowane grupy pól w Storm nie działa prawidłowo w platformy SCP.NET. Po stronie serwera Proxy w języku Java wszystkie typy danych pola są rzeczywiście byte [], a pola Grupowanie używa byte [] obiektu skrótu do wykonywania grupowania. Byte [] obiektu skrótu jest adres tego obiektu w pamięci. Dlatego grupowanie będą nieprawidłowe dla obiektów [] dwubajtowy, które mają taką samą zawartość, ale nie sam adres.

Platformy SCP.NET dodaje metodę grupowania dostosowane i używa zawartości byte [] w celu grupowania. W **specyfikacja** pliku przypomina składnię:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Tutaj

1. "punkt połączenia usługi pole group" oznacza "Grupowanie pól dostosowany implementowany przez punkt połączenia usługi".
2. ": tx"lub":-tx" oznacza, że jeśli jest transakcyjna topologii. Potrzebujemy tych informacji, ponieważ indeks początkowy a topologii-tx różni się w tx.
3. [0,1] oznacza, że skrót zbiór identyfikatorów pola, począwszy od 0.

### <a name="hybrid-topology"></a>Topologia hybrydowe
Natywne Storm jest napisany w języku Java. A platformy SCP.NET ma go w celu włączenia C\# programistom pisanie C\# kod służący do obsługi ich logiki biznesowej. Ale obsługuje ona również hybrydowe topologie, które zawiera nie tylko C\# elementów spout/bolt, ale także Java Spout/Bolt.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Określ Spout/Bolt języka Java w specyfikacji pliku
W specyfikacji pliku "punkt połączenia usługi spout" i "punkt połączenia usługi bolt" może również służyć do określania Spouts języka Java i elementów Bolt, Oto przykład:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

W tym miejscu `microsoft.scp.example.HybridTopology.Generator` jest nazwa klasy Java elementu Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Określ klasy Java runSpec polecenia
Jeśli chcesz przesłać topologii zawierające Java Spouts lub Bolt, należy najpierw skompilować Java Spouts lub elementy Bolt i pobieranie plików Jar. Następnie należy określić klasy java, który zawiera pliki JAR używane podczas przesyłania topologii. Oto przykład:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

W tym miejscu **przykłady\\HybridTopology\\java\\docelowej\\**  jest folder zawierający plik Jar elementu Spout/Bolt języka Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializacja i deserializacja między języka Java i C\#
Składnik punktu połączenia usługi zawiera strony Java i C\# po stronie. Aby można było korzystać z języka Java za pomocą natywnego elementów Spout/Bolt, serializacji/deserializacji przeprowadza się między stronie Java i C\# stronie, jak pokazano na poniższym wykresie.

![Diagram składników java wysyłania do składnika punkt połączenia usługi, wysyłania do składnika języka Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializacja w bok Java i deserializacji w języku C\# po stronie**
   
   Najpierw firma Microsoft zapewnia domyślną implementację serializacji stronie Java i deserializacji w języku C\# po stronie. Metoda serializacji w języku Java po stronie można określić w specyfikacji pliku:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoda deserializacji w języku C\# po stronie należy określić w języku C\# kod użytkownika:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Ta domyślna implementacja powinna obsługiwać większości przypadków, typ danych nie jest zbyt złożony. W przypadku niektórych przypadków, ponieważ typ danych użytkownika jest zbyt złożony lub wydajność naszej implementacji domyślnej nie spełnia wymagań użytkownika, użytkownicy mogą wtyczki zapewniali własną implementację.
   
   Serializacja interfejsu java stronie jest zdefiniowany jako:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Interfejs deserialize w języku C\# po stronie jest zdefiniowany jako:
   
   Interfejs publiczny ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serializacja w języku C\# po stronie i deserializacji stronie języka Java**
   
   Metoda serializacji w języku C\# po stronie należy określić w języku C\# kod użytkownika:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Metoda deserializacji stronie Java powinna być określona w specyfikacji pliku:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Oto "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" na nazwę Deserializator i dodano "microsoft.scp.example.HybridTopology.Person" do deserializacji jest klasa target danych.
   
   Użytkownika można również dodać zapewniali własną implementację C\# serializator i Deserializator języka Java. Ten kod jest interfejs C\# serializator:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Ten kod jest interfejs Deserializator Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Tryb punktu połączenia usługi hosta
W tym trybie użytkownika można skompilować ich kody do biblioteki DLL i umożliwia przesyłanie topologii SCPHost.exe dostarczonych przez punkt połączenia usługi. Plik specyfikacji wygląda podobnie do tego kodu:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

W tym miejscu `plugin.name` jest określony jako `SCPHost.exe` dostarczonych przez punkt połączenia usługi SDK. SCPHost.exe przyjmuje trzy parametry:

1. Pierwsza z nich jest nazwa biblioteki DLL, która jest `"HelloWorld.dll"` w tym przykładzie.
2. Drugim jest nazwy klasy, która jest `"Scp.App.HelloWorld.Generator"` w tym przykładzie.
3. Trzecie nazywa się publicznej metody statycznej, która może być wywołana w celu pobrania wystąpienia ISCPPlugin.

W trybie hosta kodu użytkownika jest kompilowany jako biblioteka DLL i jest wywoływana przez platformę punkt połączenia usługi. Dlatego punkt połączenia usługi platformy można uzyskać pełną kontrolę nad logika przetwarzania całego. Dlatego zalecamy naszym klientom przesyłanie topologii, w trybie hosta punkt połączenia usługi, ponieważ można uprościć środowisko programistyczne, a przynoszą nam większą elastyczność i lepszą zgodność z poprzednimi wersjami oraz nowszych wersji.

## <a name="scp-programming-examples"></a>Przykłady programowania punkt połączenia usługi
### <a name="helloworld"></a>HelloWorld
**HelloWorld** to prosty przykład, aby pokazać smak platformy SCP.NET. Używa nietransakcyjnej topologii z spout, o nazwie **generator**i dwóch elementów bolt o nazwie **rozdzielacz** i **licznika**. Spout **generator** losowo generuje zdania i emitują te zdania w celu **rozdzielacz**. Elementy bolt **rozdzielacz** dzieli zdania wyrazy i wyemituj te wyrazy do **licznika** bolt. Elementy bolt "counter" używa słownik do rejestrowania liczbę wystąpień każdego wyrazu.

Istnieją dwa pliki specyfikacji, **HelloWorld.spec** i **HelloWorld\_EnableAck.spec** w tym przykładzie. W języku C\# kodu, jego można znaleźć włączenie potwierdzenia uzyskując pluginConf ze strony języka Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

W spout po włączeniu potwierdzenia słownika jest używany w pamięci podręcznej krotek, które nie zostały potwierdzone. Jeśli Fail() jest wywoływana, jest odtwarzany spójna kolekcja znajdująca się nie powiodło się:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### <a name="helloworldtx"></a>HelloWorldTx
**HelloWorldTx** przykład pokazuje, jak wdrożyć topologię transakcyjnych. Ma on jeden spout o nazwie **generator**, bolt partii o nazwie **partial-count**, oraz nazywany bolt zatwierdzenia **Suma liczba**. Istnieją trzy pliki txt wstępnie utworzone: **DataSource0.txt**, **DataSource1.txt**, i **DataSource2.txt**.

W każdej transakcji spout **generator** losowo wybiera dwa pliki z wstępnie utworzone trzy pliki i emitować nazw dwóch plików do **partial-count** bolt. Elementy bolt **partial-count** pobiera plik nazwę z odebranych spójnej kolekcji, a następnie otwórz plik i liczbę słów w tym pliku i na koniec emitować numer programu word do **suma liczby** bolt. **Suma liczby** bolt zawiera podsumowanie liczby całkowitej.

Do osiągnięcia **dokładnie jeden raz** semantykę, bolt zatwierdzenia **Suma liczba** należy ocenić, czy jest powtórzonym transakcji. W tym przykładzie ma zmienną statycznej składowej:

    public static long lastCommittedTxId = -1; 

Po utworzeniu wystąpienia ISCPBatchBolt pobiera `txAttempt` z poziomu parametrów wejściowych:

    public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
    {
        /* for transactional topology, we can get txAttempt from the input parms */
        if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
        {
            StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
            return new CountSum(ctx, txAttempt);
        }
        else
        {
            throw new Exception("null txAttempt");
        }
    }

Gdy `FinishBatch()` jest wywoływana, `lastCommittedTxId` zostaną zaktualizowane, jeśli nie jest powtórzonym transakcji.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Ta topologia zawiera elementu Spout języka Java i C\# Bolt. Używa ona domyślną implementację serializacji i deserializacji dostarczane przez platformę punkt połączenia usługi. Zobacz **HybridTopology.spec** w **przykłady\\HybridTopology** szczegóły specyfikacji pliku, folderu i **SubmitTopology.bat** dla sposobu określania języka Java Ścieżka klasy.

### <a name="scphostdemo"></a>SCPHostDemo
W tym przykładzie jest taka sama jak HelloWorld w zasadzie. Jedyną różnicą jest, że kod użytkownika jest kompilowany jako biblioteka DLL, a topologia jest przesyłany za pomocą SCPHost.exe. Zobacz sekcję "Tryb punktu połączenia usługi hosta" Aby uzyskać bardziej szczegółowy opis.

## <a name="next-steps"></a>Następne kroki
Przykłady topologii Apache Storm utworzone przy użyciu punktu połączenia usługi można znaleźć w następujących dokumentach:

* [Opracowywanie topologii języka C# dla Storm Apache na HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Process Events from Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight zdarzeń](apache-storm-develop-csharp-event-hub-topology.md)
* [Przetwarzanie danych czujnika pojazdu z usługi Event Hubs przy użyciu systemu Storm Apache na HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do bazy danych Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
