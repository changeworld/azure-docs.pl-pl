---
title: Rozpoczynanie pracy z usługą Azure przekaźnika WCF przekazuje na platformie .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać przekaźników WCF Relay platformy Azure do połączenia dwóch aplikacji udostępnianych w różnych lokalizacjach.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: ee78227f645cbeded7a5c689750db835faf1055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420217"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Jak używać usługi Azure Relay WCF przekaźników przy użyciu platformy .NET
W tym artykule opisano sposób użycia usługi Azure Relay. Przykłady zostały napisane w języku C# i używają interfejsu API Windows Communication Foundation (WCF) z rozszerzeniami zawartymi w zestawie usługi Service Bus. Aby uzyskać więcej informacji na temat usługi Azure relay, zobacz [Omówienie usługi Azure Relay](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Co to jest WCF Relay?

Azure [ *przekaźnika WCF* ](relay-what-is-it.md) service umożliwia tworzenie hybrydowych aplikacji działających w centrum danych platformy Azure i w środowisku lokalnym środowisku korporacyjnym. Usługa przekaźnika ułatwia to, umożliwiając bezpieczne Uwidacznianie usług Windows Communication Foundation (WCF) znajdujących się w korporacyjnej sieci firmowej do chmury publicznej bez konieczności otwierania połączenia przez zaporę ani konieczności bez wprowadzania niepożądanych zmiany w infrastrukturze sieci firmowej.

