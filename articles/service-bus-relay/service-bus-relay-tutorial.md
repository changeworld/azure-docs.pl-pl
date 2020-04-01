---
title: Udostępnianie usługi REST on-prem WCF klientom korzystającym z usługi Azure Relay
description: W tym samouczku opisano sposób udostępnienia lokalnej usługi REST WCF klientowi zewnętrznemu przy użyciu usługi Azure WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76513086"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Samouczek: Uwidacznianie lokalnej usługi WCF REST klientowi zewnętrznemu przy użyciu usługi Azure WCF Relay

W tym samouczku opisano sposób tworzenia aplikacji klienckiej i usługi klienta WCF Relay przy użyciu usługi Azure Relay. Aby uzyskać podobny samouczek, który używa [obsługi wiadomości usługi Service Bus,](../service-bus-messaging/service-bus-messaging-overview.md)zobacz Wprowadzenie do [kolejek usługi Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Praca za pośrednictwem tego samouczka daje zrozumienie kroków, aby utworzyć klienta WCF relay i aplikacji usługi. Podobnie jak ich oryginalne odpowiedniki WCF, usługa jest konstrukcją, która udostępnia jeden lub więcej punktów końcowych. Każdy punkt końcowy udostępnia jedną lub więcej operacji usługi. Punkt końcowy usługi określa adres usługi, powiązanie zawierające informacje umożliwiające klientowi komunikowanie się z usługą i kontrakt definiujący funkcje zapewniane klientom przez usługę. Główną różnicą między WCF i WCF Relay jest, że punkt końcowy jest narażony w chmurze, a nie lokalnie na komputerze.

Po pracy nad sekwencją sekcji w tym samouczku będziesz mieć uruchomioną usługę. Będziesz mieć również klienta, który może wywoływać operacje usługi. 

W tym samouczku wykonaj następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Utwórz obszar nazw przekaźnika.
> * Utwórz kontrakt serwisowy WCF.
> * Wdrożenie kontraktu WCF.
> * Host i uruchom usługę WCF, aby zarejestrować się w usłudze Przekazywanie.
> * Utwórz klienta WCF dla umowy serwisowej.
> * Skonfiguruj klienta WCF.
> * Zaimplementuj klienta WCF.
> * Uruchom aplikacje.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). Przykłady w tym samouczku użyć visual studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Tworzenie przestrzeni nazw przekaźnika

Pierwszym krokiem jest utworzenie przestrzeni nazw i uzyskanie klucza [sygnatury dostępu współdzielonego (SAS, Shared Access Signature)](../service-bus-messaging/service-bus-sas.md). Przestrzeń nazw wyznacza granice każdej aplikacji uwidacznianej za pośrednictwem usługi przekaźnika. Klucz Sygnatury dostępu Współdzielonego jest generowany automatycznie przez system podczas tworzenia obszaru nazw usługi. Kombinacja przestrzeni nazw i klucza sygnatury dostępu współdzielonego usługi dostarcza poświadczenia dla platformy Azure w celu uwierzytelnienia dostępu do aplikacji.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definiowanie umowy serwisowej WCF

Umowa serwisowa określa, jakie operacje obsługuje usługa. Operacje są metody usługi sieci web lub funkcje. Kontrakty są tworzone przez definiowanie interfejsu C++, C# lub Visual Basic. Każda metoda w interfejsie odpowiada określonej operacji usługi. W odniesieniu do każdego interfejsu należy zastosować atrybut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), a w odniesieniu do każdej operacji należy zastosować atrybut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Jeśli metoda w interfejsie, który ma [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) atrybut nie ma [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) atrybut, ta metoda nie jest narażona. Kod dla tych zadań podano w przykładzie zamieszczonym na końcu procedury. Aby zapoznać się z szerszą dyskusją na temat umów i usług, zobacz [Projektowanie i wdrażanie usług](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Tworzenie kontraktu przekaźnika za pomocą interfejsu

