---
title: Rozpoczynanie pracy z przekazuje Azure przekazywania WCF w programie .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać przekaźników Azure przekazywania WCF do połączenia dwóch aplikacji udostępnianych w różnych lokalizacjach.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "26856086"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Jak używać usługi WCF przekazywania Azure przekazuje z platformą .NET
W tym artykule opisano sposób korzystania z usługi przekaźnika usługi Azure. Przykłady zostały napisane w języku C# i używają interfejsu API Windows Communication Foundation (WCF) z rozszerzeniami zawartymi w zestawie usługi Service Bus. Aby uzyskać więcej informacji na temat przekaźnika usługi Azure, zobacz [Omówienie przekaźnika usługi Azure](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Co to jest przekazywania WCF?

Azure [ *przekazywania WCF* ](relay-what-is-it.md) usługi umożliwia tworzenie hybrydowych aplikacji działających w centrum danych Azure i w lokalnym środowisku korporacyjnym. Ułatwia to, ponieważ umożliwia bezpieczne Uwidacznianie usług Windows Communication Foundation (WCF), które znajdują się w korporacyjnym środowisku sieciowym w chmurze publicznej, bez konieczności otwierania połączenia zapory lub wymagających niepożądanych przez usługi przekazywania zmiany infrastruktury sieci korporacyjnej.

![Pojęcia dotyczące przekaźnika WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Przekaźnik Azure umożliwia udostępnianie usług WCF w istniejącym środowisku korporacyjnym. Następnie można oddelegować nasłuchiwanie przychodzących sesji i żądań dotyczących tych usług WCF z usługą przekazywania działającej na platformie Azure. Dzięki temu można ujawniać te usługi w kodzie aplikacji działającej na platformie Azure, pracownikom mobilnym albo środowiskom partnerów w ekstranecie. Przekaźnik umożliwia bezpieczne kontroli, kto ma dostęp do tych usług na poziomie szczegółowych. Jest to nie tylko wydajny i bezpieczny sposób ujawniania funkcji oraz danych aplikacji z istniejących rozwiązań korporacyjnych, ale również metoda korzystania z nich w chmurze.

W tym artykule omówiono sposób używania przekaźnika usługi Azure można utworzyć usługi sieci web WCF, ujawnianej za pomocą kanału TCP powiązanie, która implementuje bezpieczną konwersację między dwiema stronami.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Uzyskiwanie pakietu NuGet usługi Service Bus
Dodanie [pakietu NuGet usługi Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) jest najprostszym sposobem odwołania do interfejsu API usługi Service Bus i skonfigurowania aplikacji ze wszystkimi zależnościami usługi Service Bus. Aby zainstalować pakiet NuGet w projekcie, wykonaj następujące kroki:

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
2. Wyszukaj ciąg „Service Bus” i wybierz pozycję **Microsoft Azure Service Bus**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij poniższe okno dialogowe:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Udostępnianie i korzystanie z usługi sieci web SOAP w przypadku protokołu TCP
W celu ujawnienia istniejącej usługi sieci Web SOAP WCF na potrzeby zewnętrznego korzystania z tej usługi należy wprowadzić zmiany do jej powiązań i adresów. W tym celu konieczne może być wprowadzenie zmian do pliku konfiguracyjnego albo do kodu — w zależności od sposobu konfiguracji usług WCF. Należy pamiętać, że WCF umożliwia ma wiele punktów końcowych sieci przez tę samą usługę, dzięki czemu można zachować istniejące wewnętrzne punkty końcowe podczas dodawania punktów końcowych przekazywania na potrzeby dostępu zewnętrznego w tym samym czasie.

W tym zadaniu kompilacji prostą usługę WCF i Dodaj do niej odbiornik przekazywania. W tym ćwiczeniu przyjęto, że masz pewną znajomość programu Visual Studio i w związku z tym nie omówiono wszystkich szczegółów tworzenia projektu. Zamiast tego ćwiczenie koncentruje się na kodzie.

Przed przejściem do tych kroków wykonaj następującą procedurę, aby skonfigurować swoje środowisko:

1. W programie Visual Studio utwórz aplikację konsolową, która zawiera dwa projekty — „Client” i „Service” — w ramach rozwiązania.
2. Dodaj pakiet NuGet usługi Service Bus do obu tych projektów. Pakiet ten doda do Twoich projektów wszystkie niezbędne odwołania do zestawu.

### <a name="how-to-create-the-service"></a>Jak utworzyć usługę
Najpierw należy utworzyć samą usługę. Każda usługa WCF składa się z co najmniej trzech oddzielnych części:

* Definicja kontraktu, który opisuje, jakie komunikaty są wymieniane i jakie operacje będą wywoływane.
* Implementacja tej Umowy.
* Host, który hostuje usługę WCF i ujawnia szereg punktów końcowych.

Przykłady kodu w tej sekcji dotyczą każdego z tych składników.

Kontrakt definiuje jedną operację `AddNumbers`, która dodaje dwie liczby i zwraca wynik. Interfejs `IProblemSolverChannel` ułatwia klientowi zarządzanie czasem życia serwera proxy. Utworzenie takiego interfejsu jest traktowane jako najlepsze rozwiązanie. Dobrze jest umieścić tę definicję kontraktu w osobnym pliku, do którego można się odwoływać zarówno z projektu „Client”, jak i z projektu „Service”, ale kod można również skopiować do obu tych projektów.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Z kontraktem w miejscu wdrożenia jest następujący:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Programowe konfigurowanie hosta usługi
Gdy kontrakt i implementacja są na miejscu, można hostować usługę. Hosting następuje wewnątrz obiektu [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), który zajmuje się zarządzaniem wystąpieniami usługi i hostuje punkty końcowe nasłuchujące komunikatów. Poniższy kod konfiguruje usługę przy użyciu zarówno standardowym lokalnym punktem końcowym, jak i punktu końcowego przekazywania w celu zilustrowania wygląd obok siebie, wewnętrznych i zewnętrznych punktów końcowych. Zastąp ciąg *namespace* nazwą odpowiedniej przestrzeni nazw, a ciąg *yourKey* kluczem sygnatury dostępu współdzielonego uzyskanym w poprzednim kroku konfiguracji.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

W tym przykładzie utworzysz dwa punkty końcowe należące do tej samej implementacji kontraktu. Jeden jest lokalny, a drugi jest rzutowany za pośrednictwem przekaźnika usługi Azure. Podstawowe różnice między nimi stanowią powiązania; [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) katalogu lokalnego i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) dla punktu końcowego przekazywania i adresów. Lokalny punkt końcowy ma adres sieci lokalnej z unikatowym portem. Przekaźnik punkt końcowy ma adres złożony z ciągu `sb`, przestrzeni nazw i ścieżki "solver". Powoduje to identyfikator URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identyfikuje punkt końcowy usługi jako punktu końcowego TCP usługi Service Bus (przekaźnik) z w pełni kwalifikowana nazwa zewnętrzna DNS. Jeśli umieścisz kod, zastępując symbole zastępcze w funkcji `Main` aplikacji **Service**, uzyskasz usługę funkcjonalną. Jeśli chcesz, aby usługa nasłuchiwała wyłącznie na przekaźnika, Usuń deklarację punktu końcowego.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Konfigurowanie hosta usługi w pliku App.config
Hosta można również skonfigurować przy użyciu pliku App.config. Kod hostowania usługi pojawia się w następnym przykładzie.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Definicje punktu końcowego są w tym przypadku przeniesione do pliku App.config. Pakiet NuGet już dodał szereg definicji do pliku App.config, które są wymagane rozszerzenia konfiguracji dla przekaźnika usługi Azure. Poniższy przykład, który jest dokładnym odpowiednikiem poprzedniego kodu, powinien pojawić się bezpośrednio pod elementem **system.serviceModel**. Ten przykład kodu zakłada, że przestrzeń nazw języka C# w Twoim projekcie ma nazwę **Service**.
Zastąp symbole zastępcze z przekaźnika przestrzeni nazw i klucza sygnatury dostępu Współdzielonego.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Po wprowadzeniu tych zmian usługa będzie uruchamiana tak jak poprzednio, ale z dwoma aktywnymi punktami końcowymi: jednym lokalnym i jednym nasłuchującym w chmurze.