![Pojęcia dotyczące przekaźnika WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Usługa Azure Relay umożliwia udostępnianie usług WCF w istniejącym środowisku korporacyjnym. Następnie można oddelegować nasłuchiwanie przychodzących sesji i żądań tych usług WCF do usługi relay, działającej na platformie Azure. Dzięki temu można ujawniać te usługi w kodzie aplikacji działającej na platformie Azure, pracownikom mobilnym albo środowiskom partnerów w ekstranecie. Przekaźnik pozwala na bezpieczne kontroli, kto może uzyskiwać dostęp do tych usług na poziomie szczegółowych. Jest to nie tylko wydajny i bezpieczny sposób ujawniania funkcji oraz danych aplikacji z istniejących rozwiązań korporacyjnych, ale również metoda korzystania z nich w chmurze.

W tym artykule omówiono sposób używania usługi Azure Relay do tworzenia usługi sieci web WCF, uwidaczniane za pomocą powiązania kanału TCP, który implementuje bezpieczną konwersację między dwiema stronami.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Uzyskiwanie pakietu NuGet usługi Service Bus
Dodanie [pakietu NuGet usługi Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) jest najprostszym sposobem odwołania do interfejsu API usługi Service Bus i skonfigurowania aplikacji ze wszystkimi zależnościami usługi Service Bus. Aby zainstalować pakiet NuGet w projekcie, wykonaj następujące kroki:

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
2. Wyszukaj ciąg „Service Bus” i wybierz pozycję **Microsoft Azure Service Bus**. Kliknij przycisk **Zainstaluj**, aby ukończyć instalację, a następnie zamknij poniższe okno dialogowe:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Prezentowania i wykorzystywania usługi sieci web SOAP za pomocą protokołu TCP
W celu ujawnienia istniejącej usługi sieci Web SOAP WCF na potrzeby zewnętrznego korzystania z tej usługi należy wprowadzić zmiany do jej powiązań i adresów. W tym celu konieczne może być wprowadzenie zmian do pliku konfiguracyjnego albo do kodu — w zależności od sposobu konfiguracji usług WCF. Należy pamiętać o tym, czy WCF pozwala na korzystanie z wielu punktów końcowych sieci za pośrednictwem tej samej usługi, dzięki czemu można zachować istniejące wewnętrzne punkty końcowe podczas dodawania punktem końcowym przekaźnika na potrzeby dostępu zewnętrznego w tym samym czasie.

To zadanie służy do kompilacji prostą usługę WCF i Dodaj odbiornik przekazywania do niej. W tym ćwiczeniu przyjęto, że masz pewną znajomość programu Visual Studio i w związku z tym nie omówiono wszystkich szczegółów tworzenia projektu. Zamiast tego ćwiczenie koncentruje się na kodzie.

Przed przejściem do tych kroków wykonaj następującą procedurę, aby skonfigurować swoje środowisko:

1. W programie Visual Studio utwórz aplikację konsolową, która zawiera dwa projekty — „Client” i „Service” — w ramach rozwiązania.
2. Dodaj pakiet NuGet usługi Service Bus do obu projektów. Pakiet ten doda do Twoich projektów wszystkie niezbędne odwołania do zestawu.

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

Gdy kontrakt w miejscu wdrożenia jest następująca:

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
Gdy kontrakt i implementacja są na miejscu, można hostować usługę. Hosting następuje wewnątrz obiektu [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), który zajmuje się zarządzaniem wystąpieniami usługi i hostuje punkty końcowe nasłuchujące komunikatów. Poniższy kod służy do konfigurowania usługi ze standardowym lokalnym punktem końcowym i punkt końcowy przekazywania, aby zilustrować wygląd obok siebie, wewnętrznych i zewnętrznych punktów końcowych. Zastąp ciąg *namespace* nazwą odpowiedniej przestrzeni nazw, a ciąg *yourKey* kluczem sygnatury dostępu współdzielonego uzyskanym w poprzednim kroku konfiguracji.

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

W tym przykładzie utworzysz dwa punkty końcowe należące do tej samej implementacji kontraktu. Jeden jest lokalny, a drugi jest rzutowany za pośrednictwem usługi Azure Relay. Podstawowe różnice między nimi stanowią powiązania — [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) dla lokalnego i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) dla punktu końcowego usługi relay i adresy. Lokalny punkt końcowy ma adres sieci lokalnej z unikatowym portem. Punkt końcowy usługi relay ma adres złożony z ciągu `sb`, nazwa przestrzeni nazw i ścieżki "solver". Skutkuje to identyfikator URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identyfikuje punkt końcowy usługi jako punktu końcowego TCP usługi Service Bus (przekaźnik) przy użyciu w pełni kwalifikowaną nazwą DNS. Jeśli umieścisz kod, zastępując symbole zastępcze w funkcji `Main` aplikacji **Service**, uzyskasz usługę funkcjonalną. Jeśli chcesz, aby usługa nasłuchiwała wyłącznie usługi relay, Usuń deklarację punktu końcowego.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Konfigurowanie hosta usługi w pliku App.config
Hosta można również skonfigurować przy użyciu pliku App.config. Kod hostowania usługi pojawia się w następnym przykładzie.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Definicje punktu końcowego są w tym przypadku przeniesione do pliku App.config. Pakiet NuGet już dodał szereg definicji do pliku App.config, który jest to wymagane rozszerzenia konfiguracji dla usługi Azure Relay. Poniższy przykład, który jest dokładnym odpowiednikiem poprzedniego kodu, powinien pojawić się bezpośrednio pod elementem **system.serviceModel**. Ten przykład kodu zakłada, że przestrzeń nazw języka C# w Twoim projekcie ma nazwę **Service**.
Zastąp symbole zastępcze nazwą przestrzeni nazw przekaźnika i klucza sygnatury dostępu Współdzielonego.

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
W celu korzystania z usługi możesz utworzyć klienta WCF, używając obiektu [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Usługa Service Bus używa opartego na tokenie modelu zabezpieczeń, który jest implementowany z użyciem sygnatury dostępu współdzielonego. Klasa [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) reprezentuje dostawcę tokenu zabezpieczającego z wbudowanymi metodami Factory, które zwracają dobrze znanych dostawców tokenu. W następującym przykładzie użyto metody [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) w celu obsłużenia uzyskiwania odpowiedniego tokenu sygnatury dostępu współdzielonego. Nazwa i klucz są uzyskiwane z portalu zgodnie z opisem w poprzedniej sekcji.

Najpierw należy utworzyć odwołanie do kodu kontraktu `IProblemSolver` albo skopiować ten kod z usługi do projektu Client.

Następnie Zastąp kod w `Main` metoda klienta, ponownie zastępując tekst zastępczy z przestrzeni nazw usługi relay i klucza sygnatury dostępu Współdzielonego.

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

Definicje punktu końcowego są w tym przypadku przeniesione do pliku App.config. Poniższy przykład jest taki sam jak kod przedstawiony poprzednio, powinien pojawić się bezpośrednio pod `<system.serviceModel>` elementu. W tym miejscu tak jak poprzednio, należy zastąpić symbole zastępcze z przestrzeni nazw usługi relay i klucza sygnatury dostępu Współdzielonego.

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
Teraz, kiedy znasz już podstawy usługi Azure Relay, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* Pobierz przykłady usługi Service Bus z [przykładów dla platformy Azure] [ Azure samples] lub zobacz [Przegląd przykładów usługi Service Bus][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