1. Uruchom program Microsoft Visual Studio jako administrator. W tym celu kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C# i wybierz pozycję **Dalej**.
1. Nazwij projekt *EchoService* i wybierz pozycję **Utwórz**.

   ![Tworzenie aplikacji konsolowej][2]

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **WindowsAzure.ServiceBus**. Wybierz **pozycję Zainstaluj**i zaakceptuj warunki użytkowania.

    ![Pakiet Service Bus][3]

   Ten pakiet automatycznie dodaje odwołania do bibliotek usługi Service `System.ServiceModel`Bus i WCF . [System.ServiceModel](/dotnet/api/system.servicemodel) jest przestrzenią nazw umożliwiającą programowy dostęp do podstawowych funkcji platformy WCF. Usługa Service Bus używa wielu obiektów i atrybutów usługi WCF do definiowania kontraktów usług.

1. Dodaj następujące `using` instrukcje w górnej części *Program.cs:*

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Zmień nazwę przestrzeni nazw z domyślnej nazwy `EchoService` na `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > W tym samouczku użyto `Microsoft.ServiceBus.Samples` obszaru nazw języka C#, który jest obszarem nazw typu zarządzanego opartego na umowie, który jest używany w pliku konfiguracyjnym w sekcji [Konfigurowanie klienta WCF.](#configure-the-wcf-client) Podczas tworzenia tego przykładu można określić dowolną przestrzeń nazw. Jednak samouczek nie będzie działać, chyba że następnie zmodyfikować obszary nazw umowy i usługi odpowiednio w pliku konfiguracji aplikacji. Obszar nazw określony w pliku *App.config* musi być taki sam jak obszar nazw określony w plikach języka C#.
   >

1. Bezpośrednio po `Microsoft.ServiceBus.Samples` deklaracji obszaru nazw, ale w obszarze nazw, `IEchoContract` zdefiniuj nowy interfejs o nazwie i zastosuj `ServiceContractAttribute` atrybut do interfejsu o wartości obszaru nazw `https://samples.microsoft.com/ServiceModel/Relay/`. Wklej następujący kod po deklaracji obszaru nazw:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Wartość przestrzeni nazw różni się od przestrzeni nazw używanej w kodzie. Zamiast tego wartość przestrzeni nazw jest używana jako unikatowy identyfikator dla tego kontraktu. Jawne określenie przestrzeni nazw zapobiega dodawaniu domyślnej wartości przestrzeni nazw do nazwy kontraktu.

   > [!NOTE]
   > Zazwyczaj przestrzeń nazw kontraktu usługi zawiera schemat nazewnictwa uwzględniający informacje o wersji. Uwzględnienie informacji o wersji w przestrzeni nazw kontraktu usługi umożliwia usługom izolowanie istotnych zmian przez zdefiniowanie nowego kontraktu usługi z nową przestrzenią nazw i ujawnienie go w nowym punkcie końcowym. W ten sposób klienci mogą nadal korzystać ze starej umowy serwisowej bez konieczności aktualizacji. Informacje o wersji mogą zawierać datę lub numer kompilacji. Aby uzyskać więcej informacji, zobacz [Service Versioning](/dotnet/framework/wcf/service-versioning) (Obsługa wersji usług). W tym samouczku schemat nazewnictwa obszaru nazw umowy serwisowej nie zawiera informacji o wersji.
   >

1. W `IEchoContract` interfejsie zadeklarować metodę dla `IEchoContract` pojedynczej operacji kontrakt udostępnia `OperationContractAttribute` w interfejsie i zastosować atrybut do metody, które mają być uwidacznione jako część publicznego kontraktu WCF Relay, w następujący sposób:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Bezpośrednio po definicji interfejsu `IEchoContract` zadeklaruj kanał dziedziczący zarówno po `IEchoContract`, jak i interfejsie `IClientChannel`, w sposób pokazany poniżej:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Kanał jest obiektem platformy WCF, za pomocą którego host i klient przekazują do siebie informacje. Później napiszesz kod względem kanału, aby echo informacji między dwiema aplikacjami.

