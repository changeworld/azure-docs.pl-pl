---
title: Przewodnik programowania SCP.NET na potrzeby burzy w usłudze Azure HDInsight
description: Dowiedz się, jak utworzyć program przy użyciu programu SCP.NET. Topologie burzowe oparte na sieci, które są używane z działaniem burzy w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154486"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Przewodnik programowania SCP dla Apache Storm w usłudze Azure HDInsight

SCP to platforma do kompilowania w czasie rzeczywistym, niezawodnej, spójnej i wysokiej wydajności aplikacji do przetwarzania danych. Jest on zbudowany na podstawie [Apache Storm](https://storm.incubator.apache.org/) --a systemu przetwarzania strumienia zaprojektowanego przez społeczności OSS. Burza jest zaprojektowana przez Nathana marza i została otwarta przez serwis Twitter. Wykorzystuje [Apache ZooKeeper](https://zookeeper.apache.org/), innym projekcie Apache, aby zapewnić wysoce niezawodne dystrybuowanie rozproszonej koordynacji i zarządzania stanami.

Nie tylko projekt SCP jest przeszukiwany w systemie Windows, ale również dodaliśmy do projektu rozszerzenia i dostosowania ekosystemu systemu Windows. Rozszerzenia obejmują środowisko deweloperskie platformy .NET i biblioteki, dostosowanie obejmuje wdrożenie oparte na systemie Windows.

Rozszerzenie i dostosowanie są wykonywane w taki sposób, że nie musimy rozwidlenia projektów OSS i możemy wykorzystać ekosystemy pochodne utworzone w oparciu o burze.

## <a name="processing-model"></a>Model przetwarzania

Dane w usłudze SCP są modelowane jako ciągłe strumienie krotek. Zazwyczaj krotki przepływają do pewnej kolejki, a następnie są pobierane i przekształcane przez logikę biznesową hostowaną w topologii burzy, na koniec mogą być potoki jako krotki do innego systemu SCP lub być zatwierdzone do magazynów, takich jak rozproszonego systemu plików lub baz danych. takie jak SQL Server.

![Diagram przedstawiający dane do przetworzenia w kolejce, w której są przechowywane źródła danych](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

W obszarze burza Topologia aplikacji definiuje wykres obliczeń. Każdy węzeł w topologii zawiera logikę przetwarzania, a linki między węzłami wskazują przepływ danych. Węzły, które mają wstrzyknąć dane wejściowe do topologii, są nazywane _elementy Spout_, które mogą służyć do sekwencjonowania danych. Dane wejściowe mogą znajdować się w dziennikach plików, transakcyjnych bazach danych, liczniku wydajności systemu i tak dalej. Węzły z przepływem danych wejściowych i wyjściowych są nazywane _piorunami_, które wykonują rzeczywiste filtrowanie danych i ich wybór i agregację.

Punkt połączenia usługi obsługuje najlepsze wysiłki, co najmniej raz i dokładnie po przetwarzaniu danych. W aplikacji przetwarzania strumieniowego rozproszonego mogą wystąpić różne błędy podczas przetwarzania danych, takie jak awaria sieci, awaria maszyny lub błąd kodu użytkownika itd. Co najmniej jednokrotne przetwarzanie gwarantuje, że wszystkie dane zostaną przetworzone co najmniej raz przez odtwarzanie automatycznie tych samych danych, gdy wystąpi błąd. Przetwarzanie co najmniej raz jest proste i niezawodne i ma wiele zastosowań. Jeśli jednak aplikacja wymaga dokładnego zliczania, to co najmniej jednokrotne przetwarzanie jest niewystarczające, ponieważ te same dane mogą być potencjalnie odtwarzane w topologii aplikacji. W takim przypadku należy dokładnie raz wykonać przetwarzanie, aby upewnić się, że wynik jest poprawny, nawet jeśli dane mogą być odtwarzane i przetwarzane wiele razy.

Usługa SCP umożliwia deweloperom platformy .NET opracowywanie aplikacji do przetwarzania danych w czasie rzeczywistym, jednocześnie wykorzystując wirtualna maszyna Java (JVM) z burzą w ramach okładek. .NET i JVM komunikują się za pośrednictwem lokalnych gniazd TCP. Zasadniczo każda elementu Spout/piorun to para procesów .NET/Java, w której logika użytkownika jest uruchamiana w procesie .NET jako wtyczka.

Aby utworzyć aplikację do przetwarzania danych na punkcie połączenia usługi, wymagane są kilka czynności:

* Projektuj i Implementuj elementy Spout, aby ściągnąć dane z kolejki.
* Projektuj i Implementuj pioruny, aby przetwarzać dane wejściowe i zapisywać dane w sklepach zewnętrznych, takich jak baza danych.
* Zaprojektowanie topologii, a następnie przesłanie i uruchomienie topologii. Topologia definiuje wierzchołki i dane między wierzchołkami. Punkt połączenia usługi będzie przyjmować specyfikację topologii i wdrożyć ją w klastrze burzowym, gdzie każdy wierzchołek jest uruchamiany w jednym węźle logicznym. Przełączenie w tryb failover i skalowanie będzie obsługiwane przez harmonogram zadań burzy.

W tym dokumencie przedstawiono niektóre proste przykłady umożliwiające tworzenie aplikacji do przetwarzania danych za pomocą punktu połączenia usługi.

## <a name="scp-plugin-interface"></a>Interfejs wtyczki SCP

Wtyczki usługi SCP (lub aplikacje) to autonomiczna exe, która może być uruchamiana w programie Visual Studio podczas fazy tworzenia, i być podłączona do potoku burzy po wdrożeniu w środowisku produkcyjnym. Pisanie wtyczki SCP jest tak samo samo jak w przypadku pisania innych standardowych aplikacji konsolowych systemu Windows. Platforma SCP.NET deklaruje jakiś interfejs dla elementu Spout/pioruna, a kod wtyczki użytkownika powinien implementować te interfejsy. Głównym celem tego projektu jest to, że użytkownik może skupić się na własnych logikach firmy i pozostawia inne rzeczy, które mają być obsługiwane przez platformę SCP.NET.

Kod wtyczki użytkownika powinien implementować jeden z następujących interfejsów, zależy od tego, czy topologia jest transakcyjna, czy nietransakcyjna, oraz czy składnik jest elementu Spout czy piorunem.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin jest wspólnym interfejsem dla wszystkich rodzajów wtyczek. Obecnie jest to fikcyjny interfejs.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout jest interfejsem dla nietransakcyjnych elementu Spout.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Gdy `NextTuple()` jest wywoływana, kod C# użytkownika może emitować jedną lub kilka krotek. Jeśli nie ma niczego do emisji, ta metoda powinna zwracać bez emitowania żadnych elementów. Należy zauważyć, że `NextTuple()`, `Ack()`i `Fail()` są wywoływane w ścisłej pętli w C# procesie. Gdy nie ma krotek do emisji, jest to courteous, że NextTuple uśpienie przez krótki czas (na przykład 10 milisekund), tak aby nie tracić zbyt dużej ilości zasobów procesora CPU.

`Ack()` i `Fail()` są wywoływane tylko wtedy, gdy mechanizm ACK jest włączony w pliku specyfikacji. `seqId` służy do identyfikowania spójnej kolekcji lub jej niepowodzenie. Dlatego jeśli ACK jest włączony w topologii nietransakcyjnej, w elementu Spout należy używać następującej funkcji emisji:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Jeśli ACK nie jest obsługiwany w topologii nietransakcyjnej, `Ack()` i `Fail()` można pozostawić jako pustą funkcję.

`parms` parametr wejściowy w tych funkcjach jest pustym słownikiem, jest zarezerwowany do użytku w przyszłości.

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt jest interfejsem dla nietransakcyjnego pioruna.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Gdy jest dostępna nowa krotka, funkcja `Execute()` jest wywoływana, aby ją przetworzyć.

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout jest interfejsem dla transakcyjnego elementu spoutu.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Podobnie jak w przypadku nietransakcyjnej części licznika, `NextTx()`, `Ack()`i `Fail()` są wywoływane w ścisłej pętli w ramach pojedynczego wątku w C# procesie. Gdy nie ma żadnych danych do emisji, jest to courteous `NextTx` uśpienia przez krótki czas (10 milisekund), tak aby nie tracić zbyt dużo procesora CPU.

`NextTx()` jest wywoływana w celu uruchomienia nowej transakcji, `seqId` parametru out służy do identyfikowania transakcji, która również jest używana w `Ack()` i `Fail()`. W `NextTx()`użytkownik może emitować dane po stronie Java. Dane są przechowywane w dozorcy, aby umożliwić odtwarzanie. Ponieważ pojemność dozorcy jest ograniczona, użytkownik powinien jedynie emitować metadane, a nie dane zbiorcze w transakcyjnych elementu Spout.

Burza automatycznie powtarza transakcję, jeśli zakończy się niepowodzeniem, dlatego `Fail()` nie powinna być wywoływana w normalnym przypadku. Ale jeśli punkt połączenia usługi może sprawdzić metadane emitowane przez elementu Spout transakcyjny, może wywołać `Fail()`, gdy metadane są nieprawidłowe.

`parms` parametr wejściowy w tych funkcjach jest pustym słownikiem, jest zarezerwowany do użytku w przyszłości.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt jest interfejsem dla błyskawicy transakcyjnej.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` jest wywoływana, gdy nowa krotka dociera do obiektu. `FinishBatch()` jest wywoływana, gdy transakcja zostanie zakończona. `parms` parametr wejściowy jest zarezerwowany do użytku w przyszłości.

W przypadku topologii transakcyjnej istnieje ważna koncepcja — `StormTxAttempt`. Ma dwa pola, `TxId` i `AttemptId`. `TxId` służy do identyfikowania konkretnej transakcji, a dla danej transakcji może istnieć wiele prób, jeśli transakcja nie powiedzie się i zostanie odtworzony. SCP.NET tworzy nowy obiekt ISCPBatchBolt do przetwarzania poszczególnych `StormTxAttempt`, podobnie jak w przypadku środowiska Java. Celem tego projektu jest obsługa przetwarzania transakcji równoległych. Użytkownik powinien pamiętać, że jeśli zostanie podjęta próba transakcji, odpowiedni obiekt ISCPBatchBolt zostaje zniszczony i wyrzucony.

## <a name="object-model"></a>Model obiektów

Program SCP.NET udostępnia również prosty zestaw obiektów kluczowych dla deweloperów, którzy mają być używani. Są one **kontekstowe**, **stan klientów**i **SCPRuntime**. Są one omówione w części REST tej sekcji.

### <a name="context"></a>Kontekst

Kontekst zapewnia uruchomione środowisko dla aplikacji. Każde wystąpienie ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) ma odpowiednie wystąpienie kontekstu. Funkcje zapewniane przez kontekst można podzielić na dwie części: (1) części statycznej, która jest dostępna w całym C# procesie, (2) części dynamicznej, która jest dostępna tylko dla określonego wystąpienia kontekstu.

### <a name="static-part"></a>Część statyczna

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` podano do celów dzienników.

`pluginType` jest używany do wskazania typu wtyczki C# procesu. Jeśli C# proces jest uruchamiany w trybie testowania lokalnego (bez języka Java), typ wtyczki jest `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` można pobrać parametrów konfiguracyjnych ze strony języka Java. Parametry są przesyłane ze strony Java po C# zainicjowaniu wtyczki. `Config` parametry są podzielone na dwie części: `stormConf` i `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` są parametrami zdefiniowanymi przez burzę, a `pluginConf` są parametrami zdefiniowanymi przez punkt połączenia usługi. Przykład:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` jest udostępniany w celu uzyskania kontekstu topologii, jest najbardziej przydatny w przypadku składników o wielu równoległości. Oto przykład:

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

### <a name="dynamic-part"></a>Część dynamiczna

Poniższe interfejsy są powiązane z określonym wystąpieniem kontekstu. Wystąpienie kontekstu jest tworzone przez platformę SCP.NET i przesyłane do kodu użytkownika:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

W przypadku nietransakcyjnego potwierdzenia elementu Spout jest dostępna następująca metoda:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

W przypadku nietransakcyjnego potwierdzenia, powinien on jawnie `Ack()` lub `Fail()`j spójnej kolekcji. I podczas emitowania nowej spójnej kolekcji, należy również określić kotwice nowej krotki. Podano następujące metody.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>Stan klientów

`StateStore` zapewnia usługi metadanych, generowanie sekwencji monotoniczny i niezależną współpracę. Rozproszone abstrakcyjne abstrakcje współbieżności można kompilować na `StateStore`, w tym blokad rozproszonych, kolejkach rozproszonych, barier i usług transakcyjnych.

Aplikacje SCP mogą używać obiektu `State`, aby zachować niektóre informacje w [Apache ZooKeeper](https://zookeeper.apache.org/), szczególnie w przypadku topologii transakcyjnej. Wykonanie tej czynności, jeśli transakcyjny elementu Spout ulega awarii i ponowne uruchomienie, może pobrać niezbędne informacje z dozorcy i ponownie uruchomić potok.

Obiekt `StateStore` ma głównie następujące metody:

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

Obiekt `State` ma głównie następujące metody:

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

Dla metody `Commit()`, gdy simplemode ma wartość true, usuwa odpowiednie ZNode w dozorcy. W przeciwnym razie usuwa bieżącą ZNode i dodaje nowy węzeł w ZATWIERDZONEj ścieżce\_.

### <a name="scpruntime"></a>SCPRuntime

SCPRuntime udostępnia dwie następujące metody:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` jest używany do inicjowania środowiska uruchomieniowego SCP. W tej metodzie C# proces nawiązuje połączenie ze stroną Java i pobiera parametry konfiguracji i kontekst topologii.

`LaunchPlugin()` jest używany do uruchamiania pętli przetwarzania komunikatów. W tej pętli C# wtyczka odbiera komunikaty formularz w języku Java (w tym krotki i sygnały kontrolne), a następnie przetwarza komunikaty, na przykład wywołanie metody interfejsu przez kod użytkownika. Parametr wejściowy metody `LaunchPlugin()` jest delegatem, który może zwrócić obiekt, który implementuje interfejs ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

W przypadku ISCPBatchBolt możemy uzyskać `StormTxAttempt` z `parms`i użyć go do oceny, czy jest to powtórzona próba. Sprawdzenie, czy próba powtarzania jest często wykonywana przy użyciu pioruna i przedstawiono w `HelloWorldTx` przykładzie.

Ogólnie mówiąc, wtyczki punktu połączenia usługi mogą działać w dwóch trybów tutaj:

1. Tryb testu lokalnego: w tym trybie wtyczki usługi SCP (kod C# użytkownika) działają w programie Visual Studio podczas fazy tworzenia. `LocalContext` można używać w tym trybie, który zapewnia metodę serializacji emitowanych krotek do plików lokalnych i odczytuje je z powrotem do pamięci.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Tryb zwykły: w tym trybie wtyczki usługi SCP są uruchamiane przez proces burzy języka Java.

    Oto przykład uruchamiania wtyczki SCP:

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

## <a name="topology-specification-language"></a>Język specyfikacji topologii

Specyfikacja topologii punktu połączenia usługi to język specyficzny dla domeny do opisywania i konfigurowania topologii punktu połączenia. Jest on oparty na Clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) i jest rozszerzany przez punkt połączenia usługi.

Specyfikacje topologii można przesłać bezpośrednio do klastra burzy do wykonania za pomocą polecenia ***runspec*** .

SCP.NET dodał następujące funkcje, aby zdefiniować topologie transakcyjne:

| Nowe funkcje | Parametry | Opis |
| --- | --- | --- |
| TX-topolopy |Topologia — nazwa<br />elementu Spout — Mapa<br />Mapa błyskawicy |Zdefiniuj topologię transakcyjną z nazwą topologii, &nbsp;Mapa definicji elementy Spout oraz mapa definicji błyskawicy |
| SCP-TX-elementu Spout |exec-Name<br />args<br />pola |Zdefiniuj elementu Spout transakcyjny. Uruchamia aplikację z ***nazwą exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** są polami danych wyjściowych dla elementu Spout |
| SCP-TX-Batch |exec-Name<br />args<br />pola |Definiowanie transakcyjnego pioruna partii. Uruchamia aplikację z ***nazwą exec*** przy użyciu ***argumentów.***<br /><br />Pola są polami wynikowymi dla elementu piorun. |
| SCP-TX-commit-Piorun |exec-Name<br />args<br />pola |Zdefiniuj pioruna zatwierdzeń transakcyjnych. Uruchamia aplikację z ***nazwą exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** są polami wynikowymi dla elementu Piorun |
| nontx-topolopy |Topologia — nazwa<br />elementu Spout — Mapa<br />Mapa błyskawicy |Zdefiniuj nietransakcyjną topologię z nazwą topologii,&nbsp; Mapa definicji elementy Spout oraz mapa definicji błyskawicy |
| SCP — elementu Spout |exec-Name<br />args<br />pola<br />parameters |Zdefiniuj nietransakcyjny elementu Spout. Uruchamia aplikację z ***nazwą exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** są polami danych wyjściowych dla elementu Spout<br /><br />***Parametry*** są opcjonalne, przy użyciu których można określić niektóre parametry, takie jak "nietransakcyjny. ACK. Enabled". |
| punkt połączenia usługi |exec-Name<br />args<br />pola<br />parameters |Zdefiniuj piorun nietransakcyjny. Uruchamia aplikację z ***nazwą exec*** przy użyciu ***argumentów***.<br /><br />***Pola*** są polami wynikowymi dla elementu Piorun<br /><br />***Parametry*** są opcjonalne, przy użyciu których można określić niektóre parametry, takie jak "nietransakcyjny. ACK. Enabled". |

SCP.NET ma zdefiniowane następujące słowa kluczowe:

| Słowa kluczowe | Opis |
| --- | --- |
| : Nazwa |Zdefiniuj nazwę topologii |
| : topologia |Zdefiniuj topologię przy użyciu poprzednich funkcji i skompiluj w nich. |
| :p |Zdefiniuj wskazówkę równoległą dla każdego elementu spoutu lub pioruna. |
| : config |Zdefiniuj parametr Configure lub zaktualizuj istniejące |
| : schemat |Zdefiniuj schemat strumienia. |

I często używane parametry:

| Parametr | Opis |
| --- | --- |
| "plugin.name" |Nazwa pliku exe C# wtyczki |
| "wtyczka. args" |argumenty wtyczki |
| "Output. Schema" |Schemat danych wyjściowych |
| "nietransakcyjny. ACK. Enabled" |Czy ACK jest włączony dla topologii nietransakcyjnej |

Polecenie runspec jest wdrażane wraz z usługą BITS, użycie jest podobne do:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Parametr ***Resource-dir*** jest opcjonalny, należy go określić, gdy chcesz podłączyć C# aplikację, a ten katalog zawiera aplikację, zależności i konfiguracje.

Parametr ***ścieżki klas*** jest również opcjonalny. Służy do określania ścieżki klas Java, jeśli plik specyfikacji zawiera Java elementu Spout lub piorun.

## <a name="miscellaneous-features"></a>Różne funkcje

### <a name="input-and-output-schema-declaration"></a>Deklaracja schematu danych wejściowych i wyjściowych

Użytkownicy mogą emitować krotki w C# procesach, a platforma musi serializować krotkę do Byte [], przenieść do strony Java, a burza przeniesie Tę spójną krotkę do obiektów docelowych. W przypadku C# składników podrzędnych procesy będą odbierać krotki z powrotem po stronie Java i konwertowane na oryginalne typy według platformy, a wszystkie te operacje są ukrywane na platformie.

Aby zapewnić obsługę serializacji i deserializacji, kod użytkownika musi zadeklarować schemat danych wejściowych i wyjściowych.

Schemat strumienia danych wejściowych/wyjściowych jest zdefiniowany jako słownik. Klucz jest obecny streamid. Wartość jest typami kolumn. Składnik może mieć zadeklarowane wiele strumieni.

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


W obiekcie kontekstu mamy dodany następujący interfejs API:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Deweloperzy muszą upewnić się, że krotki emitowane zgodnie ze schematem zdefiniowanym dla tego strumienia, w przeciwnym razie system zgłosi wyjątek czasu wykonywania.

### <a name="multi-stream-support"></a>Obsługa WIELOSTRUMIENIOWA

Punkt połączenia usługi obsługuje kod użytkownika, aby emitować lub odbierać z wielu odrębnych strumieni jednocześnie. Obsługa jest odzwierciedlana w obiekcie kontekstu, ponieważ metoda emisji przyjmuje opcjonalny parametr identyfikatora strumienia.

Dodano dwie metody z obiektu kontekstu SCP.NET. Są one używane do emitowania krotek lub krotek w celu określenia obecny streamid. Obecny streamid jest ciągiem i musi być spójna zarówno C# w specyfikacji definicji topologii.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

Emitowanie do nieistniejącego strumienia powoduje wyjątki środowiska uruchomieniowego.

### <a name="fields-grouping"></a>Grupowanie pól

Wbudowane grupowanie pól w obszarze burza nie działa prawidłowo w SCP.NET. Po stronie serwera proxy Java wszystkie typy danych pól są w rzeczywistości bajtowe [], a grupowanie pól używa kodu skrótu obiektu Byte [] do przeprowadzenia grupowania. Kod skrótu obiektu Byte [] jest adresem tego obiektu w pamięci. Dlatego grupowanie będzie niewłaściwe dla dwubajtowych obiektów, które współużytkują tę samą zawartość, ale nie tego samego adresu.

SCP.NET dodaje dostosowaną metodę grupowania i używa zawartości Byte [] do wykonania grupowania. W pliku **specyfikacji** , składnia jest następująca:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

Etapie

1. "SCP-Field-Group" oznacza "dostosowane grupowanie pól zaimplementowane przez punkt połączenia usługi".
2. ": TX" lub ": non-TX" oznacza, że jest to topologia transakcyjna. Potrzebujemy tych informacji, ponieważ indeks początkowy jest inny w topologii TX a inne niż TX.
3. [0, 1] oznacza zestaw skrótów identyfikatorów pól, rozpoczynając od 0.

### <a name="hybrid-topology"></a>Topologia hybrydowa

Natywna burza jest zapisywana w języku Java. Program SCP.NET został ulepszony, aby C# umożliwić deweloperom C# pisanie kodu do obsługi logiki biznesowej. Ale obsługuje również topologie hybrydowe, które zawierają nie C# tylko elementy Spout/pioruny, ale również Java elementu Spout/piorunów.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Określ kod języka Java elementu Spout/piorun w pliku specyfikacji

W pliku specyfikacji "SCP-elementu Spout" i "SCP-piorun" można również użyć do określenia języka Java elementy Spout i piorunów, poniżej przedstawiono przykład:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Tutaj `microsoft.scp.example.HybridTopology.Generator` jest nazwą klasy Java elementu Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Określ ścieżkę klas Java w runSpec polecenie

Jeśli chcesz przesłać topologię zawierającą elementy Spout Java lub piorunów, musisz najpierw skompilować środowisko Java elementy Spout lub pioruny i pobrać pliki jar. Następnie należy określić ścieżkę klasy Java, która zawiera pliki jar podczas przesyłania topologii. Oto przykład:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Poniżej przedstawiono **przykłady\\HybridTopology\\java\\target\\** jest folderem zawierającym plik JAR elementu Spout/pioruna języka Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializacja i deserializacja między językami Java iC#

Składnik punktu połączenia usługi zawiera po C# stronie środowiska Java. Aby można było korzystać z natywnych elementy spoutów języka Java, serializacji/deserializacji należy przeprowadzić między stroną Java a C# stroną, jak pokazano na poniższym wykresie.

![Diagram składnika Java, który wysyła do składnika punktu SCP wysyłanie do składnika Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Serializacja po stronie Java i deserializacji C# po stronie

   Najpierw udostępniamy domyślną implementację serializacji po stronie Java i deserializacji C# . Metodę serializacji w stronie Java można określić w pliku specyfikacji:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   Metoda deserializacji C# po stronie powinna być określona w C# kodzie użytkownika:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   Ta domyślna implementacja powinna obsługiwać większość przypadków, gdy typ danych nie jest zbyt skomplikowany. W niektórych przypadkach, ponieważ typ danych użytkownika jest zbyt skomplikowany lub wydajność implementacji domyślnej nie spełnia wymagań użytkownika, użytkownicy mogą podłączyć własną implementację.

   Serializowany interfejs na stronie Java jest zdefiniowany jako:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   Interfejs deserializacji C# po stronie jest zdefiniowany jako:

   ICustomizedInteropCSharpDeserializer interfejsu publicznego

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Serializacja C# po stronie i deserializacji na stronie Java

   Metoda serializacji C# po stronie powinna być określona w C# kodzie użytkownika:

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   Metoda deserializacji po stronie Java powinna być określona w pliku specyfikacji:

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   W tym miejscu "Microsoft. scp. burz. CustomizedInteropJSONDeserializer" jest nazwą deserializacji, a "Microsoft. scp. example. HybridTopology. Person" jest klasą docelową, do której dane są deserializowane.

   Użytkownik może również podłączyć własną implementację C# serializatorów i deserializacji Java. Ten kod jest interfejsem programu C# szeregującego:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Ten kod jest interfejsem dla deserializacji języka Java:

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Tryb hosta SCP

W tym trybie użytkownik może skompilować swoje kody do biblioteki DLL i użyć SCPHost. exe dostarczonej przez punkt połączenia usługi do przesyłania topologii. Plik specyfikacji wygląda podobnie do tego kodu:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Tutaj `plugin.name` jest określony jako `SCPHost.exe` udostępniany przez zestaw SCP SDK. SCPHost. exe akceptuje trzy parametry:

1. Pierwszym z nich jest nazwa biblioteki DLL, która jest `"HelloWorld.dll"` w tym przykładzie.
2. Druga z nich to nazwa klasy, która jest `"Scp.App.HelloWorld.Generator"` w tym przykładzie.
3. Trzecią jest nazwa publicznej metody statycznej, która może być wywoływana w celu uzyskania wystąpienia ISCPPlugin.

W trybie hosta kod użytkownika jest kompilowany jako biblioteka DLL i wywoływany przez platformę SCP. Dlatego platforma SCP może uzyskać pełną kontrolę nad całą logiką przetwarzania. Dlatego zalecamy naszym klientom przesyłanie topologii w trybie hosta usługi SCP, ponieważ może to uprościć środowisko programistyczne i zapewnić większą elastyczność i lepszą zgodność z poprzednimi wersjami.

## <a name="scp-programming-examples"></a>Przykłady programowania usługi SCP

### <a name="helloworld"></a>HelloWorld

**HelloWorld** to prosty przykład przedstawiający smak SCP.NET. Używa ona topologii nietransakcyjnej, z elementu Spout o nazwie **Generator**i dwóch piorunów o nazwie **rozdzielacza** i **licznika**. **Generator** elementu Spout losowo generuje zdania i emituje te zdania do **rozdzielacza**. Rozdzielacz * * dzielący zdania na słowa i emituje te wyrazy **do błyskawicy** . Piorun "licznik" używa słownika do rejestrowania numeru wystąpienia każdego wyrazu.

Istnieją dwa pliki specyfikacji **HelloWorld. spec** i **HelloWorld\_EnableAck. spec** w tym przykładzie. W C# kodzie można sprawdzić, czy ACK jest włączony, pobierając pluginConf ze strony Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

W elementu Spout, jeśli ACK jest włączona, słownik jest używany do buforowania krotek, które nie zostały potwierdzone. Jeśli operacja zakończy się niepowodzeniem (), niepomyślna krotka zostanie powtórzona:

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

W przykładzie **HelloWorldTx** pokazano, jak wdrożyć topologię transakcyjną. Ma jeden elementu Spout o nazwie **generatora**, obiekt wsadowy o nazwie **częściowa liczba**i obiekt zatwierdzający o nazwie **Count-sum**. Istnieją również trzy wstępnie utworzone pliki txt: **DataSource0. txt**, **DataSource1. txt**i **DataSource2. txt**.

W każdej transakcji **Generator** elementu Spout losowo wybiera dwa pliki z wstępnie utworzonych trzech plików i emitują dwie nazwy plików do pioruna **Count** . Wartość **częściowa "piorun-Count"** Pobiera nazwę pliku z otrzymanej spójnej kolekcji, a następnie otwiera plik i zlicza liczbę słów w tym pliku, a następnie emituje numer wyrazu do błyskawicy **licznika** . Wartość **licznika-suma** jest podsumowanie łącznej liczby.

Aby osiągnąć **dokładnie jednokrotne** semantykę, **Liczba** piorunów zatwierdzeń musi ocenić, czy jest to transakcja odtwarzana. W tym przykładzie ma statyczną zmienną członkowską:

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

Gdy `FinishBatch()` jest wywoływana, `lastCommittedTxId` zostanie zaktualizowana, jeśli nie jest to powtórzona transakcja.

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

Ta topologia zawiera elementu Spout Java i C# piorun. Używa domyślnej serializacji i deserializacji implementacji dostarczonej przez platformę SCP. Zobacz **HybridTopology. spec** in **przykłady\\HybridTopology** folder dla szczegółowych informacji o pliku i **SubmitTopology. bat** , aby określić, jak określać ścieżki klas Java.

### <a name="scphostdemo"></a>SCPHostDemo

Ten przykład jest taki sam jak HelloWorld. Jedyną różnicą jest to, że kod użytkownika jest kompilowany jako biblioteka DLL, a topologia jest przesyłana przy użyciu SCPHost. exe. Aby uzyskać bardziej szczegółowy opis, zobacz sekcję "tryb hosta usługi SCP".

## <a name="next-steps"></a>Następne kroki

Przykłady topologii Apache Storm utworzonych za pomocą usługi SCP można znaleźć w następujących dokumentach:

* [Tworzenie C# topologii dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Przetwarzanie danych z czujnika pojazdu z Event Hubs przy użyciu Apache Storm w usłudze HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do platformy Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
