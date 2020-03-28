---
title: 'Samouczek: Samouczek REST przy użyciu usługi Azure Relay'
description: 'Samouczek: Tworzenie aplikacji hosta usługi Azure Service Bus Relay, która udostępnia interfejs oparty na rest.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718836"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Samouczek: Samouczek REST przekaźnika WCF platformy Azure

W tym samouczku opisano sposób tworzenia aplikacji hosta usługi Azure Relay, która udostępnia interfejs oparty na rest. Interfejs REST umożliwia klientowi sieci Web, takiemu jak przeglądarka sieci Web, uzyskiwanie dostępu do interfejsów API usługi Service Bus za pomocą żądań HTTP.

Samouczek używa modelu programowania REST programu Windows Communication Foundation (WCF) do konstruowania usługi REST w usłudze Azure Relay. Aby uzyskać więcej informacji, zobacz [WCF REST Model programowania](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) i [projektowania i wdrażania usług](/dotnet/framework/wcf/designing-and-implementing-services).

W tym samouczku wykonaj następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Utwórz obszar nazw przekaźnika.
> * Zdefiniuj umowę serwisową WCF opartą na rest.
> * Implementowanie umowy WCF opartej na rest.
> * Hostuj i uruchamiaj usługę WCF opartą na rest.
> * Uruchom i przetestuj usługę.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). Przykłady w tym samouczku użyć visual studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Tworzenie przestrzeni nazw przekaźnika

Aby rozpocząć korzystanie z funkcji przekazywania na platformie Azure, należy najpierw utworzyć przestrzeń nazw usługi. Przestrzeń nazw zapewnia kontener określania zakresu na potrzeby adresowania zasobów platformy Azure w aplikacji. Postępuj zgodnie z [instrukcjami podanymi w tym miejscu](relay-create-namespace-portal.md), aby utworzyć przestrzeń nazw przekazywania.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definiowanie umowy serwisowej opartej na restziemi do użycia z usługą Azure Relay

Podczas tworzenia usługi WCF REST stylu, należy zdefiniować umowy. Kontrakt określa operacje obsługiwane przez hosta. Operacja usługi przypomina metodę usługi sieci web. Zdefiniuj kontrakt za pomocą interfejsu C++, C#lub Visual Basic. Każda metoda w interfejsie odpowiada określonej operacji usługi. Zastosuj [Atrybut ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) do każdego interfejsu i zastosuj atrybut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) do każdej operacji. 

> [!TIP]
> Jeśli metoda w interfejsie, który ma [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) nie ma [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), ta metoda nie jest narażona. Kod używany dla tych zadań pojawia się w przykładzie po procedurze.

Podstawową różnicą między kontraktem WCF a kontraktem w stylu REST jest dodanie właściwości do [atrybutu OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Ta właściwość umożliwia mapowanie metody w interfejsie na metodę po drugiej stronie interfejsu. W tym przykładzie użyto atrybutu [WebGetAttribute,](/dotnet/api/system.servicemodel.web.webgetattribute) aby połączyć metodę `HTTP GET`z . Takie podejście umożliwia usługi Service Bus dokładnie pobrać i interpretować polecenia wysyłane do interfejsu.

### <a name="to-create-a-contract-with-an-interface"></a>Aby utworzyć kontrakt z interfejsem

1. Uruchom program Microsoft Visual Studio jako administrator. W tym celu kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C# i wybierz pozycję **Dalej**.
1. Nazwij projekt *ImageListener*. Użyj domyślnej **lokalizacji**, a następnie wybierz pozycję **Utwórz**.

   W przypadku projektu języka C# program Visual Studio tworzy plik *Program.cs.* Ta klasa zawiera pustą metodę `Main()` wymaganą do prawidłowej kompilacji projektu aplikacji konsolowej.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ImageListener,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
1. Wybierz **pozycję Przeglądaj**, a następnie wyszukaj i wybierz pozycję **WindowsAzure.ServiceBus**. Wybierz **pozycję Zainstaluj**i zaakceptuj warunki użytkowania.

    W tym kroku dodano odwołania do usługi Service Bus i *System.ServiceModel.dll*. Ten pakiet automatycznie dodaje odwołania do bibliotek usługi Service `System.ServiceModel`Bus i WCF .

1. Jawnie dodać odwołanie `System.ServiceModel.Web.dll` do projektu. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Odwołania** w folderze projektu, a następnie wybierz pozycję Dodaj **odwołanie**.
1. W **obszarze Dodaj odwołanie**wybierz pozycję **Framework** i enter *System.ServiceModel.Web* in **Search**. Zaznacz pole wyboru **System.ServiceModel.Web** i kliknij przycisk **OK**.

Następnie należy wprowadzić następujące zmiany kodu do projektu:

1. Dodaj następujące `using` instrukcje w górnej części pliku *Program.cs.*

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) jest przestrzenią nazw umożliwiającą programowy dostęp do podstawowych funkcji usługi WCF. WCF Relay używa wielu obiektów i atrybutów WCF do definiowania umów serwisowych. Ta przestrzeń nazw jest używana w większości aplikacji przekazywania.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) pomaga zdefiniować kanał, który jest obiektem, za pośrednictwem którego komunikujesz się z usługą Azure Relay i przeglądarką sieci web klienta.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) zawiera typy, które umożliwiają tworzenie aplikacji opartych na sieci Web.