1. Wybierz **build** > **build solution** lub select Ctrl+Shift+B, aby potwierdzić dokładność dotychczasowej pracy.

### <a name="example-of-a-wcf-contract"></a>Przykład kontraktu WCF

Poniższy kod przedstawia podstawowy interfejs, który definiuje kontrakt WCF Relay.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Teraz, gdy interfejs został utworzony, możesz go zaimplementować.

## <a name="implement-the-wcf-contract"></a>Wdrożenie kontraktu WCF

Tworzenie przekaźnika platformy Azure wymaga, aby najpierw utworzyć kontrakt przy użyciu interfejsu. Aby uzyskać więcej informacji na temat tworzenia interfejsu, zobacz poprzednią sekcję. Następna procedura implementuje interfejs. To zadanie polega na utworzeniu klasy o nazwie, `EchoService` która implementuje interfejs zdefiniowany przez `IEchoContract` użytkownika. Po zaimplementacji interfejsu należy skonfigurować interfejs przy użyciu pliku konfiguracyjnego *app.config.* Plik konfiguracyjny zawiera niezbędne informacje dla aplikacji. Informacje te obejmują nazwę usługi, nazwę kontraktu i typ protokołu, który jest używany do komunikowania się z usługą przekazywania. Kod używany dla tych zadań jest podany w przykładzie, który następuje zgodnie z procedurą. Aby uzyskać bardziej ogólną dyskusję na temat sposobu implementacji umowy serwisowej, zobacz [Wdrażanie umów serwisowych](/dotnet/framework/wcf/implementing-service-contracts).

1. Utwórz nową klasę o nazwie `EchoService` bezpośrednio po definicji interfejsu `IEchoContract`. Klasa `EchoService` implementuje interfejs `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Podobnie jak w przypadku innych implementacji interfejsów, można zaimplementować definicję w innym pliku. Jednak w przypadku tego samouczka implementację umieszczono w tym samym pliku, w którym znajduje się definicja interfejsu i metoda `Main()`.

1. Zastosuj atrybut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) do interfejsu `IEchoContract`. Ten atrybut określa nazwę usługi i przestrzeń nazw. Po wykonaniu tych czynności klasa `EchoService` wygląda następująco:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Zaimplementuj metodę `Echo` zdefiniowaną w interfejsie `IEchoContract` w klasie `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Wybierz **pozycję Zbuduj** > **rozwiązanie kompilacji** lub wybierz ctrl+shift+B.

### <a name="define-the-configuration-for-the-service-host"></a>Definiowanie konfiguracji hosta usługi

Plik konfiguracyjny jest podobny do pliku konfiguracyjnego WCF. Zawiera nazwę usługi, punkt końcowy i powiązanie. Punkt końcowy jest lokalizacją, w których usługa Azure Relay udostępnia klientom i hostom komunikację ze sobą. Powiązanie jest typem protokołu, który jest używany do komunikowania się. Główną różnicą jest to, że ten skonfigurowany punkt końcowy usługi odwołuje się do powiązania [NetTcpRelayBinding,](/dotnet/api/microsoft.servicebus.nettcprelaybinding) które nie jest częścią programu .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) jest jednym z powiązań zdefiniowanych przez usługę.

