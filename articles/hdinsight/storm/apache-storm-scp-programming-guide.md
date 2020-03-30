---
title: SCP.NET przewodnik programowania dla storm w usłudze Azure HDInsight
description: Dowiedz się, jak tworzyć SCP.NET za pomocą SCP.NET . Oparte na sieci topologie storm do użytku z storm uruchomiony w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186860"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Przewodnik po programowaniu protokołu SCP dla usługi Apache Storm w usłudze Azure HDInsight

Protokół SCP to platforma do tworzenia aplikacji do przetwarzania danych w czasie rzeczywistym, niezawodnych, spójnych i wydajnych. Jest zbudowany na szczycie [Apache Storm](https://storm.incubator.apache.org/), który jest systemem przetwarzania strumienia zaprojektowanym przez społeczności oprogramowania open source. Nathan Marz stworzył Storm. Został opublikowany jako open source przez Twitter. Storm wykorzystuje [Apache ZooKeeper](https://zookeeper.apache.org/), który jest kolejnym projektem Apache, który umożliwia wysoce niezawodną rozproszoną koordynację i zarządzanie państwem.

Projekt SCP został przeniesiony nie tylko Storm w systemie Windows, ale także rozszerzenia dodane do projektu i dostosowanie dla środowiska Windows. Rozszerzenia obejmują środowisko deweloperskie platformy .NET i biblioteki .NET. Dostosowanie obejmuje wdrożenie oparte na systemie Windows.

Dzięki rozszerzeniu i dostosowywaniu nie trzeba rozwidlenia projektów oprogramowania typu open source. Można użyć środowisk pochodnych, które są zbudowane na szczycie Storm.

## <a name="processing-model"></a>Model przetwarzania

Dane w SCP jest modelowany jako ciągłe strumienie krotek. Zazwyczaj krotek:

1. Przepływ do kolejki.
1. Są odbierane i przekształcane przez logikę biznesową hostowane wewnątrz topologii Storm.
1. Albo mają ich dane wyjściowe potoku jako krotek do innego systemu SCP lub są zobowiązane do magazynów, takich jak rozproszone systemy plików i baz danych, takich jak SQL Server.

![Diagram kolejki podajnika danych do przetwarzania, który z kolei zasila magazyn danych](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

W storm topologia aplikacji definiuje wykres obliczeń. Każdy węzeł w topologii zawiera logikę przetwarzania. Łącza między węzłami wskazują przepływ danych.

Węzły, które wstrzykują dane wejściowe do topologii, są nazywane _wylewkami_. Można ich używać do sekwencjonowania danych. Dane wejściowe mogą pochodzić ze źródła, takiego jak dzienniki plików, transakcyjna baza danych lub licznik wydajności systemu.

Węzły, które mają zarówno przepływ danych wejściowych, jak i _wyjściowych,_ są nazywane śrubami . Wykonują one rzeczywiste filtrowanie danych, selekcje i agregację.

Protokół SCP obsługuje najlepsze wysiłki, co najmniej raz i dokładnie raz po przetworzeniu danych.

W rozproszonej aplikacji przetwarzania strumienia błędy mogą wystąpić podczas przetwarzania danych. Takie błędy obejmują awarię sieci, awarię komputera lub błąd w kodzie. Przetwarzanie co najmniej raz gwarantuje, że wszystkie dane są przetwarzane co najmniej raz, automatycznie odtwarzając te same dane, gdy występuje błąd.

Przetwarzanie co najmniej raz jest proste i niezawodne i pasuje do wielu aplikacji. Ale gdy aplikacja wymaga dokładnego liczenia, co najmniej raz przetwarzania jest niewystarczająca, ponieważ te same dane mogą być odtwarzane w topologii aplikacji. W takim przypadku dokładnie raz przetwarzania upewnia się, że wynik jest poprawny, nawet wtedy, gdy dane są odtwarzane i przetwarzane wiele razy.

Protokół SCP umożliwia deweloperom platformy .NET tworzenie aplikacji do przetwarzania danych w czasie rzeczywistym podczas korzystania z maszyny wirtualnej Java (JVM) z usługą Storm. JVM i .NET komunikują się za pośrednictwem lokalnych gniazd TCP. Każda wylewka/śruba jest parą procesów .NET/Java, gdzie logika użytkownika jest uruchamiana w procesie .NET jako wtyczka.

Aby utworzyć aplikację przetwarzania danych na podstawie protokołu SCP, wykonaj następujące kroki:

1. Projektowanie i implementowanie wylewek do ściągania danych z kolejek.
1. Projektowanie i implementowanie śrub, które przetwarzają dane wejściowe i zapisują je w magazynach zewnętrznych, takich jak baza danych.
1. Zaprojektuj topologię, a następnie prześlij ją i uruchom.

Topologia definiuje wierzchołki i dane, które przepływa między nimi. Protokół SCP przyjmuje specyfikację topologii i wdraża ją w klastrze Storm, gdzie każdy wierzchołek jest uruchamiany w jednym węźle logicznym. Harmonogram zadań Storm zajmuje się pracy awaryjnej i skalowania.

W tym artykule użyto kilku prostych przykładów, aby przejść przez sposób tworzenia aplikacji przetwarzania danych za pomocą protokołu SCP.

## <a name="scp-plug-in-interface"></a>Interfejs wtyczki SCP

Wtyczki SCP są aplikacjami autonomicznymi. Mogą one działać wewnątrz programu Visual Studio podczas tworzenia i być podłączone do potoku storm po wdrożeniu produkcyjnym.

Zapisywanie wtyczki SCP jest takie samo jak pisanie dowolnej innej aplikacji konsoli systemu Windows. Platforma SCP.NET deklaruje niektóre interfejsy dla dziobka/śruby. Kod wtyczki implementuje te interfejsy. Głównym celem tego projektu jest umożliwienie skupienia się na logice biznesowej, pozwalając jednocześnie platformie SCP.NET obsługiwać inne rzeczy.

Kod wtyczki implementuje jeden z następujących interfejsów. Który interfejs zależy od tego, czy topologia jest transakcyjna czy nietransakcyjna i czy składnik jest wylewką, czy śrubą.

* **IscpSpout (WYSUŃ ISCP)**
* **ISCPBolt (ISCPBolt)**
* **IsCPTxSpout**
* **ISCPBatchBolt (ISCPBatchBolt)**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** jest wspólnym interfejsem dla wielu wtyczek.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>IscpSpout (WYSUŃ ISCP)

**ISCPSpout** jest interfejsem dla nietransakcyjnego wylewki.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Gdy **NextTuple** jest wywoływana, kod Języka C# może emitować jedną lub więcej krotek. Jeśli nie ma nic do emisji, ta metoda powinna powrócić bez emitowania czegokolwiek.

**NextTuple**, **Ack**i **Fail** metody są wywoływane w ciasnej pętli w jednym wątku procesu C#. Gdy nie ma żadnych krotek do emisji, mają **NextTuple** uśpić przez krótki czas, takich jak 10 milisekund. Ten sen pomaga uniknąć marnowania dostępności procesora CPU.

**Ack** i **Fail** metody są wywoływane tylko wtedy, gdy plik specyfikacji włącza mechanizm potwierdzenia. Parametr *seqId* identyfikuje krotkę, która została potwierdzona lub nie powiodła się. Jeśli potwierdzenie jest włączone w topologii nietransakcyjnej, w dziobku należy użyć następującej funkcji **Emit:**

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Jeśli topologia nietransakcyjna nie obsługuje potwierdzenia, **Ack** i **Fail** można pozostawić jako puste funkcje.

Parametr *wejściowy parms* w tych funkcjach określa pusty słownik i jest zarezerwowany do wykorzystania w przyszłości.

### <a name="iscpbolt"></a>ISCPBolt (ISCPBolt)

**ISCPBolt** jest interfejsem dla śruby nietransakcyjnej.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Gdy nowa krotka jest dostępna, **Execute** funkcja jest wywoływana do przetwarzania go.

### <a name="iscptxspout"></a>IsCPTxSpout

**ISCPTxSpout** jest interfejsem dla transakcyjnej dziobka.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Podobnie jak ich odpowiedniki nietransakcyjne, **NextTx**, **Ack**i **Fail** są wywoływane w ciasnej pętli w jednym wątku procesu C#. Gdy nie ma żadnych krotek do emisji, mieć **NextTx** uśpić przez krótki czas, jak 10 milisekund. Ten sen pomaga uniknąć marnowania dostępności procesora CPU.

Gdy **NextTx** jest wywoływana, aby rozpocząć nową transakcję, parametr *wyjściowy seqId* identyfikuje transakcję. Transakcja jest również używana w **Ack** i **Fail**. Metoda **NextTx** może emitować dane po stronie Oprogramowania Java. Dane są przechowywane w ZooKeeper do obsługi powtórki. Ponieważ ZooKeeper ma ograniczoną pojemność, kod powinien emitować tylko metadane, a nie zbiorcze dane w wylewce transakcyjnej.

Ponieważ Storm automatycznie odtwarza nieudaną transakcję, **fail** zwykle nie zostanie wywołana. Ale jeśli protokół SCP może sprawdzić metadane emitowane przez wylewkę transakcyjną, może wywołać **fail,** gdy metadane są nieprawidłowe.

Parametr *wejściowy parms* w tych funkcjach określa pusty słownik i jest zarezerwowany do wykorzystania w przyszłości.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt (ISCPBatchBolt)

**ISCPBatchBolt** jest interfejsem dla śruby transakcyjnej.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Execute **Execute** Metoda jest wywoływana, gdy nowa krotka dociera do śruby. **FinishBatch** Metoda jest wywoływana po zakończeniu tej transakcji. Parametr *wejściowy parms* jest zarezerwowany do wykorzystania w przyszłości.

Dla topologii transakcyjnej **StormTxAttempt** jest ważną klasą. Ma dwóch członków: **TxId** i **AttemptId**. Element członkowski **TxId** identyfikuje określoną transakcję. Transakcja może być podejmowana wiele razy, jeśli nie powiedzie się i jest odtwarzana.

SCP.NET tworzy nowy obiekt **ISCPBatchBolt** do przetwarzania każdego obiektu **StormTxAttempt,** podobnie jak to, co storm robi w Javie. Celem tego projektu jest obsługa przetwarzania transakcji równoległych. Po zakończeniu próby transakcji odpowiedni obiekt **ISCPBatchBolt** jest niszczony i zbierany moduł śmietnikowy.

## <a name="object-model"></a>Model obiektu

SCP.NET zapewnia również prosty zestaw kluczowych obiektów dla deweloperów do programowania. Obiekty to **Context**, **StateStore**i **SCPRuntime**. Zostały one omówione w tej sekcji.

### <a name="context"></a>Kontekst

**Context** Obiekt zapewnia uruchomione środowisko do aplikacji. Każde **wystąpienie ISCPPlugin** **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**lub **ISCPBatchBolt** ma odpowiednie **wystąpienie kontekstu.** Funkcjonalność zapewniana przez **Context** jest podzielona na dwie części:

* Część statyczna, która jest dostępna w całym procesie C#,
* Część dynamiczna, która jest dostępna tylko dla określonego **wystąpienia kontekstu**

### <a name="static-part"></a>Część statyczna

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**Logger** Obiekt jest przeznaczony do celów rejestrowania.

Obiekt **pluginType** wskazuje typ wtyczki procesu C#. Jeśli proces jest uruchamiany w lokalnym trybie testowym bez języka Java, typ wtyczki jest **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Właściwość Config** pobiera parametry konfiguracji od strony Java, która przekazuje je po zainicjowaniu wtyczki C#. Parametry **Config** są podzielone na dwie części: **stormConf** i **pluginConf.**

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**StormConf** część jest parametry zdefiniowane przez Storm, a **pluginConf** część jest parametry zdefiniowane przez SCP. Oto przykład:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**Typ TopologyContext** pobiera kontekst topologii. Jest to najbardziej przydatne dla wielu składników równoległych. Oto przykład:

```csharp
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
```

### <a name="dynamic-part"></a>Część dynamiczna

Następujące interfejsy są istotne dla określonego **wystąpienia kontekstu,** który jest tworzony przez platformę SCP.NET i przekazywane do kodu:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

W przypadku nietransakcyjnego dziobka obsługującego potwierdzenie dostępna jest następująca metoda:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Śruba nietransakcyjna, która obsługuje potwierdzenie należy jawnie wywołać **Ack** lub **Fail** z krotki, które otrzymał. Podczas emitowania nowej krotki śruba musi również określać kotwy krotki. Dostępne są następujące metody:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>Magazyn stanu

**StateStore** Obiekt zapewnia usługi metadanych, generowanie sekwencji monotoniczne i koordynacji bez oczekiwania. Można tworzyć rozdzielne abstrakcje współbieżności wyższego poziomu w **StateStore**. Te abstrakcje obejmują blokady rozproszone, kolejki rozproszone, bariery i usługi transakcyjne.

Aplikacje SCP mogą używać obiektu **State** do serializacji informacji w [aplikacji Apache ZooKeeper](https://zookeeper.apache.org/). Ta zdolność jest szczególnie cenne dla topologii transakcyjnej. Jeśli wylewka transakcyjna przestaje odpowiadać i uruchamia się ponownie, **państwo** może pobrać niezbędne informacje z ZooKeeper i ponownie uruchomić potok.

**Obiekt StateStore** ma następujące metody główne:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**State** Obiekt ma następujące główne metody:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Gdy **simpleMode** jest ustawiona na **true,** **Commit** metoda usuwa odpowiednie ZNode w ZooKeeper. W przeciwnym razie metoda usuwa bieżący ZNode i dodaje\_nowy węzeł w ścieżkę committed.

### <a name="scpruntime"></a>Czas SCPRuntime

Klasa **SCPRuntime** zawiera następujące dwie metody:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoda **Initialize** inicjuje środowisko wykonawcze punktu uruchomieniowego punktu scp. W tej metodzie proces C# łączy się po stronie Java, aby uzyskać parametry konfiguracji i kontekst topologii.

**LaunchPlugin** Metoda uruchamia pętlę przetwarzania wiadomości. W tej pętli wtyczka Języka C# odbiera wiadomości od strony Java. Komunikaty te obejmują krotek i sygnałów sterujących. Wtyczka następnie przetwarza komunikaty, być może przez wywołanie metody interfejsu dostarczone przez kod.

Parametr wejściowy dla **LaunchPlugin** jest pełnomocnikiem. Metoda może zwrócić obiekt implementuje interfejs **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**lub **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Dla **ISCPBatchBolt**, można uzyskać **StormTxAttempt** obiektu z *parms* parametru i użyć go do oceny, czy próba jest próbą powtórzoną. Sprawdzanie próby powtórki jest często wykonywane na śrubę zatwierdzenia. Przykład HelloWorldTx w dalszej części tego artykułu pokazuje ten czek.

Wtyczki SCP mogą być uruchamiane w dwóch trybach: lokalnym trybie testowym i trybie regularnym.

#### <a name="local-test-mode"></a>Lokalny tryb testowy

W tym trybie wtyczki SCP w kodzie języka C# są uruchamiane wewnątrz programu Visual Studio w fazie rozwoju. W tym trybie można użyć interfejsu **ILocalContext.** Interfejs zawiera metody serializacji emitowanych krotek do plików lokalnych i odczytać je z powrotem do pamięci RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Tryb normalny

W tym trybie proces Storm Java uruchamia wtyczki SCP. Oto przykład:

```csharp
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
```

## <a name="topology-specification-language"></a>Język specyfikacji topologii

Specyfikacja topologii SCP jest językiem specyficznym dla domeny (DSL) do opisywania i konfigurowania topologii SCP. Jest on oparty na [Clojure DSL Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) i jest rozszerzony przez SCP.

Specyfikacje topologii można przesłać bezpośrednio do klastra Storm do wykonania za pomocą polecenia **runSpec.**

SCP.NET dodał następujące funkcje do definiowania topologii transakcyjnych:

| Nowa funkcja | Parametry | Opis |
| --- | --- | --- |
| **tx-topolopy** |*nazwa topologii*<br />*wylewka-mapa*<br />*mapa śrub* |Definiuje topologię transakcyjną z nazwą topologii, mapą definicji wylewek i mapą definicji śrub. |
| **wylewka scp-tx** |*nazwa exec*<br />*args*<br />*Pola* |Definiuje wylewkę transakcyjną. Funkcja uruchamia aplikację, która jest określona przez *exec-name* i używa *args*.<br /><br />Parametr *fields* określa pola wyjściowe wylewki. |
| **scp-tx-batch-bolt** |*nazwa exec*<br />*args*<br />*Pola* |Definiuje śrubę wsadową transakcyjną. Funkcja uruchamia aplikację, która jest określona przez *exec-name* i używa *args.*<br /><br />Parametr *fields* określa pola wyjściowe dla śruby. |
| **scp-tx-commit-bolt** |*nazwa exec*<br />*args*<br />*Pola* |Definiuje śrubę zatwierdzania transakcyjnego. Funkcja uruchamia aplikację, która jest określona przez *exec-name* i używa *args*.<br /><br />Parametr *fields* określa pola wyjściowe dla śruby. |
| **nontx-topologia** |*nazwa topologii*<br />*wylewka-mapa*<br />*mapa śrub* |Definiuje nietransakcyjną topologię z nazwą topologii, mapą definicji wylewek i mapą definicji śrub. |
| **spout scp** |*nazwa exec*<br />*args*<br />*Pola*<br />*Parametry* |Definiuje nietransakcyjną wylewkę. Funkcja uruchamia aplikację, która jest określona przez *exec-name* i używa *args*.<br /><br />Parametr *fields* określa pola wyjściowe wylewki.<br /><br />Parametr *parametrów* jest opcjonalny. Użyj go, aby określić parametry, takie jak "nontransactional.ack.enabled". |
| **śruba scp** |*nazwa exec*<br />*args*<br />*Pola*<br />*Parametry* |Definiuje śrubę nietransakcyjną. Funkcja uruchamia aplikację, która jest określona przez *exec-name* i używa *args*.<br /><br />Parametr *fields* określa pola wyjściowe dla śruby<br /><br />Parametr *parametrów* jest opcjonalny. Użyj go, aby określić parametry, takie jak "nontransactional.ack.enabled". |

SCP.NET definiuje następujące słowa kluczowe:

| Słowo kluczowe | Opis |
| --- | --- |
| **:nazwa** |Nazwa topologii |
| **:topologia** |Topologia przy użyciu funkcji w poprzedniej tabeli i wbudowanych funkcji |
| **:p** |Wskazówka równoległości dla każdego wylewki lub śruby |
| **:config** |Czy chcesz skonfigurować parametry, czy zaktualizować istniejące |
| **:schemat** |Schemat strumienia |

SCP.NET definiuje również te często używane parametry:

| Parametr | Opis |
| --- | --- |
| "plugin.name" |Nazwa pliku exe wtyczki C# |
| "plugin.args" |Argumenty wtyczki |
| "output.schema" |Schemat danych wyjściowych |
| "nontransactional.ack.enabled" |Czy potwierdzenie jest włączone dla topologii nietransakcyjnej |

Polecenie **runSpec** jest wdrażane razem z bitami. Oto użycie polecenia:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametr *resource-dir* jest opcjonalny. Określ go, gdy chcesz podłączyć aplikację języka C#. Określony katalog zawiera aplikację, zależności i konfiguracje.

Parametr *classpath* jest również opcjonalny. Określa ścieżkę klasy Java, jeśli plik specyfikacji zawiera wylewkę lub śrubę Java.

## <a name="miscellaneous-features"></a>Różne funkcje

### <a name="input-and-output-schema-declarations"></a>Deklaracje schematów wejściowych i wyjściowych

Procesy języka C# może emitować krotek. W tym celu platforma serializuje krotek do **bajt[]** obiektów i przenosi obiekty do strony Java. Storm następnie przenosi te krotki do celów.

W składnikach podrzędnych procesy języka C# odbierają krotki z powrotem od strony Java i konwertują je na oryginalne typy platformy. Wszystkie te operacje są ukryte przez platformę.

Aby obsługiwać serializacji i deserializacji, kod musi zadeklarować schemat danych wejściowych i wyjściowych. Schemat jest zdefiniowany jako słownik. Identyfikator strumienia jest kluczem słownika. Wartość klucza to typy kolumn. Składnik może zadeklarować wiele strumieni.

```csharp
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
```

Do obiektu **Context** jest dodawana następująca funkcja:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Deweloperzy muszą upewnić się, że emitowane krotek są zgodne ze schematem zdefiniowanym dla strumienia. W przeciwnym razie system zda wyjątek środowiska uruchomieniowego.

### <a name="multistream-support"></a>Obsługa wielu strumieni

Protokół SCP umożliwia emitowanie kodu do wielu różnych strumieni lub odbieranie go w tym samym czasie. **Obiekt Context** odzwierciedla tę obsługę jako parametr identyfikatora opcjonalnego strumienia metody **Emit.**

Dodano dwie metody w obiekcie SCP.NET **Context.** Emitują one jedną lub więcej krotek do określonych strumieni. *StreamId* Parametr jest ciągiem. Jego wartość musi być taka sama w kodzie języka C# i specyfikacji definicji topologii.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Emitowanie do nieistniejącego strumienia powoduje wyjątki środowiska uruchomieniowego.

### <a name="fields-grouping"></a>Grupowanie pól

Wbudowane grupowanie pól w storm nie działa poprawnie w SCP.NET. Po stronie serwera proxy Java typ danych wszystkich pól jest w rzeczywistości **bajtem[]**. Grupowanie pól używa kodu skrótu **obiektu bajtowego[]** do grupowania. Kod skrótu jest adresem tego obiektu w pamięci RAM. Tak więc grupowanie będzie nieprawidłowe dla obiektów wielobajtowych, które mają tę samą zawartość, ale nie ten sam adres.

SCP.NET dodaje dostosowaną metodę grupowania i używa zawartości obiektu **bajt[]** do grupowania. W pliku specyfikacji składnia wygląda następująco:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

W poprzednim pliku specyfikacji:

* `scp-field-group`określa, że grupowanie jest dostosowaną grupą pól zaimplementowanym przez protokół SCP.
* `:tx`lub `:non-tx` określa, czy topologia jest transakcyjna. Te informacje są potrzebne, ponieważ indeks początkowy różni się między transakcyjnymi i nietransakcyjnymi topologiami.
* `[0,1]`określa zestaw skrótów identyfikatorów pól rozpoczynających się od zera.

### <a name="hybrid-topology"></a>Topologia hybrydowa

Natywny kod Storm jest napisany w języku Java. SCP.NET udoskonaliła storm, aby umożliwić pisanie kodu języka C# do obsługi logiki biznesowej. Ale SCP.NET obsługuje również topologie hybrydowe, które zawierają nie tylko wylewki C#/ śruby, ale także wylewki /śruby Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Określanie wylewki/śruby Java w pliku specyfikacji

Można użyć **scp-spout** i **scp-bolt** w pliku specyfikacji, aby określić wylewki i śruby Java. Oto przykład:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Oto `microsoft.scp.example.HybridTopology.Generator` nazwa klasy wylewki Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Określanie ścieżki klasy języka Java w poleceniu runSpec

Jeśli chcesz przesłać topologię zawierającą wylewki lub śruby Java, najpierw skompiluj je do produkcji plików JAR. Następnie określ ścieżkę klasy java, która zawiera pliki JAR podczas przesyłania topologii. Oto przykład:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

`examples\HybridTopology\java\target\` Oto folder zawierający plik JAR wylewki Java/śruba.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializacja i deserializacja między Javą a C #

Składnik SCP zawiera stronę Java i stronę C#. Aby wchodzić w interakcje z natywnymi wylewkami/śrubami Java, serializacja i deserializacja muszą mieć miejsce między stroną Javy a stroną języka C#, jak pokazano na poniższym wykresie:

![Diagram składnika Java wysyłającego do składnika SCP, który następnie wysyła do innego składnika Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializacja po stronie Javy i deserializacja po stronie języka C#

Najpierw należy podać domyślną implementację serializacji po stronie Języka Java i deserializacji po stronie języka C#.

Określ metodę serializacji po stronie języka Java w pliku specyfikacji.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Określ metodę deserializacji strony języka C# w kodzie języka C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Jeśli typ danych nie jest zbyt złożony, ta domyślna implementacja powinna obsługiwać większość przypadków. Oto przypadki, w których można podłączyć własną implementację:

* Typ danych jest zbyt złożony dla implementacji domyślnej.
* Wydajność implementacji domyślnej nie spełnia wymagań.

Interfejs serializacji po stronie Języka Java jest zdefiniowany jako:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Interfejs deserializacji po stronie języka C# jest zdefiniowany jako:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializacja po stronie języka C# i deserializacja po stronie Javy

Określ metodę serializacji po stronie języka C# w kodzie języka C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Określ metodę deserializacji strony Języka Java w pliku specyfikacji.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

W `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` tym miejscu jest nazwa deserializer i `"microsoft.scp.example.HybridTopology.Person"` jest klasą docelową dane są deserializowane do.

Można również podłączyć własną implementację serializatora języka C# i deserializatora języka Java.

Ten kod jest interfejsem dla serializatora języka C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Ten kod jest interfejsem deserializatora Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Tryb hosta protokołu SCP

W tym trybie można skompilować kod jako bibliotekę DLL i użyć pliku SCPHost.exe dostarczonego przez protokół SCP w celu przesłania topologii. Plik specyfikacji wygląda jak ten kod:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

W `"plugin.name"` tym miejscu `"SCPHost.exe"`jest określony jako , który jest dostarczany przez SDK SCP. Program SCPHost.exe akceptuje trzy parametry w następującej kolejności:

1. Nazwa biblioteki DLL, która znajduje się `"HelloWorld.dll"` w tym przykładzie.
1. Nazwa klasy, która `"Scp.App.HelloWorld.Generator"` znajduje się w tym przykładzie.
1. Nazwa publicznej metody statycznej, która może być wywołana w celu uzyskania wystąpienia **ISCPPlugin**.

W trybie hosta skompiluj kod jako bibliotekę DLL do wywołania przez platformę SCP. Ponieważ platforma może następnie uzyskać pełną kontrolę nad całą logiką przetwarzania, zaleca się przesłanie topologii w trybie hosta SCP. Upraszcza to doświadczenie w zakresie rozwoju. Zapewnia również większą elastyczność i lepszą zgodność z powrotem dla nowszych wydań.

## <a name="scp-programming-examples"></a>Przykłady programowania SCP

### <a name="helloworld"></a>Helloworld

Poniższy prosty przykład HelloWorld pokazuje smak SCP.NET. Wykorzystuje nietransakcyjną topologię z wylewką o nazwie **generator** i dwie śruby zwane **rozdzielaczem** i **licznikiem.** Generator **generator** wylewka losowo generuje zdania i emitują te zdania do **rozdzielacza**. Śruba **rozdzielacza** dzieli zdania na słowa i emituje te słowa do śruby **licznika.** Śruba **licznika** używa słownika do rejestrowania występowania każdego wyrazu.

W tym przykładzie znajdują się dwa pliki\_specyfikacji: HelloWorld.spec i HelloWorld EnableAck.spec. Kod języka C# można dowiedzieć się, czy `pluginConf` potwierdzenie jest włączone przez uzyskanie obiektu od strony Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Jeśli potwierdzenie jest włączone w wylewce, słownik buforuje krotek, które nie zostały potwierdzone. Jeśli `Fail` jest wywoływana, nie można powtórzyć krotki.

```csharp
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
```

### <a name="helloworldtx"></a>HelloWorldTx (witaj)

W poniższym przykładzie HelloWorldTx pokazano, jak zaimplementować topologii transakcyjnej. W przykładzie ma jeden wylewka o nazwie **generator**, śrubę wsadową o nazwie **partial-count**i śrubę zatwierdzania o nazwie **count-sum**. W przykładzie znajdują się również trzy istniejące pliki tekstowe: DataSource0.txt, DataSource1.txt i DataSource2.txt.

W każdej transakcji **generator** wylewka losowo wybiera dwa pliki z istniejących trzech plików i emituje dwie nazwy plików do **śruby częściowej liczby.** Śruba **z częściową liczbą:**

1. Pobiera nazwę pliku z otrzymanej krotki.
1. Otwiera odpowiedni plik.
1. Zlicza liczbę słów w pliku.
1. Emituje liczbę wyrazów do śruby **sumy licznika.**

Śruba **sumy zliczania** podsumowuje całkowitą liczbę.

Aby osiągnąć dokładnie raz semantyki, **count-sum** commit bolt musi ocenić, czy jest to transakcja powtórzona. W tym przykładzie ma następującą statyczną zmienną elementu członkowskiego:

```csharp
public static long lastCommittedTxId = -1; 
```

Po utworzeniu wystąpienia **ISCPBatchBolt** pobiera wartość `txAttempt` obiektu z parametrów wejściowych.

```csharp
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
```

Po `FinishBatch` wywołaniu `lastCommittedTxId` jest aktualizowana, jeśli nie jest to transakcja odtwomiona.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopologia

Ta topologia zawiera wylewkę Java i śrubę C#. Używa domyślnej implementacji serializacji i deserializacji dostarczonej przez platformę SCP. Szczegółowe informacje o pliku specyfikacji można\\znaleźć w pliku HybridTopology.spec w folderze HybridTopology. Zobacz też SubmitTopology.bat, aby dowiedzieć się, jak określić ścieżkę klasy języka Java.

### <a name="scphostdemo"></a>SCPHostDemo (SCPHostDemo)

Ten przykład jest w istocie taki sam jak HelloWorld. Jedyną różnicą jest to, że kod jest kompilowany jako biblioteka DLL i topologii jest przesyłany przy użyciu SCPHost.exe. Zobacz sekcję trybu hosta protokołu SCP, aby uzyskać bardziej szczegółowe wyjaśnienie.

## <a name="next-steps"></a>Następne kroki

Przykłady topologii apache Storm utworzonej przy użyciu protokołu SCP można znaleźć w następujących artykułach:

* [Tworzenie topologii języka C# dla usługi Apache Storm w programie HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Przetwarzanie zdarzeń z usługi Azure Event Hubs za pomocą usługi Apache Storm w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Przetwarzaj dane z czujników pojazdu z centrów zdarzeń za pomocą aplikacji Apache Storm w programie HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
