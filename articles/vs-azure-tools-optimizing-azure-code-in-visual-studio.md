---
title: Optymalizacja kodu platformy Azure w programie Visual Studio | Dokumentacja firmy Microsoft
description: Informacje dotyczące sposobu Azure kodu optymalizacji narzędzia w programie Visual Studio sprawić, że kod bardziej niezawodny i lepszego wykonywania.
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: 3ee2cc3ac5098ebf205331167faffa2b5f9b6d56
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937561"
---
# <a name="optimizing-your-azure-code"></a>Optymalizacja kodu platformy Azure
W przypadku programowania aplikacji, które używają programu Microsoft Azure, istnieją pewne praktyk kodowania, które należy wykonać, aby uniknąć problemów z aplikacji, skalowalność, zachowania i wydajności w środowisku chmury. Firma Microsoft udostępnia narzędzia do analizy kodu platformy Azure, która rozpoznaje i identyfikuje niektóre z tych często napotkał problemy i ułatwia ich rozwiązywania. Możesz pobrać narzędzie w programie Visual Studio za pośrednictwem pakietu NuGet.

## <a name="azure-code-analysis-rules"></a>Azure reguł analizy kodu
Narzędzie do analizy kodu Azure stosowane są następujące reguły do automatycznie Flaga Azure kodu, gdy znajdzie znanych problemów wpływających na wydajność. Wykryto problemy występują jako ostrzeżenia lub błędy kompilatora. Poprawki kodu lub sugestie rozwiązywać ostrzeżenia lub błędu często są realizowane za pośrednictwem ikoną żarówki.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Unikaj używania domyślny tryb stanu sesji (w trakcie)
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Opis
Jeśli używasz domyślny tryb stanu sesji (w trakcie) dla aplikacji w chmurze, zostaną utracone stanu sesji.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Domyślnie tryb stanu sesji, które są określone w pliku web.config jest w trakcie. Ponadto jeśli żadnego wpisu określony w pliku konfiguracji Tryb stanu sesji domyślnie w procesie. Tryb w trakcie zapisuje stan sesji w pamięci na serwerze sieci web. Po uruchomieniu wystąpienia lub nowe wystąpienie jest używana do równoważenia obciążenia lub obsługi trybu failover, stan sesji, przechowywane w pamięci na serwerze sieci web nie są zapisywane. Taka sytuacja uniemożliwia aplikacji jest skalowalna w chmurze.

