---
title: Podręcznik programowania SCP.NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać SCP.NET do tworzenia. Na podstawie NET topologii Storm do za pomocą Storm w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 0f4c021bc209c99e1b3f34b34bf5ba0549eb48f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421559"
---
# <a name="scp-programming-guide"></a>Podręcznik programowania punkt połączenia usługi
Punkt połączenia usługi jest platformę do tworzenia w czasie rzeczywistym, niezawodnych, spójne i wysokiej wydajności przetwarzania danych aplikacji. Jest ona wbudowana nad [Apache Storm](http://storm.incubator.apache.org/) — systemu projektowania Wspólnot OSS przetwarzania strumienia. STORM jest zaprojektowany przez Nathan Marz i został otwarty kwerendą źródłową Twitter. Jest przeprowadzana z zastosowaniem [Apache ZooKeeper](http://zookeeper.apache.org/), inny projekt Apache w celu włączenia wysoce niezawodne rozproszone Zarządzanie koordynacji i stanu. 

Nie tylko projektu SCP systemie Storm w systemie Windows, ale również projekt został dodany rozszerzenia i dostosowania dla ekosystemu systemu Windows. Rozszerzenia .NET środowisko dewelopera i bibliotek, dostosowywania zawiera wdrażania systemu Windows. 

Rozszerzenie i dostosowanie odbywa się w taki sposób, że firma Microsoft nie ma potrzeby rozwidlania projektów OSS i firma Microsoft może korzystać z ekosystemami pochodnej, rozszerzający Storm.

## <a name="processing-model"></a>Przetwarzanie modelu
Dane w punkt połączenia usługi jest modelowana jako ciągłe strumienie spójnych kolekcji. Zwykle krotki przepływać do niektórych kolejki najpierw, a następnie pobrana i przekształcenia przez hostowaną wewnątrz topologii Storm logiki biznesowej, koniec dane wyjściowe może być przekazywane w potoku jako krotek do innego systemu SCP lub zostać zatwierdzone do magazynów, takich jak rozproszonego systemu plików lub baz danych Podobnie jak SQL Server.

![Diagram kolejki podawania danych do przetwarzania, które źródeł danych magazynu danych](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Topologia aplikacji Storm, definiuje Graf obliczeń. Każdy węzeł w topologii zawiera logikę przetwarzania i łącza między węzłami Oznaczanie przepływu danych. Węzły można wstawić danych wejściowych do topologii są nazywane _spouts_, które mogą służyć do serii danych. Dane wejściowe może znajdować się w plików dzienników, transakcyjne bazy danych licznika wydajności systemu itp. Węzły z obu przepływów danych wejściowych i wyjściowych są nazywane _bolts_, która nie filtrowania rzeczywiste dane i opcje i agregacji.

Punkt połączenia usługi obsługuje starań w najmniej jednokrotne, a dokładnie — raz przetwarzania danych. W przypadku przesyłania strumieniowego przetwarzania aplikacji rozproszonej różne błędy może się tak zdarzyć podczas przetwarzania danych, takich jak awaria sieci, błąd maszyny lub błąd kodu użytkownika itd. Przetwarzanie w najmniej jednokrotne gwarantuje, że wszystkie dane będą przetwarzane przez odtwarzanie automatycznie tych samych danych, gdy błąd występuje co najmniej raz. Przetwarzanie w najmniej jednokrotne jest proste i niezawodne i odpowiada również wiele aplikacji. Jednak gdy aplikacja wymaga dokładnego zliczanie, przetwarzania na najmniej jednokrotne jest niewystarczająca, ponieważ te same dane potencjalnie może zostać odtworzone w topologii aplikacji. W takim przypadku, dokładnie-po przetwarzania zaprojektowano w celu upewnij się, że wynik jest poprawna, nawet jeśli dane mogą być odtwarzany i przetwarzane wiele razy.

Punkt połączenia usługi umożliwia deweloperom platformy .NET opracowywania aplikacji proces danych czasu rzeczywistego pracownikom na języku Java maszyny wirtualnej (JVM) z systemu Storm w tle. .NET i JVM komunikują się za pośrednictwem lokalnego gniazda TCP. Zasadniczo każdego Spout/Bolt jest para procesu .net/Java, gdzie logikę użytkownika działa w procesie .net jako dodatek.

Do tworzenia aplikacji programu przetwarzania danych na górze punkt połączenia usługi, potrzebne jest wykonanie kilku kroków:

* Projektowanie i implementowanie elementach Spout ściągania danych z kolejki.
* Projektowanie i implementowanie elementów Bolt do przetwarzania danych wejściowych i Zapisz danych zewnętrzne magazyny, takie jak bazy danych.
* Projektowanie topologii, przesyłania, a następnie uruchom topologii. Topologia definiuje wierzchołki i dane między wierzchołków. Punkt połączenia usługi otrzymuje specyfikację topologii i wdrożyć ją na klaster Storm, w którym każdy wierzchołek jest uruchamiany w jednym węźle logiczne. Tryb failover i skalowanie będzie można wybrać ofertę z harmonogramu zadań systemu Storm.

Ten dokument używa proste przykłady przechodzenia przez sposób tworzenia aplikacji przetwarzania danych z punktu połączenia usługi.

## <a name="scp-plugin-interface"></a>Interfejs wtyczki punkt połączenia usługi
Wtyczki punkt połączenia usługi (lub aplikacji) są autonomiczne plików exe, można jednocześnie uruchomić w programie Visual Studio fazie projektowania, które można podłączyć do potoku Storm po wdrożeniu w środowisku produkcyjnym. Pisanie wtyczki punkt połączenia usługi jest w taki sam jak zapisywania wszelkich innych standardowych aplikacji systemu Windows konsoli. Platforma SCP.NET deklaruje niektórych interfejs dla spout/bolt i kod wtyczki użytkownika należy zaimplementować te interfejsy. Głównym celem projektu tego jest to, czy użytkownik może skupić się na ich własnych warunki biznesowe logiczne i pozostawienie innymi mają być obsługiwane przez platformę SCP.NET.

Kod użytkownika wtyczki powinny implementować jeden z następujących typów interfejsów, zależy od tego, czy topologia jest transakcyjna lub nietransakcyjna i określa, czy składnik jest spout lub bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin jest wspólny interfejs dla wszystkich rodzajów wtyczek. Obecnie jest interfejsem zastępczego.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout jest interfejsem spout nietransakcyjnej.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Gdy `NextTuple()` jest nazywany C\# jeden lub więcej krotek można emitowanie kodu użytkownika. Jeśli nie ma nic do emisji, ta metoda powinna zwrócić bez emitowanie żadnych czynności. Należy zauważyć, że `NextTuple()`, `Ack()`, i `Fail()` są wszystkie wywoływane w pętli ścisłej w jednym wątku w języku C\# procesu. Nie ma żadnych krotek, aby emitować, jest uprzejmy uśpienia NextTuple o krótkim czasie (na przykład 10 ms) w celu są używane zbyt dużo Procesora.

`Ack()` i `Fail()` są wywoływane tylko wtedy, gdy mechanizmu potwierdzenia jest włączone w specyfikacji pliku. `seqId` Służy do identyfikowania krotki jest prześledzone lub nie powiodło się. Dlatego po włączeniu potwierdzenia w nietransakcyjnej topologii w Spout należy użyć następujących funkcji emitowanie:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Jeśli potwierdzenie nie jest obsługiwany w topologii nietransakcyjnej `Ack()` i `Fail()` może pozostać puste funkcji.

`parms` Parametru wejściowego w tych funkcji jest pusty słownik, jest on zarezerwowany do użytku w przyszłości.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt jest interfejsem bolt nietransakcyjnej.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Po udostępnieniu nowej krotki `Execute()` funkcja jest wywoływana go przetworzyć.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout jest interfejsem spout transakcyjnych.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Podobnie jak ich nietransakcyjnej zaradczych strony `NextTx()`, `Ack()`, i `Fail()` są wszystkie wywoływane w pętli ścisłej w jednym wątku w języku C\# procesu. Jeśli nie ma żadnych danych do wysyłania, jest uprzejmy mają `NextTx` uśpienia przez krótki czas (10 ms) w celu są używane zbyt dużo Procesora.

`NextTx()` jest wywoływana, aby rozpocząć nowej transakcji, a parametr out `seqId` służy do identyfikowania transakcji, która jest również używana w `Ack()` i `Fail()`. W `NextTx()`, użytkownik może emitować danych do strony Java. Dane są przechowywane w dozorcy do obsługi powtarzania. Ponieważ pojemność dozorcy jest ograniczona, użytkownik powinien Emituj tylko metadane, nie zbiorcze danych transakcyjnych spout.

STORM będzie odtwarzana w transakcji automatycznie w przypadku niepowodzenia to `Fail()` nie powinna być wywoływana w przypadku normalnych. Ale jeśli punkt połączenia usługi można sprawdzić metadanych emitowane przez transakcyjne spout, może wywołać `Fail()` Jeśli metadanych jest nieprawidłowy.

`parms` Parametru wejściowego w tych funkcji jest pusty słownik, jest on zarezerwowany do użytku w przyszłości.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt jest interfejs dla transakcyjnego bolt.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` jest wywoływana po nowej krotki otrzymywanych elementy bolt. `FinishBatch()` jest wywoływana po zakończeniu tej transakcji. `parms` Parametr wejściowy jest zarezerwowany do użytku w przyszłości.

Topologia transakcyjne jest ważne pojęcia — `StormTxAttempt`. Składa się z dwóch pól `TxId` i `AttemptId`. `TxId` Służy do identyfikowania określonej transakcji, a dla danej transakcji, może mieć wiele prób transakcji nie powiedzie się i jest odtwarzany. SCP.NET tworzy nowy obiekt ISCPBatchBolt przetwarzania każdego `StormTxAttempt`, po prostu podobnie jak w języku Java czego Storm. Celem tego projektu jest obsługuje transakcji równoległych przetwarzania. Użytkownik powinien zachować pamiętać, że jeśli próba transakcji jest zakończone, odpowiedni obiekt ISCPBatchBolt zostanie zniszczony i bezużytecznych.

## <a name="object-model"></a>Model obiektu
SCP.NET udostępnia prosty zestaw obiektów kluczy dla deweloperów do programu z. Są one **kontekstu**, **stan klientów**, i **SCPRuntime**. Są one omówione w pozostałej części tej sekcji.

### <a name="context"></a>Kontekst
Kontekst udostępnia środowisko uruchomionej aplikacji. Każde wystąpienie ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) ma odpowiednie wystąpienia kontekstu. Funkcje zapewniane przez kontekst można podzielić na dwie części: (1) części statycznej, które jest dostępne w całej C\# przetwarzać, (2 dynamiczne strony, który jest dostępny tylko dla określonego wystąpienia kontekstu.

### <a name="static-part"></a>Statycznej części
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` podano w celu dziennika.

`pluginType` Służy do wskazywania typu dodatek c\# procesu. Jeśli C\# proces jest uruchomiony w trybie lokalnym testu (bez Java), jest typu wtyczki `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` podano można pobrać parametrów konfiguracji po stronie Java. Parametry są przekazywane z Java strony po C\# dodatek został zainicjowany. `Config` Parametry są podzielone na dwie części: `stormConf` i `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` jest parametrów zdefiniowanych Storm i `pluginConf` jest parametrów zdefiniowanych przez punkt połączenia usługi. Na przykład:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` podano pobrać kontekstu topologii, jest najbardziej przydatny w przypadku składników z wielu równoległości. Oto przykład:

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

### <a name="dynamic-part"></a>Części dynamicznej
Następujące interfejsy są odpowiednie do wystąpienia kontekstu. Wystąpienie kontekstu jest tworzony przez platformę SCP.NET i przekazane do kodu użytkownika:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Do obsługi potwierdzenia nietransakcyjnej spout podano następującą metodę:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Dla obsługi potwierdzenia nietransakcyjnej bolt, należy go jawnie `Ack()` lub `Fail()` odebrała spójnej kolekcji. I podczas emitowania nowe spójnej kolekcji, należy także określić kotwic nowe spójnej kolekcji. Udostępniono następujące metody.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Stan klientów
`StateStore` udostępnia usługi metadanych, generowanie monotoniczna sekwencji i koordynacji bez oczekiwania. Abstrakcje wyższego poziomu współbieżności rozproszonej mogą być wbudowane w `StateStore`, w tym rozproszonej blokad, kolejek rozproszonych bariery i transakcji usługi.

Punkt połączenia usługi, aplikacje mogą używać `State` obiekt, aby zachować niektóre informacje w dozorcy, szczególnie w przypadku topologii transakcyjnych. Wykonywanie, dzięki czemu transakcyjne spout ulegnie awarii, uruchom ponownie może pobrać niezbędne informacje z dozorcy i ponownie uruchomić potoku.

`StateStore` Obiekt ma głównie tych metod:

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
    /// <returns>Uncommited States</returns>
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
    public IEnumerable<Registry> Commited();

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

`State` Obiekt ma głównie tych metod:

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

Dla `Commit()` metody, gdy simpleMode ma ustawioną wartość true, usuwa odpowiedniego ZNode w dozorcy. W przeciwnym razie usuwa bieżący ZNode i dodanie nowego węzła w zatwierdzone\_ścieżki.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime zapewnia następujących dwóch metod:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` Służy do inicjowania środowiska uruchomieniowego punkt połączenia usługi. W przypadku tej metody C\# proces łączy się z boku Java i pobiera parametry konfiguracji i topologii kontekstu.

`LaunchPlugin()` Służy do zaczną działać poza pętlą przetwarzania komunikatów. W tym pętli C\# wtyczki odbiera komunikaty formularza po stronie Java (w tym sygnały krotek i sterowania), a następnie przetwarzanie wiadomości, być może podczas wywoływania metody interfejsu Podaj przez kod użytkownika. Parametr wejściowy dla metody `LaunchPlugin()` jest delegata, który może zwracać obiekt, który implementuje interfejs ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Dla ISCPBatchBolt, można pobrać `StormTxAttempt` z `parms`i przy jego użyciu ocenić, czy jest ono próbę powtórzony. Sprawdzanie prób powtarzania często jest wykonywane na bolt zatwierdzania i zostało to przedstawione w `HelloWorldTx` przykład.

Ogólnie rzecz biorąc wtyczek SCP może działać w dwóch trybach tutaj:

1. Tryb lokalnego testu: W tym trybie wtyczek punkt połączenia usługi (C\# kod użytkownika) uruchom w programie Visual Studio w fazie projektowania. `LocalContext` można w tym trybie, który zapewnia metody do serializacji emitowany krotek do lokalnych plików i odczytania ich pamięci.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Tryb regularnych: W tym trybie wtyczek punkt połączenia usługi będą uruchamiane przez proces java storm.
   
    Oto przykład uruchamiania SCP wtyczki:
   
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
Specyfikacja topologii punkt połączenia usługi jest języka specyficznego dla domeny zawierająca opis i Konfigurowanie topologii punkt połączenia usługi. Jest on oparty na jego Storm Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) i zostanie rozszerzony przez punkt połączenia usługi.

Specyfikacje topologii można przesyłać bezpośrednio do klastra storm do wykonywania za pośrednictwem ***runspec*** polecenia.

SCP.NET dodał następujące funkcje do definiowania transakcyjne topologii:

| **Nowe funkcje** | **Parametry** | **Opis** |
| --- | --- | --- |
| **TX topolopy** |Nazwa topologii<br />spout mapy<br />bolt mapy |Zdefiniuj transakcyjne topologii o nazwie topologii &nbsp;spouts mapy definicji, a następnie mapować definicji elementów bolt |
| **spout-tx — punkt połączenia usługi** |exec — nazwa<br />argumentów<br />pola |Zdefiniuj spout transakcyjnych. Uruchomieniu aplikacji z ***nazwa exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** jest pól danych wyjściowych dla spout |
| **punkt połączenia usługi tx partii bolt** |exec — nazwa<br />argumentów<br />pola |Zdefiniuj transakcyjne Bolt partii. Uruchomieniu aplikacji z ***nazwa exec*** przy użyciu ***argumentów.***<br /><br />Pola jest pól danych wyjściowych dla bolt. |
| **punkt połączenia usługi tx-commit-bolt** |exec — nazwa<br />argumentów<br />pola |Zdefiniuj bolt transakcyjnych zatwierdzania. Uruchomieniu aplikacji z ***nazwa exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** jest pól danych wyjściowych dla bolt |
| **nontx topolopy** |Nazwa topologii<br />spout mapy<br />bolt mapy |Zdefiniuj topologię nietransakcyjne o nazwie topologii&nbsp; spouts mapy definicji, a następnie mapować definicji elementów bolt |
| **spout punkt połączenia usługi** |exec — nazwa<br />argumentów<br />pola<br />parameters |Zdefiniuj spout nietransakcyjne. Uruchomieniu aplikacji z ***nazwa exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** jest pól danych wyjściowych dla spout<br /><br />***Parametrów*** są opcjonalne, używany do określenia niektórych parametrów, takich jak "nontransactional.ack.enabled". |
| **bolt punkt połączenia usługi** |exec — nazwa<br />argumentów<br />pola<br />parameters |Zdefiniuj nietransakcyjne Bolt. Uruchomieniu aplikacji z ***nazwa exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** jest pól danych wyjściowych dla bolt<br /><br />***Parametrów*** są opcjonalne, używany do określenia niektórych parametrów, takich jak "nontransactional.ack.enabled". |

SCP.NET ma następujące słowa kluczowe zdefiniowane:

| **Słowa kluczowe** | **Opis** |
| --- | --- |
| **: Nazwa** |Zdefiniuj nazwę topologii |
| **: topologii** |Zdefiniuj topologię za pomocą funkcji poprzednie i kompilacji w tych. |
| **: p** |Zdefiniuj wskazówka równoległości dla każdego spout lub bolt. |
| **: config** |Zdefiniuj parametr skonfigurować lub zaktualizować istniejące |
| **: schematu** |Zdefiniuj schemat strumienia. |

I często używanych parametrów:

| **Parametr** | **Opis** |
| --- | --- |
| **"plugin.name"** |Nazwa pliku exe wtyczki C# |
| **"plugin.args"** |argumenty wtyczki |
| **"output.schema"** |Schemat danych wyjściowych |
| **"nontransactional.ack.enabled"** |Czy potwierdzenia jest włączone dla topologii nietransakcyjne |

Polecenie runspec jest wdrażany razem z usługi bits, jest użycie, takich jak:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***Zasobów dir*** parametr jest opcjonalny, musisz podać go, jeśli chcesz dołączyć C\# aplikacji, a ten katalog zawiera aplikację, zależności i konfiguracji.

***Ścieżki klasy*** parametr również jest opcjonalny. Służy do określenia klasy Java, jeśli plik specyfikacji zawiera Java Spout lub Bolt.

## <a name="miscellaneous-features"></a>Różne funkcje
### <a name="input-and-output-schema-declaration"></a>Danych wejściowych i wyjściowych schematu deklaracji
Użytkownicy mogą wysyłać krotek w języku C\# procesów, platforma musi być serializuje spójna kolekcja znajdująca się na byte [], Przenieś stronę Java i Storm przekaże to tuple elementy docelowe. W tym samym czasie w składniki podrzędne, C\# procesy będą otrzymywać krotek powrotem po stronie java i przekonwertować go do oryginalnego typów przez platformę, wszystkie czynności są ukryte przez platformę.

Do obsługi serializacji i deserializacji, kod użytkownika musi zadeklarować schemat danych wejściowych i wyjściowych.

Schemat wejścia/wyjścia strumień jest zdefiniowany jako słownik. Klucz jest StreamId. Wartość jest typów kolumn. Składnik może mieć wielu strumieni zadeklarowany.

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


Obiekt kontekstu mamy następujący interfejs API dodany:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Deweloperzy muszą zapewnić, że krotek wysyłanego przestrzegać schematu zdefiniowane dla tego strumienia, w przeciwnym razie system zgłosi wyjątek czasu wykonywania.

### <a name="multi-stream-support"></a>Obsługa wielu strumienia
Punkt połączenia usługi obsługuje kod użytkownika Emituj lub odebrać z wielu różnych strumieni w tym samym czasie. Obsługa odzwierciedla w kontekście obiektu jako metoda emitowanie korzysta z parametru Identyfikatora strumienia opcjonalne.

Dodano dwie metody w obiekcie SCP.NET kontekstu. Są one używane do wysyłania spójnej kolekcji lub krotek, aby określić StreamId. StreamId jest ciągiem i musi być zgodne w obu C\# i użycie definicji topologii.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Emitowanie strumień nieistniejącego powoduje, że wyjątki czasu wykonywania.

### <a name="fields-grouping"></a>Grupowanie pól
Grupowanie pól wbudowanych w Strom nie działa prawidłowo w SCP.NET. Po stronie serwera Proxy Java wszystkich typów danych pola są faktycznie byte [], a pola Grupowanie używa typu byte [] obiektu skrótu do wykonania grupowanie. Byte [] obiektu skrótu jest adres tego obiektu w pamięci. To grupowanie będą nieprawidłowe dla dwóch byte [] obiektów, które mają tę samą zawartość, ale nie sam adres.

SCP.NET dodaje metodę grupowania dostosowane i używa zawartości byte [] do grupowania. W **SPEC** pliku przypomina składnię:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Tutaj

1. "punkt połączenia usługi pola group" oznacza "Grupowania pola niestandardowe implementowane przez punkt połączenia usługi".
2. ": tx"lub":-tx" oznacza, że jeśli jest transakcyjna topologii. Potrzebujemy tych informacji, ponieważ indeks początkowy a topologie-tx różni się w tx.
3. [0,1] oznacza, że zestaw skrót identyfikatorów pola, począwszy od 0.

### <a name="hybrid-topology"></a>Topologia hybrydowego
Natywny Storm napisano w języku Java. I SCP.Net ma rozszerzone go w celu włączenia C\# deweloperom pisanie C\# kod, aby obsłużyć ich logiki biznesowej. Ale obsługuje ona również hybrydowe topologie, która zawiera nie tylko C\# elementów elementach spout/bolt, ale również elementów Java Spout/Bolt.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Określ Java Spout/Bolt w specyfikacji pliku
W specyfikacji pliku "punkt połączenia usługi spout" i "punkt połączenia usługi bolt" można również określić Java Spouts i elementów Bolt, Oto przykład:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

W tym miejscu `microsoft.scp.example.HybridTopology.Generator` jest nazwa klasy Java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Określ klasy Java w runSpec polecenia
Jeśli chcesz przesłać topologii zawierające Java Spouts lub elementów Bolt, należy najpierw skompilować Java Spouts lub elementów Bolt i Pobierz pliki Jar. Następnie należy określić klasy java, który zawiera pliki Jar podczas przesyłania topologii. Oto przykład:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

W tym miejscu **przykłady\\HybridTopology\\java\\docelowej\\**  jest folder zawierający plik Jar Spout/Bolt Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializacja i deserializacja między Java i C\#
Składnik punktu połączenia usługi zawiera stronie Java i C\# po stronie. Aby pracować interaktywnie z natywnego języka Java elementach Spout/elementów Bolt, serializacji/deserializacji przeprowadza się między stronie Java i C\# po stronie, jak przedstawiono na wykresie.

![Diagram składnika java wysyłanie do wysyłania do składnika Java składnika punkt połączenia usługi](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serializacja w stronie Java i deserializacji w języku C\# po stronie**
   
   Najpierw firma Microsoft udostępnia domyślną implementację do serializacji w języku Java po stronie i deserializacji w języku C\# po stronie. Metoda serializacji w języku Java po stronie można określić w specyfikacji pliku:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoda deserializacji w języku C\# po stronie powinny być określone w języku C\# kod użytkownika:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Ta domyślna implementacja powinna obsługiwać najczęściej podany typ danych nie jest zbyt złożony. Dla niektórych przypadków, ponieważ typem danych użytkownika jest zbyt złożony lub funkcjonowania naszych Domyślna implementacja nie spełnia wymagań użytkownika, użytkownicy mogą wtyczki realizacji.
   
   Interfejs serializacja po stronie java jest zdefiniowany jako:
   
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
2. **Serializacji w języku C\# po stronie i deserializacji po stronie Java**
   
   Metoda serializacji w języku C\# po stronie powinny być określone w języku C\# kod użytkownika:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   W języku Java po stronie metody deserializacji powinny być określone w specyfikacji pliku:
   
     (punkt połączenia usługi spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Tutaj "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" jest nazwą Deserializator i "microsoft.scp.example.HybridTopology.Person" to klasa docelowa danych jest deserializacji do.
   
   Użytkownika można także podłączyć w wykonywaniu c\# serializator i Deserializator Java. Ten kod jest interfejs dla C\# serializator:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Ten kod jest interfejsem Deserializator Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Tryb punktu połączenia usługi hosta
W tym trybie użytkownika można skompilować ich kody do biblioteki DLL i umożliwia SCPHost.exe dostarczonych przez punkt połączenia usługi przesyłania topologii. Specyfikacji pliku wygląda ten kod:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

W tym miejscu `plugin.name` jest określony jako `SCPHost.exe` dostarczonych przez punkt połączenia usługi SDK. SCPHost.exe akceptuje trzy parametry:

1. Pierwsza z nich jest nazwa biblioteki DLL, która jest `"HelloWorld.dll"` w tym przykładzie.
2. Drugim jest nazwy klasy, która jest `"Scp.App.HelloWorld.Generator"` w tym przykładzie.
3. Trzeci jest nazwą publicznej metody statycznej, który może być wywoływany można pobrać wystąpienia ISCPPlugin.

W trybie hosta kod użytkownika jest skompilowana jako biblioteki DLL i jest wywoływane przez platformę punkt połączenia usługi. Dlatego platformy punkt połączenia usługi można uzyskać pełną kontrolę nad logika przetwarzania całego. Dlatego zaleca się naszym klientom przesyłania topologii w trybie hosta punktu połączenia usługi, ponieważ może uprościć proces tworzenia i przełączyć nam większą elastyczność i lepszą zgodność z poprzednimi wersjami oraz nowszych wersji.

## <a name="scp-programming-examples"></a>Przykłady programowania w języku punkt połączenia usługi
### <a name="helloworld"></a>HelloWorld
**HelloWorld** prosty przykład pokazanie smak SCP.Net. Za pomocą topologii nietransakcyjnej i spout, nazywany **generator**i dwóch elementów bolt o nazwie **podziału** i **licznika**. Spout **generator** losowo generuje zdań i wyemitować tych zdań **podziału**. Elementy bolt **podziału** dzieli zdań wyrazy i wysyłać te słowa do **licznika** bolt. Bolt "licznika" używa słownika, aby zarejestrować numer wystąpienia każdego wyrazu.

Istnieją dwa pliki specyfikacji, **HelloWorld.spec** i **HelloWorld\_EnableAck.spec** w tym przykładzie. W języku C\# kodu, jego można ustalić, czy potwierdzenia jest włączona, pobierając pluginConf z strony Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

W spout po włączeniu potwierdzenia słownik jest używane do pamięci podręcznej krotek, które nie zostały jeszcze prześledzone. Jeśli po wywołaniu Fail() jest odtwarzany krotki nie powiodło się:

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
**HelloWorldTx** przykładzie pokazano sposób wdrożenia transakcyjnego topologii. Ma on jeden spout o nazwie **generator**, bolt partii o nazwie **partial-count**, i wywołuje bolt zatwierdzania **Suma liczba**. Istnieją również trzy pliki txt wstępnie utworzone: **DataSource0.txt**, **DataSource1.txt**, i **DataSource2.txt**.

W każdej transakcji spout **generator** losowo wybiera dwa pliki z wstępnie utworzone trzy pliki i Emituj nazw dwóch plików do **partial-count** bolt. Elementy bolt **partial-count** pobiera plik nazwy z odebrane spójnej kolekcji, a następnie otwórz plik i liczbę słów w tym pliku i ostatecznie Emituj numer programu word do **Suma liczba** bolt. **Suma liczba** bolt zawiera podsumowanie liczby całkowitej.

Do osiągnięcia **dokładnie raz** semantyki, bolt zatwierdzania **Suma liczba** należy ocenić, czy jest powtórzonym transakcji. W tym przykładzie ma statycznym elementem członkowskim zmiennej:

    public static long lastCommittedTxId = -1; 

Po utworzeniu wystąpienia ISCPBatchBolt pobiera `txAttempt` z parametrów wejściowych:

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

Gdy `FinishBatch()` jest nazywany `lastCommittedTxId` zostanie zaktualizowany, jeśli nie jest transakcją powtórzony.

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
Ta topologia zawiera elementów Spout Java i C\# Bolt. Domyślna implementacja serializacji i deserializacji dostarczane przez platformę punkt połączenia usługi jest używany. Zobacz **HybridTopology.spec** w **przykłady\\HybridTopology** folder szczegółów specyfikacji pliku i **SubmitTopology.bat** dla sposobu określania Java Ścieżka klasy.

### <a name="scphostdemo"></a>SCPHostDemo
W tym przykładzie jest taka sama jak HelloWorld w zasadzie. Jedyna różnica polega na kompilowania kodu użytkownika jako biblioteki DLL, a topologia jest przesyłany za pomocą SCPHost.exe. Zobacz sekcję "Punkt połączenia usługi hosta tryb" Aby uzyskać bardziej szczegółowy opis.

## <a name="next-steps"></a>Następne kroki
Przykłady topologii Storm utworzone przy użyciu połączenia usługi można znaleźć w następujących dokumentach:

* [Tworzenie topologii C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Zdarzenia procesu z usługi Azure Event Hubs z systemu Storm w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Przetwarzania danych czujnika vehicle z usługi Event Hubs za pomocą Storm w usłudze HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Wyodrębniania, transformacji i ładowania (ETL) z usługi Azure Event Hubs do bazy danych HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
