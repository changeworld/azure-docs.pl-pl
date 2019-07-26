---
title: Samouczek REST z użyciem Azure Relay | Microsoft Docs
description: Utwórz prostą aplikację hosta przekaźnika Azure Service Bus, która uwidacznia Interfejs REST.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422891"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Samouczek REST usługi Azure WCF Relay
W tym samouczku opisano sposób tworzenia prostej aplikacji hosta Azure Relay, która uwidacznia Interfejs REST. Interfejs REST umożliwia klientowi sieci Web, takiemu jak przeglądarka sieci Web, uzyskiwanie dostępu do interfejsów API usługi Service Bus za pomocą żądań HTTP.

W tym samouczku jest używany model programowania REST programu Windows Communication Foundation (WCF) do konstruowania usługi REST na Azure Relay. Aby uzyskać więcej informacji, zobacz [Model programowania interfejsu REST usługi WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) oraz [Projektowanie i implementowanie usług](/dotnet/framework/wcf/designing-and-implementing-services) w dokumentacji usługi WCF.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Utwórz przestrzeń nazw przekaźnika.
> * Definiowanie kontraktu usługi WCF opartego na interfejsie REST
> * Implementowanie kontraktu WCF opartego na protokole REST
> * Hostowanie i uruchamianie usługi WCF opartej na protokole REST
> * Uruchamianie i testowanie usługi

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). W przykładach znajdujących się w tym samouczku używany jest program Visual Studio 2017.
- Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Tworzenie przestrzeni nazw przekaźnika

Aby rozpocząć korzystanie z funkcji przekazywania na platformie Azure, należy najpierw utworzyć przestrzeń nazw usługi. Przestrzeń nazw zapewnia kontener określania zakresu na potrzeby adresowania zasobów platformy Azure w aplikacji. Postępuj zgodnie z [instrukcjami podanymi w tym miejscu](relay-create-namespace-portal.md), aby utworzyć przestrzeń nazw przekazywania.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definiowanie kontraktu usługi WCF opartego na interfejsie REST do użycia z usługą Azure Relay

Podczas tworzenia usługi w stylu REST WCF należy zdefiniować kontrakt. Kontrakt określa operacje obsługiwane przez hosta. Operacja usługi może być rozumiana jako metoda usługi sieci Web. Kontrakty są tworzone przez definiowanie interfejsu C++, C# lub Visual Basic. Każda metoda w interfejsie odpowiada określonej operacji usługi. W odniesieniu do każdego interfejsu należy zastosować atrybut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), a w odniesieniu do każdej operacji należy zastosować atrybut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Jeśli metoda w interfejsie z atrybutem [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) nie ma atrybutu [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), nie jest uwidaczniana. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym na końcu procedury.