1. Zmień nazwę `ImageListener` obszaru `Microsoft.ServiceBus.Samples`nazw na .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Bezpośrednio po otwarciu nawiasu klamrowego deklaracji obszaru nazw zdefiniuj nowy interfejs o nazwie `IImageContract` i zastosuj `ServiceContractAttribute` atrybut do interfejsu o `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`wartości . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Wartość przestrzeni nazw różni się od przestrzeni nazw używanej w kodzie. Wartość obszaru nazw jest unikatowym identyfikatorem dla tego kontraktu i powinna mieć informacje o wersji. Aby uzyskać więcej informacji, zobacz [Service Versioning](/dotnet/framework/wcf/service-versioning) (Obsługa wersji usług). Jawne określenie przestrzeni nazw zapobiega dodawaniu domyślnej wartości przestrzeni nazw do nazwy kontraktu.

1. W `IImageContract` interfejsie zadeklarować metodę dla pojedynczej operacji, że `IImageContract` umowa `OperationContract` udostępnia w interfejsie i zastosować atrybut do metody, które mają być uwidacznione w ramach umowy publicznej usługi Service Bus.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. W `OperationContract` atrybucie `WebGet` dodaj wartość.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Dodanie `WebGet` wartości umożliwia usłudze przekazywania do `GetImage`kierowania żądań HTTP `GetImage` GET `HTTP GETRESPONSE` do programów i przełożenia wartości zwracanych na odpowiedź. W dalszej części samouczka użyjesz przeglądarki internetowej, aby uzyskać dostęp do tej metody i wyświetlić obraz w przeglądarce.

1. Bezpośrednio po definicji `IImageContract` zadeklaruj kanał dziedziczący z interfejsów `IImageContract` i `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Kanał jest obiektem usługi WCF, za pomocą którego usługa i klient przekazują do siebie informacje. Później można utworzyć kanał w aplikacji hosta. Usługa Azure Relay następnie używa tego kanału do przekazywania `GetImage` żądań HTTP GET z przeglądarki do implementacji. Przekaźnik używa również kanału `GetImage` do podjęcia wartości `HTTP GETRESPONSE` zwracanej i przetłumaczyć ją na dla przeglądarki klienta.

1. Wybierz **Build** > **Build Solution,** aby potwierdzić dokładność swojej dotychczasowej pracy.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Przykład definiujący kontrakt WCF Relay

Poniższy kod przedstawia podstawowy interfejs, który definiuje kontrakt WCF Relay.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Wdrażanie umowy serwisowej opartej na rest w wynaturze

Aby utworzyć usługę przekazywania WCF w stylu REST, należy najpierw utworzyć kontrakt przy użyciu interfejsu. Następnym krokiem jest zaimplementowanie interfejsu. Ta procedura polega na utworzeniu klasy o nazwie, `ImageService` która implementuje interfejs zdefiniowany przez `IImageContract` użytkownika. Po zaimplementacji umowy, następnie skonfigurować interfejs przy użyciu pliku *App.config.* Plik konfiguracyjny zawiera niezbędne informacje dla aplikacji. Informacje te obejmują nazwę usługi, nazwę kontraktu i typ protokołu, który jest używany do komunikowania się z usługą przekazywania. Kod używany dla tych zadań pojawia się w przykładzie po procedurze.

Podobnie jak w poprzednich krokach, istnieje niewielka różnica między implementowanie umowy w stylu REST i umowy WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Aby zaimplementować kontrakt usługi Service Bus oparty na interfejsie REST

1. Utwórz nową klasę o nazwie `ImageService` bezpośrednio po definicji interfejsu `IImageContract`. Klasa `ImageService` implementuje interfejs `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Podobnie jak w przypadku innych implementacji interfejsów, można zaimplementować definicję w innym pliku. Jednak w przypadku tego samouczka implementację umieszczono w tym samym pliku, w którym znajduje się definicja interfejsu i metoda `Main()`.