### <a name="create-the-client"></a>Tworzenie klienta
#### <a name="configure-a-client-programmatically"></a>Programowe konfigurowanie klienta
W celu korzystania z usługi możesz utworzyć klienta WCF, używając obiektu [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Usługa Service Bus używa opartego na tokenie modelu zabezpieczeń, który jest implementowany z użyciem sygnatury dostępu współdzielonego. Klasa [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) reprezentuje dostawcę tokenu zabezpieczającego z wbudowanymi metodami Factory, które zwracają dobrze znanych dostawców tokenu. W następującym przykładzie użyto metody [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) w celu obsłużenia uzyskiwania odpowiedniego tokenu sygnatury dostępu współdzielonego. Nazwa i klucz są uzyskiwane z portalu zgodnie z opisem w poprzedniej sekcji.

Najpierw należy utworzyć odwołanie do kodu kontraktu `IProblemSolver` albo skopiować ten kod z usługi do projektu Client.

Następnie Zastąp kod w `Main` klienta, ponownie zastępując tekst zastępczy nazwą przestrzeni nazw przekazywania i klucza sygnatury dostępu Współdzielonego.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Po wykonaniu tych czynności można skompilować klienta i usługę, uruchomić je (najpierw usługę), a następnie klient wywoła usługę i wyświetli **9**. Klienta i serwer można uruchamiać na różnych maszynach, nawet w różnych sieciach, a komunikacja między nimi będzie nadal działać. Kod klienta można również uruchomić w chmurze lub lokalnie.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Konfigurowanie klienta w pliku App.config
Poniższy kod przedstawia sposób konfigurowania klienta przy użyciu pliku App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Definicje punktu końcowego są w tym przypadku przeniesione do pliku App.config. Poniższy przykład, który jest taki sam, jak kod przedstawiony poprzednio, powinien pojawić się bezpośrednio pod `<system.serviceModel>` elementu. W tym miejscu jak poprzednio, należy zastąpić symbole zastępcze nazwą przestrzeni nazw przekazywania i klucza sygnatury dostępu Współdzielonego.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy przekaźnika usługi Azure, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Omówienie architektury usługi Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Pobierz przykłady usługi Service Bus z [przykładów dla platformy Azure] [ Azure samples] lub zobacz [Przegląd przykładów usługi Service Bus][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