Stan sesji ASP.NET obsługuje kilka opcji innego magazynu dla danych stanu sesji: InProc, StateServer, SQLServer, niestandardowe i Off. Zalecane jest używanie trybu niestandardowy do przechowywania danych w zewnętrznym sklepie stanu sesji, takie jak [dostawcę stanu sesji Azure Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Rozwiązanie
Jedno rozwiązanie zalecane jest przechowywanie stanu sesji na usługi zarządzana pamięć podręczna. Dowiedz się, jak używać [dostawcę stanu sesji Azure Redis](http://go.microsoft.com/fwlink/?LinkId=401521) do przechowywania Twojego stanu sesji. Można również przechowywanie stanu sesji w innych miejscach, aby upewnić się, że aplikacja jest skalowalna w chmurze. Aby dowiedzieć się więcej na temat rozwiązań alternatywnych, przeczytaj [trybów stanu sesji](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Uruchom metody nie powinna być asynchroniczne
### <a name="id"></a>ID
AP1000

### <a name="description"></a>Opis
Tworzenie metod asynchronicznych (takich jak [await](https://msdn.microsoft.com/library/hh156528.aspx)) poza [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody, a następnie wywołać metody asynchroniczne z [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklarowanie [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) roli proces roboczy wprowadzić pętli ponowne uruchomienie powoduje, że metoda jako asynchroniczny.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Wywołanie metod asynchronicznych wewnątrz [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody powoduje, że środowisko wykonawcze usługi chmury do odtworzenia roli procesu roboczego. Podczas uruchamiania roli proces roboczy, wszystkie wykonywania programu ma miejsce w obrębie [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody. Kończenie [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda powoduje roli proces roboczy ponownie uruchomić. Gdy środowiska uruchomieniowego roli procesu roboczego trafi metoda asynchroniczna, wywołuje wszystkie operacje po metoda asynchroniczna, a następnie zwraca. Powoduje to roli proces roboczy wyjść z [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) — metoda i uruchom ponownie. W następnej iteracji wykonywania roli procesu roboczego trafienia metody asynchronicznej ponownie i uruchamia ponownie, powodując roli procesu roboczego do odtworzenia także ponownie.

### <a name="solution"></a>Rozwiązanie
Umieść wszystkie operacje asynchroniczne poza [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody. Następnie wywołaj metodę refaktoryzowane async z wewnątrz [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody, takiej jak RunAsync .wait (). Narzędzie do analizy kodu Azure może pomóc rozwiązać ten problem.

Poniższy fragment kodu przedstawia kod poprawkę dotyczącą tego problemu:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Sygnatura dostępu współdzielonego magistrali usługi uwierzytelniania
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Opis
Dostęp do sygnatury dostępu Współdzielonego jest używany do uwierzytelniania. Usługi kontroli dostępu (ACS) jest przestarzałe uwierzytelniania magistrali usługi.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Aby zwiększyć bezpieczeństwo Azure Active Directory zastępuje ACS uwierzytelniania przy użyciu uwierzytelniania sygnatury dostępu Współdzielonego. Zobacz [usługi Azure Active Directory jest przyszłości ACS](https://cloudblogs.microsoft.com/enterprisemobility/2013/06/22/azure-active-directory-is-the-future-of-acs/) uzyskać informacji o planie przejścia.

### <a name="solution"></a>Rozwiązanie
Korzystanie z uwierzytelniania sygnatury dostępu Współdzielonego w aplikacjach. Poniższy przykład przedstawia użycie istniejącego tokenu sygnatury dostępu Współdzielonego dostępu przestrzeń nazw magistrali usług lub jednostki.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Zobacz następujące tematy, aby uzyskać więcej informacji.

* Aby uzyskać ogólne informacje, zobacz [uwierzytelniania podpisu dostępu udostępnionych za pomocą usługi Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [Sposób użycia uwierzytelniania sygnatury dostępu do udostępnionych z usługą Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)
* Aby uzyskać przykładowy projekt, zobacz [uwierzytelniania przy użyciu dostępu sygnatury dostępu Współdzielonego z subskrypcji magistrali usług](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Należy rozważyć użycie metody OnMessage w celu uniknięcia "pętlę odbierania"
### <a name="id"></a>ID
AP2002

### <a name="description"></a>Opis
Aby uniknąć przechodzenia w "otrzymywać pętli" wywoływania **OnMessage** metoda jest lepszym rozwiązaniem do odbierania wiadomości niż wywoływania **Receive** — metoda. Jednak jeśli musisz użyć **Receive** metoda i określić czas oczekiwania serwer z systemem innym niż domyślny, upewnij się, czas oczekiwania serwera jest więcej niż jedną minutę.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Podczas wywoływania metody **OnMessage**, klient uruchamia pompę wewnętrzny komunikat, który sonduje stale kolejki lub subskrypcji. Ten komunikat pompa zawiera nieskończoną pętlę, która wystawia wywołania do odbierania wiadomości. Jeśli upłynie limit czasu połączenia, wystawia nowe połączenie. Interwał limitu czasu jest określany przez wartość [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) właściwość [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)który jest używany.

Zaletą używania **OnMessage** w porównaniu do **Receive** jest, że użytkownicy nie muszą ręcznie sondować komunikatów, obsługa wyjątków przetwarzać wiele komunikatów równolegle i ukończyć wiadomości.

Jeśli należy wywołać **Receive** bez użycia wartość domyślną, upewnij się, *ServerWaitTime* wartość jest więcej niż jednej minuty. Ustawienie *ServerWaitTime* na więcej niż jedną minutę zapobiega serwera limit czasu, zanim pełni odbioru wiadomości.

### <a name="solution"></a>Rozwiązanie
Zobacz poniższe przykłady kodu dla zalecane użycia. Aby uzyskać więcej informacji, zobacz [QueueClient.OnMessage — metoda (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)i [QueueClient.Receive — metoda (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Aby poprawić wydajność Azure infrastruktury obsługi wiadomości, zobacz wzorzec projektowy [asynchroniczne Elementarz wiadomości](https://msdn.microsoft.com/library/dn589781.aspx).

Poniżej przedstawiono przykład użycia **OnMessage** do odbierania wiadomości.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Poniżej przedstawiono przykład użycia **Receive** z serwerem domyślny czas oczekiwania.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Poniżej przedstawiono przykład użycia **Receive** z serwerem z systemem innym niż domyślny czas oczekiwania.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Należy rozważyć użycie metod asynchronicznych usługi Service Bus
### <a name="id"></a>ID
AP2003

### <a name="description"></a>Opis
Użyć metod asynchronicznych usługi Service Bus do zwiększenia wydajności z komunikatów obsługiwanych przez brokera.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Używanie metod asynchronicznych umożliwia aplikacji program współbieżności, ponieważ wykonywania każdego wywołania nie blokuje głównego wątku. Korzystając z usługi Service Bus metod do obsługi komunikatów, wykonywania operacji (wysyłanie, odbierania, usuwanie, itp.) jest czasochłonne. Teraz obejmuje przetwarzanie operacji przez usługi Service Bus, oprócz opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, operacji musi być wykonywany współbieżnie. Aby uzyskać więcej informacji można znaleźć [najlepsze rozwiązania dotyczące wydajności ulepszenia przy użyciu usługi magistrali obsługiwanych przez brokera obsługi komunikatów](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Rozwiązanie
Zobacz [QueueClient klasy (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) informacji o sposobie używania zalecanych metod asynchronicznych.

Aby poprawić wydajność Azure infrastruktury obsługi wiadomości, zobacz wzorzec projektowy [asynchroniczne Elementarz wiadomości](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Należy wziąć pod uwagę partycjonowania tematów i kolejek usługi Service Bus
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Opis
Partycji usługi Service Bus kolejek i tematów w celu poprawy wydajności z komunikatów usługi Service Bus.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Partycjonowanie tematów i kolejek usługi Service Bus zwiększa dostępność przepływności i usługi wydajności, ponieważ ogólną przepustowość partycjonowanej kolejka lub temat nie jest już ograniczone przez wydajność brokera komunikatów pojedynczego lub magazynie obsługi komunikatów. Ponadto tymczasowego awaria magazynie obsługi komunikatów nie należy partycjonowanej kolejka lub temat niedostępny. Aby uzyskać więcej informacji, zobacz [partycjonowania jednostek obsługi komunikatów](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Rozwiązanie
Poniższy fragment kodu przedstawia sposób partycji jednostek obsługi komunikatów.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Aby uzyskać więcej informacji, zobacz [podzielona na partycje kolejek usługi Service Bus i tematy | Blog usługi Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) i wyewidencjonowania [Microsoft Azure na partycje kolejką usługi Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) próbki.

## <a name="do-not-set-sharedaccessstarttime"></a>Nie ustawiaj SharedAccessStartTime
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Opis
Należy unikać używania SharedAccessStartTimeset bieżący czas, aby natychmiast uruchomić zasady dostępu udostępnionego. Należy ustawić tę właściwość, aby rozpocząć zasady dostępu udostępnionego w późniejszym czasie.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Synchronizacja zegara powoduje, że czas niewielkie różnice centrów danych. Na przykład czy logicznie uważasz, że ustawienie czas rozpoczęcia magazynu zasad sygnatury dostępu Współdzielonego jako bieżący czas za pomocą DateTime.Now lub podobnej metody spowoduje, że SAS zasady zaczynają obowiązywać natychmiast. Jednak czas niewielkie różnice między centrów danych może spowodować problemy z tym, ponieważ sytuacje centrum danych mogą być nieco później niż czas rozpoczęcia, podczas gdy inne wcześniejsze go. W związku z tym zasady sygnatury dostępu Współdzielonego można wygaśnie szybko (lub nawet natychmiast) Jeśli okres istnienia zasad jest zbyt krótki.

Aby uzyskać więcej pomocy na temat używania sygnatura dostępu współdzielonego w magazynie Azure, zobacz [wprowadzenie tabeli SAS (Shared Access Signature) SAS kolejki i zaktualizuj go do SAS obiektu Blob - Microsoft Azure Blog zespołu usługi Magazyn — strona główna — lokacji blogi MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Rozwiązanie
Usuń instrukcji, która ustawia czas rozpoczęcia zasady dostępu współdzielonego. Narzędzie do analizy kodu Azure zapewnia rozwiązanie tego problemu. Aby uzyskać więcej informacji dotyczących zarządzania zabezpieczeniami, zobacz wzorzec projektowy [wzorzec klucza Valet](https://msdn.microsoft.com/library/dn568102.aspx).

Poniższy fragment kodu przedstawia kod poprawkę dotyczącą tego problemu.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Udostępnione czas wygaśnięcia musi być przeprowadzona ponad pięć minut zasad dostępu
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Opis
Może istnieć jak pięciu minut różnica w zegary między centrami danych w różnych lokalizacjach z powodu warunek, znany jako "przesunięcia czasowego zegara." Aby zapobiec sygnatury dostępu Współdzielonego token zasady wygaśnięcia Ustaw wcześniej niż czas wygaśnięcia za więcej niż pięć minut.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Synchronizuj centrów danych w różnych miejscach na świecie przez sygnał zegara. Ponieważ czas sygnał zegara przemieszczać się między lokalizacjami, może istnieć wariancji czas między centrami danych w różnych lokalizacjach geograficznych mimo wszystko, co prawdopodobnie jest zsynchronizowany. Ta różnica czasu mogą wpływać na dostęp współdzielony zasad początkowy czas i wygaśnięcia interwał. W związku z tym aby zapewnić, że zasady dostępu udostępnionego obowiązywać natychmiast, nie Określ czas rozpoczęcia. Ponadto upewnij się, że czas wygaśnięcia jest więcej niż 5 minut, aby zapobiec wczesne limitu czasu.

Aby uzyskać więcej informacji o korzystaniu z sygnaturą dostępu współdzielonego w magazynie Azure, zobacz [wprowadzenie tabeli SAS (Shared Access Signature) SAS kolejki i zaktualizuj go do SAS obiektu Blob - Microsoft Azure Blog zespołu usługi Magazyn — strona główna — lokacji blogi MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Rozwiązanie
Aby uzyskać więcej informacji dotyczących zarządzania zabezpieczeniami, zobacz wzorzec projektowy [wzorzec klucza Valet](https://msdn.microsoft.com/library/dn568102.aspx).

Oto przykład nieokreślenie godzinę rozpoczęcia zasad dostępu udostępnionego.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Oto przykład określenia godziny rozpoczęcia zasad dostęp współdzielony z okresem ważności zasady więcej niż pięć minut.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Aby uzyskać więcej informacji, zobacz [tworzenia i używania sygnaturę dostępu współdzielonego](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Użyj CloudConfigurationManager
### <a name="id"></a>ID
AP4000

### <a name="description"></a>Opis
Przy użyciu [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) klasy dla projektów, takie jak witryny sieci Web Azure i usług Azure mobile services nie będzie powodować problemy środowiska wykonawczego. Najlepszym rozwiązaniem, jednak jest warto użyć chmury[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) jako jednolity sposób zarządzania konfiguracjami dla wszystkich aplikacji w chmurze Azure.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
CloudConfigurationManager odczytuje plik konfiguracji odpowiednich dla środowiska aplikacji.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Rozwiązanie
Zrefaktoryzuj kod, aby używał [klasa CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Kod poprawkę dotyczącą tego problemu są udostępniane przez narzędzie do analizy kodu platformy Azure.

Poniższy fragment kodu przedstawia kod poprawkę dotyczącą tego problemu. Replace

`var settings = ConfigurationManager.AppSettings["mySettings"];`

z 

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Poniżej przedstawiono przykład sposobu przechowywania ustawienie konfiguracji w pliku App.config lub Web.config. Dodaj ustawienia w sekcji appSettings pliku konfiguracji. Poniżej znajduje się plik Web.config w poprzednim przykładzie kodu.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Unikaj używania ciągów połączenia stałe
### <a name="id"></a>ID
AP4001

### <a name="description"></a>Opis
Użyj parametrów połączenia z wpisaną na stałe, musisz zaktualizować je później będzie konieczne zmiany kodu źródłowego i ponownie skompilować aplikację. Jednak jeśli parametry połączenia są przechowywane w pliku konfiguracji, można zmienić je później, po prostu aktualizując pliku konfiguracji.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Koduj parametrów połączenia jest zła rozwiązaniem, ponieważ wprowadza ona problemów, gdy parametry połączenia muszą zostać zmienione szybko. Ponadto jeśli projekt musi zostać zaewidencjonowane do kontroli źródła, parametry połączenia ustalony wprowadzić luk w zabezpieczeniach, ponieważ ciągi można wyświetlić w kodzie źródłowym.

### <a name="solution"></a>Rozwiązanie
Przechowywanie parametrów połączenia w plików konfiguracyjnych lub środowisk Azure.

* Dla aplikacji autonomicznej należy użyć pliku app.config do przechowywania ustawień parametrów połączenia.
* Dla aplikacji hostowanych przez usługi IIS sieci web należy użyć pliku web.config do przechowywania ciągów połączenia.
* Dla aplikacji vNext ASP.NET należy użyć configuration.json do przechowywania ciągów połączenia.

Informacje na temat używania plików konfiguracji, takich jak plik web.config lub app.config, zobacz [wskazówki dotyczące konfigurowania sieci Web ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Uzyskać informacji na temat sposobu Azure pracy zmiennych środowiska, zobacz [Azure Web Sites: jak ciągi aplikacji i działać ciągów połączenia](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Aby uzyskać informacje na przechowywanie parametrów połączenia w kontroli źródła, zobacz [należy unikać umieszczania poufne informacje, takie jak parametry połączenia w plikach, które są przechowywane w repozytorium kodu źródłowego](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Użyj pliku konfiguracji diagnostyki
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Opis
Zamiast konfigurować ustawienia diagnostyki w kodzie, takich jak przy użyciu Microsoft.WindowsAzure.Diagnostics programowania interfejsu API, należy skonfigurować ustawienia diagnostyki w pliku diagnostics.wadcfg. (Lub diagnostics.wadcfgx, jeśli używasz 2.5 zestawu SDK platformy Azure). W ten sposób można zmienić ustawień diagnostycznych bez konieczności ponownego kompilowania kodu.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Przed 2.5 zestawu SDK platformy Azure, (używający Diagnostyka Azure 1.3), Azure Diagnostics (WAD) może zostać skonfigurowany przy użyciu kilka różnych metod: dodanie go do obiektu blob konfiguracji w magazynie, za pomocą kodu imperatywnych, deklaratywne konfiguracji lub domyślny Konfiguracja. Preferowany sposób, aby skonfigurować diagnostykę jest jednak użycie plik konfiguracyjny XML (diagnostics.wadcfg lub diagnositcs.wadcfgx dla zestawu SDK, 2.5 i nowszych) w projekcie aplikacji. W tym podejście pliku diagnostics.wadcfg całkowicie definiuje konfigurację i aktualizowanie i wdrożone w momencie. Mieszanie korzystanie z pliku konfiguracji diagnostics.wadcfg z metod programistycznych ustawienia konfiguracji za pomocą [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)lub [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)można klas prowadzić do pomyłek. Zobacz [zainicjować lub zmienianie konfiguracji diagnostyki Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) Aby uzyskać więcej informacji.

Począwszy od 1.3 WAD (dołączony do 2.5 zestawu SDK platformy Azure), jego nie jest już możliwość użycia kod, aby skonfigurować diagnostykę. W związku z tym można podać tylko konfiguracji podczas stosowania lub aktualizowania rozszerzenia diagnostyki.

### <a name="solution"></a>Rozwiązanie
Przenoszenie ustawień diagnostycznych do pliku konfiguracji diagnostyki (diagnositcs.wadcfg lub diagnositcs.wadcfgx dla zestawu SDK, 2.5 i nowszych) za pomocą projektanta konfiguracji diagnostyki. Zaleca się zainstalowanie [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) i korzystać z najnowszych funkcji diagnostyki.

1. Menu skrótów dla roli, który chcesz skonfigurować wybierz polecenie Właściwości, a następnie wybierz kartę Konfiguracja.
2. W **diagnostyki** sekcji, upewnij się, że **włączyć diagnostyki** pole wyboru jest zaznaczone.
3. Wybierz **Konfiguruj** przycisku.

   ![Dostęp do opcji Włącz diagnostyki](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i maszyn wirtualnych](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) Aby uzyskać więcej informacji.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Unikaj deklarowanie obiektów DbContext jako statyczne
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Opis
Aby zapisać pamięci, należy unikać deklarowanie obiektów DBContext jako statyczny.

Udostępnij te koncepcje i opinie na [opinii analizy kodu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Przyczyna
Obiekty DBContext przechowywania wyników zapytania z każdym wywołaniu. Obiekty DBContext statyczne nie są usuwane, dopóki nie zostanie zwolniony domeny aplikacji. W związku z tym statycznego obiektu DBContext może używać dużych ilości pamięci.

### <a name="solution"></a>Rozwiązanie
Zadeklarować zmiennej lokalnej lub pole wystąpienia niestatycznego DBContext, użyj go zadania, a następnie pozwól mu usuwane po użyciu.

Klasa kontrolera MVC poniższy przykład przedstawia użycie obiektu DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o optymalizacji i rozwiązywanie problemów z aplikacjami platformy Azure, zobacz [Rozwiązywanie problemów aplikacji sieci web w usłudze Azure App Service przy użyciu programu Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