Główną różnicą między kontraktem programu WCF a kontraktem w stylu REST jest dodanie właściwości do [atrybutu OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Ta właściwość umożliwia mapowanie metody w interfejsie na metodę po drugiej stronie interfejsu. Ten przykład używa atrybutu [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) do łączenia metody z HTTP GET. Dzięki temu Service Bus dokładne pobieranie i interpretowanie poleceń wysyłanych do interfejsu.

### <a name="to-create-a-contract-with-an-interface"></a>Aby utworzyć kontrakt z interfejsem

1. Otwórz program Visual Studio jako administrator, klikając prawym przyciskiem myszy ikonę programu w menu **Start**, a następnie klikając polecenie **Uruchom jako administrator**.
2. Utwórz nowy projekt aplikacji konsoli. Kliknij menu **Plik** i wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. W oknie dialogowym **Nowy projekt** kliknij pozycję **Visual C#** , wybierz szablon **Aplikacja konsolowa** i nadaj mu nazwę **ImageListener**. Użyj domyślnej **lokalizacji**. Kliknij przycisk **OK**, aby utworzyć projekt.
3. W przypadku projektu C# program Visual Studio utworzy plik `Program.cs`. Ta klasa zawiera pustą metodę `Main()` wymaganą do prawidłowej kompilacji projektu aplikacji konsolowej.
4. Dodaj odwołania do usługi Service Bus i pliku **System.ServiceModel.dll** w projekcie, instalując pakiet NuGet usługi Service Bus. Ten pakiet automatycznie dodaje odwołania do bibliotek usługi Service Bus, jak również przestrzeń nazw **System.ServiceModel** usługi WCF. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **ImageListener**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg `Microsoft Azure Service Bus`. Kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.
5. Należy jawnie dodać odwołanie do pliku **System.ServiceModel.Web.dll** w projekcie:
   
    a. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **References** w folderze projektu i kliknij pozycję **Dodaj odwołanie**.
   
    b. W oknie dialogowym **Dodawanie odwołania** kliknij kartę **Struktura** po lewej stronie i w polu **Wyszukaj** wpisz **System.ServiceModel.Web**. Zaznacz pole wyboru **System.ServiceModel.Web** i kliknij przycisk **OK**.
6. Dodaj następujące instrukcje `using` w górnej części pliku Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) jest przestrzenią nazw umożliwiającą programowy dostęp do podstawowych funkcji usługi WCF. WCF Relay używa wielu obiektów i atrybutów WCF do definiowania kontraktów usługi. Ta przestrzeń nazw jest używana w większości aplikacji do przekazywania. Podobnie [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) ułatwia zdefiniowanie kanału, który jest obiektem, za pomocą którego komunikuje się za pomocą Azure Relay i przeglądarki sieci Web klienta. Wreszcie przestrzeń nazw [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) zawiera typy umożliwiające tworzenie aplikacji sieci Web.
7. Zmień nazwę przestrzeni nazw `ImageListener` na **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Bezpośrednio po otwierającym nawiasie klamrowym deklaracji przestrzeni nazw zdefiniuj nowy interfejs o nazwie **IImageContract** i zastosuj atrybut **ServiceContractAttribute** do interfejsu z wartością `https://samples.microsoft.com/ServiceModel/Relay/`. Wartość przestrzeni nazw różni się od przestrzeni nazw używanej w kodzie. Zamiast tego wartość przestrzeni nazw jest używana jako unikatowy identyfikator dla tego kontraktu i powinna zawierać informacje o wersji. Aby uzyskać więcej informacji, zobacz [Service Versioning](https://go.microsoft.com/fwlink/?LinkID=180498) (Obsługa wersji usług). Jawne określenie przestrzeni nazw zapobiega dodawaniu domyślnej wartości przestrzeni nazw do nazwy kontraktu.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. W ramach interfejsu `IImageContract` zadeklaruj metodę dla pojedynczej operacji uwidacznianej przez kontrakt `IImageContract` w interfejsie i zastosuj atrybut `OperationContractAttribute` do metody, którą chcesz uwidocznić jako część publicznego kontraktu usługi Service Bus.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. W atrybucie **OperationContract** dodaj wartość **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Dzięki temu usługa przekazywania może kierować żądania HTTP GET do `GetImage`i przetłumaczać `GetImage` wartości zwracane do odpowiedzi HTTP GetResponse. W dalszej części tego samouczka użyjesz przeglądarki sieci Web, aby uzyskać dostęp do tej metody i wyświetlić obraz w przeglądarce.
11. Bezpośrednio po definicji `IImageContract` zadeklaruj kanał dziedziczący z interfejsów `IImageContract` i `IClientChannel`.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Kanał jest obiektem usługi WCF, za pomocą którego usługa i klient przekazują do siebie informacje. Później utworzysz kanał w aplikacji hosta. Azure Relay następnie używa tego kanału do przekazywania żądań HTTP GET z przeglądarki do implementacji metody **GetImage** . Przekaźnik korzysta również z kanału, aby pobrać wartość  zwracaną GetImage i przetłumaczyć ją na http GetResponse dla przeglądarki klienta.
12. W menu **Kompiluj** kliknij pozycję **Kompiluj rozwiązanie**, aby potwierdzić dokładność pracy wykonanej do tej pory.

### <a name="example"></a>Przykład
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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementowanie kontraktu usługi WCF opartego na protokole REST
Tworzenie usługi WCF Relay w stylu REST wymaga najpierw utworzenia kontraktu, który jest definiowany przy użyciu interfejsu. Następnym krokiem jest zaimplementowanie interfejsu. Jest to związane z utworzeniem klasy o nazwie **ImageService** implementującej interfejs **IImageContract** zdefiniowany przez użytkownika. Po zaimplementowaniu kontraktu należy skonfigurować interfejs przy użyciu pliku App.config. Plik konfiguracji zawiera niezbędne informacje dotyczące aplikacji, takie jak nazwa usługi, nazwa kontraktu i typ protokołu, który jest używany do komunikacji z usługą przekaźnika. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

Zgodnie z poprzednimi krokami istnieje bardzo mała różnica między implementacją kontraktu w stylu REST i kontraktem WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Aby zaimplementować kontrakt usługi Service Bus oparty na interfejsie REST
1. Utwórz nową klasę o nazwie **ImageService** bezpośrednio po definicji interfejsu **IImageContract**. Klasa **ImageService** implementuje interfejs **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Podobnie jak w przypadku innych implementacji interfejsów, można zaimplementować definicję w innym pliku. Jednak w przypadku tego samouczka implementację umieszczono w tym samym pliku, w którym znajduje się definicja interfejsu i metoda `Main()`.
2. Zastosuj atrybut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) do klasy **IImageService**, aby wskazać, że klasa jest implementacją kontraktu usługi WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Jak wspomniano wcześniej, ta przestrzeń nazw nie jest tradycyjną przestrzenią nazw. Zamiast tego jest częścią architektury usługi WCF, która identyfikuje kontrakt. Aby uzyskać więcej informacji, zobacz artykuł [nazwy kontraktów danych](https://msdn.microsoft.com/library/ms731045.aspx) w dokumentacji usługi WCF.
3. Dodaj obraz jpg do projektu.  
   
    Jest to obraz, który usługa wyświetla w przeglądarce będącej odbiorcą. Kliknij prawym przyciskiem myszy projekt i kliknij polecenie **Dodaj**. Następnie kliknij pozycję **Istniejący element**. Użyj okna dialogowego **Dodawanie istniejącego elementu**, aby przejść do odpowiedniego pliku jpg, a następnie kliknij przycisk **Dodaj**.
   
    Podczas dodawania pliku upewnij się, że pozycja **Wszystkie pliki** jest zaznaczona na liście rozwijanej obok pola **Nazwa pliku:** . W pozostałej części tego samouczka przyjęto założenie, że nazwa obrazu to „image.jpg”. Jeśli masz inny plik, musisz zmienić jego nazwę lub zmienić swój kod, aby zrekompensować.
4. Aby upewnić się, że uruchomiona usługa może odnaleźć plik obrazu, w **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy plik obrazu, a następnie kliknij pozycję **Właściwości**. W okienku **Właściwości** ustaw opcję **Kopiuj do katalogu wyjściowego** na wartość **Kopiuj, jeśli nowszy**.
5. Dodaj do projektu odwołanie do zestawu **System.Drawing.dll** oraz następujące skojarzone instrukcje `using`.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. W klasie **ImageService** dodaj następujący konstruktor, który ładuje mapę bitową i przygotowuje ją do wysłania do przeglądarki klienta.
   
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
7. Bezpośrednio po poprzednim kodzie dodaj następującą metodę **GetImage** w klasie **ImageService** służącą do zwracania komunikatu protokołu HTTP zawierającego obraz.
   
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
   
    W tej implementacji element **MemoryStream** służy do pobierania obrazu i przygotowywania go do przesyłania strumieniowego do przeglądarki. Rozpoczyna przekazywanie strumieniowe w pozycji zero, deklaruje zawartość strumienia jako obraz jpeg i przekazuje strumieniowo informacje.
8. W menu **Kompiluj** kliknij pozycję **Kompiluj rozwiązanie**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Aby zdefiniować konfigurację uruchamiania usługi sieci Web w usłudze Service Bus
1. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **App.config**, aby otworzyć go w edytorze programu Visual Studio.
   
    Plik **App. config** zawiera nazwę usługi, punkt końcowy (czyli lokalizację Azure Relay uwidacznia się klientom i hostom, aby komunikować się ze sobą), oraz powiązanie (typ protokołu używanego do komunikacji). Główną różnicą jest to, że skonfigurowany punkt końcowy usługi odwołuje się do powiązania [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) .
2. Element XML `<system.serviceModel>` jest elementem usługi WCF definiującym co najmniej jedną usługę. Tutaj został użyty do zdefiniowania nazwy usługi i punktu końcowego. W dolnej części elementu `<system.serviceModel>` (ale nadal w elemencie `<system.serviceModel>`) dodaj element `<bindings>` mający następującą zawartość. Definiuje ona powiązania używane w aplikacji. Można zdefiniować wiele powiązań, ale w tym samouczku definiowane jest tylko jedno.
   
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
   
    Poprzedni kod definiuje WCF Relay powiązanie [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) z relayClientAuthenticationTypeem  ustawionym na **none**. To ustawienie wskazuje, że punkt końcowy korzystający z tego powiązania nie wymaga poświadczeń klienta.
3. Po elemencie `<bindings>` dodaj element `<services>`. Podobnie jak w przypadku powiązań, w pojedynczym pliku konfiguracji można zdefiniować wiele usług. Jednak w tym samouczku definiowana jest tylko jedna.
   
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
   
    W tym kroku konfigurowana jest usługa korzystająca z wcześniej zdefiniowanego domyślnego powiązania **webHttpRelayBinding**. Używana jest również domyślna wartość **sbTokenProvider**, która jest definiowana w następnym kroku.
4. Po elemencie Utwórz element o następującej zawartości, zastępując "SAS_KEY" kluczem *sygnatury dostępu* współdzielonego (SAS) uzyskanym wcześniej w [Azure Portal.][Azure portal] `<behaviors>` `<services>`
   
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
5. W pliku App.config w elemencie `<appSettings>` zastąp całą wartość parametrów połączenia parametrami połączenia, które zostały wcześniej uzyskane z portalu. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. W menu **Kompiluj** kliknij pozycję **Kompiluj rozwiązanie**, aby skompilować całe rozwiązanie.

### <a name="example"></a>Przykład
Poniższy kod przedstawia implementację kontraktu i usługi opartej na interfejsie REST uruchomionej w usłudze Service Bus przy użyciu powiązania **WebHttpRelayBinding**.

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

Poniższy kod przedstawia plik App.config skojarzony z usługą.

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

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hostowanie usługi WCF opartej na protokole REST w celu używania Azure Relay
W tym kroku opisano sposób uruchamiania usługi sieci Web przy użyciu aplikacji konsolowej z WCF Relay. Pełny kod napisany w tym kroku podano w przykładzie poniżej procedury.

### <a name="to-create-a-base-address-for-the-service"></a>Aby utworzyć podstawowy adres usługi
1. W deklaracji `Main()` funkcji Utwórz zmienną do przechowywania przestrzeni nazw projektu. Pamiętaj, aby zamienić `yourNamespace` na nazwę przestrzeni nazw przekaźnika, która została wcześniej utworzona.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Usługa Service Bus używa przestrzeni nazw do utworzenia unikatowego identyfikatora URI.
2. Utwórz wystąpienie identyfikatora `Uri` dla podstawowego adresu usługi, która jest oparta na tej przestrzeni nazw.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Aby utworzyć i skonfigurować hosta usługi sieci Web
* Utwórz hosta usługi sieci Web przy użyciu adresu URI utworzonego wcześniej w tej sekcji.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Host usługi jest obiektem usługi WCF tworzącym wystąpienie aplikacji hosta. W tym przykładzie przekazywany jest do niego typ hosta, który ma zostać utworzony (klasa **ImageService**) oraz adres, pod którym ma zostać uwidoczniona aplikacja hosta.

### <a name="to-run-the-web-service-host"></a>Aby uruchomić hosta usługi sieci Web
1. Otwórz usługę.
   
    ```csharp
    host.Open();
    ```
    Usługa jest teraz uruchomiona.
2. Wyświetl komunikat z informacją, że usługa jest uruchomiona, oraz informacją o sposobie zatrzymania usługi.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Po zakończeniu zamknij hosta usługi.
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>Przykład
Poniższy przykład zawiera kontrakt usługi i implementację z poprzednich kroków samouczka i hostuje usługę w aplikacji konsolowej. Skompiluj następujący kod do pliku wykonywalnego o nazwie ImageListener.exe.

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

1. Naciśnij klawisz **F5** lub przejdź do lokalizacji pliku wykonywalnego (ImageListener\bin\Debug\ImageListener.exe), aby uruchomić usługę. Nie zatrzymuj aplikacji, ponieważ będzie ona wymagana do wykonania kolejnego kroku.
2. Skopiuj i wklej adres z wiersza polecenia do przeglądarki, aby wyświetlić obraz.
3. Po zakończeniu naciśnij klawisz **Enter** w oknie wiersza polecenia, aby zamknąć aplikację.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz wbudowaną aplikację, która korzysta z usługi Azure Relay, zobacz następujące artykuły, aby dowiedzieć się więcej:

* [Omówienie usługi Azure Relay](relay-what-is-it.md)
* [Jak używać usługi przekaźnika WCF z platformą .NET](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