1. W **Eksploratorze rozwiązań**kliknij dwukrotnie **app.config,** aby otworzyć plik w edytorze Visual Studio.
1. W elemencie `<appSettings>` zastąp symbole zastępcze nazwą przestrzeni nazw usługi i kluczem sygnatury dostępu współdzielonego skopiowanym we wcześniejszym kroku.
1. W tagach `<system.serviceModel>` dodaj element `<services>`. Można zdefiniować wiele aplikacji przekazywania w jednym pliku konfiguracyjnym. W tym samouczku zdefiniowano jednak tylko jedną.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. W elemencie `<services>` dodaj element `<service>`, aby zdefiniować nazwę usługi.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. W elemencie `<service>` zdefiniuj lokalizację kontraktu punktu końcowego i wpisz powiązanie dla punktu końcowego.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Punkt końcowy określa, gdzie klient będzie szukać aplikacji hosta. Później samouczek używa tego kroku, aby utworzyć identyfikator URI, który w pełni udostępnia hosta za pośrednictwem usługi Azure Relay. Powiązanie deklaruje, że używamy protokołu TCP jako protokołu do komunikowania się z usługą przekazywania.

1. Wybierz **build** > **build solution** lub select Ctrl+Shift+B, aby potwierdzić dokładność dotychczasowej pracy.

### <a name="example-of-implementation-of-a-service-contract"></a>Przykład realizacji umowy o świadczenie usług

Poniższy kod przedstawia implementację kontraktu usługi.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Poniższy kod przedstawia podstawowy format pliku *App.config* skojarzonego z hostem usługi.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hostuj i uruchamiaj usługę WCF, aby zarejestrować się w usłudze przekazywania

W tym kroku opisano sposób uruchamiania usługi Azure Relay.

### <a name="create-the-relay-credentials"></a>Tworzenie poświadczeń przekazywania

1. W procedurze `Main()` utwórz dwie zmienne do przechowywania przestrzeni nazw i klucza sygnatury dostępu współdzielonego odczytanego z okna konsoli.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Klucz Sygnatury dostępu współdzielonego zostanie użyty później do uzyskania dostępu do projektu. Przestrzeń nazw jest przekazywana jako parametr do procedury `CreateServiceUri` w celu utworzenia identyfikatora URI.

1. Za pomocą [Obiektu TransportClientEndpointBehavior,](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) oświadcz, że będziesz używać klucza sygnatury dostępu Współdzielonego jako typ poświadczeń. Dodaj poniższy kod bezpośrednio po kodzie dodanym w ostatnim kroku.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Tworzenie adresu podstawowego usługi

Po kod został dodany w poprzedniej `Uri` sekcji, należy utworzyć wystąpienie dla adresu podstawowego usługi. Ten identyfikator URI określa schemat magistrali usług, przestrzeń nazw i ścieżkę interfejsu usługi.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Wartość "sb" jest skrótem od programu Service Bus. Oznacza to, że używamy protokołu TCP jako protokołu. Ten schemat był również wcześniej wskazany w pliku konfiguracyjnym, gdy [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) został określony jako powiązanie.

W tym samouczku użyto identyfikatora URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Tworzenie i konfigurowanie hosta usługi

1. Nadal działa `Main()`w , ustaw `AutoDetect`tryb łączności na .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Tryb łączności opisuje protokół używany przez usługę do komunikowania się z usługą przekazywania; http lub TCP. Przy użyciu `AutoDetect`ustawienia domyślnego usługa próbuje połączyć się z usługą Azure Relay za pośrednictwem protokołu TCP, jeśli jest dostępna, i http, jeśli protokół TCP nie jest dostępny. Ten wynik różni się od protokołu, który usługa określa dla komunikacji z klientem. Ten protokół jest ustalany na podstawie używanego powiązania. Na przykład usługa może używać [powiązania BasicHttpRelayBinding,](/dotnet/api/microsoft.servicebus.basichttprelaybinding) które określa, że jej punkt końcowy komunikuje się z klientami za pośrednictwem protokołu HTTP. Ta sama usługa `ConnectivityMode.AutoDetect` może określić, aby usługa komunikuje się z usługi Azure Relay za pomocą protokołu TCP.