1. Zastosuj [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) atrybut do `IImageService` klasy, aby wskazać, że klasa jest implementacją kontraktu WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Jak wspomniano wcześniej, ta przestrzeń nazw nie jest tradycyjną przestrzenią nazw. Jest to część architektury WCF, która identyfikuje kontrakt. Aby uzyskać więcej informacji, zobacz [nazwy kontraktów danych](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Dodaj obraz *jpg* do projektu. Ten plik jest obrazem wyświetlanym przez usługę w przeglądarce odbierającej.

   1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**.
   1. Następnie wybierz **pozycję Istniejący element**.
   1. Użyj **opcji Dodaj istniejący element,** aby przejść do odpowiedniego punktu rozszerkowego, a następnie wybierz pozycję **Dodaj**. Podczas dodawania pliku wybierz **pozycję Wszystkie pliki** z listy rozwijanej obok pozycji Nazwa **pliku**.

   Reszta tego samouczka zakłada, że nazwa obrazu jest *image.jpg*. Jeśli masz inny plik, należy zmienić nazwę obrazu lub zmienić kod, aby zrekompensować.

1. Aby upewnić się, że uruchomiona usługa może znaleźć plik obrazu, w **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy plik obrazu, a następnie wybierz polecenie **Właściwości**. W **oknie Właściwości**ustaw polecenie **Kopiuj na Katalog wyjściowy** na **Kopiuj, jeśli jest nowszy**.

1. Użyj procedury w [Aby utworzyć kontrakt z interfejsem,](#to-create-a-contract-with-an-interface) aby dodać odwołanie do zestawu *System.Drawing.dll* do projektu.

1. Dodaj następujące skojarzone instrukcje: `using`

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. W `ImageService` klasie dodaj następujący konstruktor, który ładuje mapę bitową i przygotowuje się do wysłania jej do przeglądarki klienta:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Bezpośrednio po poprzednim kodzie dodaj `GetImage` następującą `ImageService` metodę w klasie, aby zwrócić komunikat HTTP zawierający obraz.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Ta implementacja `MemoryStream` służy do pobierania obrazu i przygotowania go do przesyłania strumieniowego do przeglądarki. Rozpoczyna pozycję strumienia na zero, deklaruje zawartość strumienia jako *jpg*i strumieniuje informacje.

1. Wybierz **opcję Zbuduj** > **rozwiązanie kompilacji**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Aby zdefiniować konfigurację uruchamiania usługi sieci Web w usłudze Service Bus

1. W **Eksploratorze rozwiązań**kliknij dwukrotnie **app.config,** aby otworzyć plik w edytorze Visual Studio.

    Plik *App.config* zawiera nazwę usługi, punkt końcowy i powiązanie. Punkt końcowy jest lokalizacją, w których usługa Azure Relay udostępnia klientom i hostom komunikację ze sobą. Powiązanie jest typem protokołu, który jest używany do komunikowania się. Główną różnicą w tym miejscu jest to, że skonfigurowany punkt końcowy usługi odwołuje się do [powiązania WebHttpRelayBinding.](/dotnet/api/microsoft.servicebus.webhttprelaybinding)

1. Element XML `<system.serviceModel>` jest elementem usługi WCF definiującym co najmniej jedną usługę. W tym miejscu służy do definiowania nazwy usługi i punktu końcowego. W dolnej `<system.serviceModel>` części elementu, `<system.serviceModel>`ale nadal `<bindings>` w obrębie , dodać element, który ma następującą zawartość:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Ta zawartość definiuje powiązania używane w aplikacji. Można zdefiniować wiele powiązań, ale w tym samouczku definiujesz tylko jeden.

    Poprzedni kod definiuje powiązanie WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) z `relayClientAuthenticationType` zestawem na `None`. To ustawienie wskazuje, że punkt końcowy przy użyciu tego powiązania nie wymaga poświadczenia klienta.

1. Po elemencie `<bindings>` dodaj element `<services>`. Podobnie jak w przypadku powiązań, w pojedynczym pliku konfiguracji można zdefiniować wiele usług. Jednak w tym samouczku definiowana jest tylko jedna.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Ta zawartość konfiguruje usługę, która używa `webHttpRelayBinding`wcześniej zdefiniowanego domyślnego . Używa również domyślnego `sbTokenProvider`, który jest zdefiniowany w następnym kroku.

1. Po `<services>` elemencie `<behaviors>` utwórz element z `SAS_KEY` następującą zawartością, zastępując kluczem sygnatury dostępu współdzielonego (SAS). Aby uzyskać klucz Sygnatury dostępu Współdzielonego z [witryny Azure portal,][Azure portal]zobacz [Uzyskiwanie poświadczeń zarządzania](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Nadal w *App.config* `<appSettings>` , w elemencie, zastąp całą wartość ciągu połączenia ciągiem połączenia uzyskanym wcześniej z portalu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Wybierz **build** > **build solution,** aby utworzyć całe rozwiązanie.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Przykład implementuje umowę serwisową opartą na rest w wylewie usług

Poniższy kod przedstawia implementację umowy i usługi dla usługi opartej `WebHttpRelayBinding` na rest, która jest uruchomiona w usłudze Service Bus przy użyciu powiązania.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

W poniższym przykładzie przedstawiono plik *App.config* skojarzony z usługą.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hostuj usługę WCF opartą na rest do korzystania z usługi Azure Relay

W tej sekcji opisano sposób uruchamiania usługi sieci web przy użyciu aplikacji konsoli z WCF Relay. Pełna lista kodu napisanego w tej sekcji pojawia się w przykładzie po wykonaniu procedury.

### <a name="to-create-a-base-address-for-the-service"></a>Aby utworzyć podstawowy adres usługi

1. W `Main()` deklaracji funkcji utwórz zmienną do przechowywania obszaru nazw projektu. Pamiętaj, aby `yourNamespace` zastąpić ją nazwą utworzonej wcześniej przestrzeni nazw przekazu.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Usługa Service Bus używa przestrzeni nazw do utworzenia unikatowego identyfikatora URI.

1. Utwórz wystąpienie identyfikatora `Uri` dla podstawowego adresu usługi, która jest oparta na tej przestrzeni nazw.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Aby utworzyć i skonfigurować hosta usługi sieci Web

Nadal `Main()`w , utwórz hosta usługi sieci web, przy użyciu adresu identyfikatora URI utworzonego wcześniej w tej sekcji.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Host usługi jest obiektem usługi WCF tworzącym wystąpienie aplikacji hosta. W tym przykładzie przekazuje go typ hosta, `ImageService`który chcesz utworzyć, który jest , a także adres, pod którym chcesz udostępnić aplikację hosta.

### <a name="to-run-the-web-service-host"></a>Aby uruchomić hosta usługi sieci Web

1. Nadal `Main()`w , dodaj następujący wiersz, aby otworzyć usługę.

    ```csharp
    host.Open();
    ```

    Usługa jest teraz uruchomiona.

1. Wyświetl komunikat z informacją, że usługa jest uruchomiona, oraz informacją o sposobie zatrzymania usługi.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po zakończeniu zamknij hosta usługi.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Przykład umowy o świadczenie usług i realizacji

Poniższy przykład zawiera kontrakt usługi i implementację z poprzednich kroków samouczka i hostuje usługę w aplikacji konsolowej. Skompiluj następujący kod do pliku wykonywalnego o nazwie *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Uruchamianie i testowanie usługi

Po utworzeniu rozwiązania należy wykonać następujące polecenie, aby uruchomić aplikację:

1. Wybierz opcję F5 lub przejdź do lokalizacji pliku wykonywalnego, *ImageListener\bin\Debug\ImageListener.exe*, aby uruchomić usługę. Utrzymuj aplikację w pracy, ponieważ jest to wymagane do następnego kroku.
1. Skopiuj i wklej adres z wiersza polecenia do przeglądarki, aby wyświetlić obraz.
1. Po zakończeniu wybierz pozycję Wprowadź w oknie wiersza polecenia, aby zamknąć aplikację.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy została sypilna aplikacja, która korzysta z usługi Azure Relay, zobacz następujące artykuły, aby dowiedzieć się więcej:

* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Udostępnianie lokalnej usługi WCF REST klientowi zewnętrznemu przy użyciu usługi Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