1. Utwórz hosta usługi przy użyciu identyfikatora URI utworzonego wcześniej w tej sekcji.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Host usługi jest obiektem platformy WCF tworzącym wystąpienie usługi. W tym miejscu należy przekazać go typ usługi, które chcesz utworzyć, `EchoService` typ, a także do adresu, pod którym chcesz udostępnić usługę.

1. W górnej części *pliku Program.cs* dodaj odwołania do [systemów System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) i [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Ponownie w procedurze `Main()` skonfiguruj punkt końcowy do obsługi dostępu publicznego.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ten krok informuje usługę przekazywania, że aplikację można znaleźć publicznie, badając kanał Atom dla projektu. Jeśli `DiscoveryType` ustawiono `private`na , klient może nadal uzyskać dostęp do usługi. Jednak usługa nie pojawi się podczas `Relay` przeszukiwania obszaru nazw. Zamiast tego klient musiałby wcześniej znać ścieżkę punktu końcowego.

1. Zastosuj poświadczenia usługi do punktów końcowych usługi zdefiniowanych w pliku *App.config:*

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Jak wspomniano wcześniej, można zadeklarować wiele usług i punktów końcowych w pliku konfiguracji. W takim przypadku ten kod pomijałby plik konfiguracji i wyszukiwałby każdy punkt końcowy, do którego powinien zastosować Twoje poświadczenia. W tym samouczku plik konfiguracji ma tylko jeden punkt końcowy.

### <a name="open-the-service-host"></a>Otwieranie hosta usługi

1. Nadal `Main()`w , dodaj następujący wiersz, aby otworzyć usługę.

    ```csharp
    host.Open();
    ```

1. Poinformuj użytkownika, że usługa jest uruchomiona, i wyjaśnij, jak zamknąć usługę.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po zakończeniu zamknij hosta usługi.

    ```csharp
    host.Close();
    ```

1. Wybierz klawisze Ctrl+Shift+B, aby utworzyć projekt.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Przykład obsługi usługi w aplikacji konsoli

Wypełniony kod usługi powinien być wyświetlany w następujący sposób. Kod zawiera umowę serwisową i implementację z poprzednich kroków w samouczku i obsługuje usługę w aplikacji konsoli.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Tworzenie klienta platformy WCF dla kontraktu usługi

Następnym zadaniem jest utworzenie aplikacji klienckiej i zdefiniowanie umowy serwisowej, którą zaimplementujesz później. Te kroki przypominają kroki użyte do utworzenia usługi: definiowanie umowy, edytowanie pliku *App.config,* używanie poświadczeń do łączenia się z usługą przekazywania i tak dalej. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

1. Utwórz nowy projekt w bieżącym rozwiązaniu programu Visual Studio dla klienta:

   1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy bieżące rozwiązanie (nie projekt) i wybierz pozycję **Dodaj** > **nowy projekt**.
   1. W **obszarze Dodaj nowy projekt**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C#i wybierz pozycję **Dalej**.
   1. Nazwij projekt *EchoClient* i wybierz pozycję **Utwórz**.

1. W **Eksploratorze rozwiązań**w projekcie **EchoClient** kliknij dwukrotnie **Program.cs,** aby otworzyć plik w edytorze, jeśli nie jest jeszcze otwarty.
1. Zmień nazwę przestrzeni nazw z domyślnej nazwy `EchoClient` na `Microsoft.ServiceBus.Samples`.
1. Zainstaluj [pakiet NuGet usługi Service Bus:](https://www.nuget.org/packages/WindowsAzure.ServiceBus)

   1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy pozycję **EchoClient,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
   1. Wybierz **pozycję Przeglądaj**, a następnie wyszukaj i wybierz **pozycję WindowsAzure.ServiceBus**. Wybierz **pozycję Zainstaluj**i zaakceptuj warunki użytkowania.

      ![Instalowanie pakietu magistrali usług][4]

1. Dodaj `using` instrukcję dla obszaru nazw [System.ServiceModel](/dotnet/api/system.servicemodel) w pliku *Program.cs.*

    ```csharp
    using System.ServiceModel;
    ```

1. Dodaj definicję kontraktu usługi do przestrzeni nazw jak pokazano w poniższym przykładzie. Ta definicja jest identyczna z definicją używaną w projekcie **usługi.** Dodaj ten kod w `Microsoft.ServiceBus.Samples` górnej części obszaru nazw.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Wybierz klawisze Ctrl+Shift+B, aby utworzyć klienta.

### <a name="example-of-the-echoclient-project"></a>Przykład projektu EchoClient

Poniższy kod przedstawia bieżący stan pliku *Program.cs* w projekcie **EchoClient.**

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurowanie klienta platformy WCF

W tym kroku utworzysz plik *App.config* dla podstawowej aplikacji klienckiej, która uzyskuje dostęp do usługi utworzonej wcześniej w tym samouczku. Ten plik *App.config* definiuje kontrakt, powiązanie i nazwę punktu końcowego. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

1. W **Eksploratorze rozwiązań**w projekcie **EchoClient** kliknij dwukrotnie **app.config,** aby otworzyć plik w edytorze Visual Studio.
1. W elemencie `<appSettings>` zastąp symbole zastępcze nazwą przestrzeni nazw usługi i kluczem sygnatury dostępu współdzielonego skopiowanym we wcześniejszym kroku.
1. W `system.serviceModel` obrębie elementu `<client>` dodaj element.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ten kod deklaruje, że definiujesz aplikację kliencką w stylu WCF.

1. W elemencie `client` zdefiniuj nazwę, kontrakt i typ powiązania punktu końcowego.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ten kod definiuje nazwę punktu końcowego. Definiuje również kontrakt zdefiniowany w usłudze i fakt, że aplikacja kliencka używa protokołu TCP do komunikowania się z usługą Azure Relay. Nazwa punktu końcowego jest używana w następnym kroku do powiązania tej konfiguracji pliku końcowego z identyfikatorem URI usługi.

1. Wybierz **pozycję Zapisz** > **wszystkie**pliki .

### <a name="example-of-the-appconfig-file"></a>Przykład pliku App.config

Poniższy kod przedstawia plik *App.config* dla klienta Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementowanie klienta WCF

W tej sekcji zaimplementujesz podstawową aplikację kliencką, która uzyskuje dostęp do usługi utworzonej wcześniej w tym samouczku. Podobnie jak w usłudze, klient wykonuje wiele tych samych operacji, aby uzyskać dostęp do usługi Azure Relay:

* Ustawienie trybu łączności.
* Utworzenie identyfikatora URI, który lokalizuje usługę hosta.
* Uzyskanie poświadczeń zabezpieczeń.
* Zastosowanie poświadczeń do połączenia.
* Otwarcie połączenia.
* Wykonanie zadań specyficznych dla aplikacji.
* Zamknięcie połączenia.

Jednak jedną z głównych różnic jest to, że aplikacja kliencka używa kanału do łączenia się z usługą przekazywania. Usługa korzysta z wywołania **ServiceHost**. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

### <a name="implement-a-client-application"></a>Implementowanie aplikacji klienckiej

1. Ustaw tryb łączności `AutoDetect`. Dodaj następujący kod w metodzie `Main()` aplikacji **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Zdefiniuj zmienne do przechowywania wartości przestrzeni nazw i klucza sygnatury dostępu współdzielonego odczytanego z okna konsoli.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Utwórz identyfikator URI, który definiuje lokalizację hosta w projekcie relay.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Utwórz obiekt poświadczeń dla punktu końcowego przestrzeni nazw Twojej usługi.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Utwórz fabrykę kanałów, która ładuje konfigurację opisaną w pliku *App.config.*

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Fabryka kanałów jest obiektem platformy WCF tworzącym kanał komunikacyjny dla usługi i aplikacji klienckich.

1. Zastosuj poświadczenia.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Utwórz i otwórz kanał dla usługi.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Zapisz podstawowy interfejs użytkownika i funkcje dla echa.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Kod używa wystąpienia obiektu kanału jako serwera proxy dla usługi.

1. Zamknij kanał i fabrykę.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Przykładowy kod dla tego samouczka

Wypełniony kod powinien być wyświetlany w następujący sposób. Ten kod pokazuje, jak utworzyć aplikację kliencką, jak wywołać operacje usługi i jak zamknąć klienta po zakończeniu wywołania operacji.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

1. Wybierz Ctrl+Shift+B, aby utworzyć rozwiązanie. Ta akcja tworzy zarówno projekt klienta, jak i projekt usługi utworzony w poprzednich krokach.
1. Przed uruchomieniem aplikacji klienckiej musisz upewnić się, że aplikacja usługi jest uruchomiona. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie **EchoService,** a następnie wybierz polecenie **Właściwości**.
1. W **obszarze Strony właściwości**, Projekt**uruchamiania** **wspólnych właściwości** > , a następnie wybierz pozycję **Wiele projektów startowych**. Upewnij się, że pozycja **EchoService** jest wyświetlana na początku listy.
1. Ustaw w polu **Akcja** zarówno dla projektu **EchoService**, jak i projektu **EchoClient** ustawienie **Uruchom**.

    ![Strony właściwości projektu][5]

1. Wybierz **opcję Zależności projektu**. W **obszarze Projekty**wybierz pozycję **EchoClient**. W **programie Depends**on upewnij się, że wybrano opcję **EchoService.**

    ![Zależności projektu][6]

1. Wybierz **przycisk OK,** aby zamknąć **strony właściwości**.
1. Wybierz F5, aby uruchomić oba projekty.
1. Oba okna konsoli zostaną otwarte z monitami o podanie nazwy przestrzeni nazw. Usługa musi działać najpierw, więc w oknie konsoli **EchoService** wprowadź obszar nazw, a następnie wybierz pozycję Enter.
1. Następnie konsola wyświetli monit o klucz Sygnatury dostępu Współdzielonego. Wprowadź klucz Sygnatury dostępu Współdzielonego i wybierz pozycję Enter.

    Oto przykładowe dane wyjściowe z okna konsoli. Wartości tutaj są tylko przykłady.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Aplikacja usługi wyświetla w oknie konsoli adres, na którym nasłuchuje, jak w poniższym przykładzie.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. W oknie konsoli **EchoClient** wprowadź te same informacje, które zostały wprowadzone uprzednio dla aplikacji usługi. Wprowadź te same wartości obszaru nazw usługi i klucza sygnatury dostępu Współdzielonego dla aplikacji klienckiej.
1. Po wprowadzeniu tych wartości klient otworzy kanał do usługi i będzie monitować o wprowadzenie tekstu, jak w poniższym przykładzie danych wyjściowych konsoli.

    `Enter text to echo (or [Enter] to exit):`

    Wprowadź tekst do wysłania do aplikacji serwisowej i wybierz pozycję Enter. Ten tekst jest wysyłany do usługi za pośrednictwem operacji usługi Echo i pojawia się w oknie konsoli usługi, jak w poniższym przykładzie danych wyjściowych.

    `Echoing: My sample text`

    Aplikacja kliencka odbiera wartość zwracaną przez operację `Echo`, która jest oryginalnym tekstem, i wyświetla go w oknie swojej konsoli. Poniższy tekst jest przykładem danych wyjściowych z okna konsoli klienta.

    `Server echoed: My sample text`

1. Możesz kontynuować wysyłanie wiadomości tekstowych z klienta do usługi w ten sposób. Po zakończeniu wybierz pozycję Wprowadź w oknach konsoli klienta i usługi, aby zakończyć obie aplikacje.

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Uwidacznianie lokalnej usługi WCF dla klienta WCF spoza sieci](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
