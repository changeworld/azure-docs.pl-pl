---
title: Hybrydowa aplikacja lokalna/chmurowa programu Azure Windows Communication Foundation (WCF) Relay (.NET) | Dokumenty firmy Microsoft
description: Dowiedz się, jak uwidocznić lokalną usługę WCF dla aplikacji internetowej w chmurze za pomocą usługi Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212968"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Uwidacznianie lokalnej usługi WCF dla aplikacji internetowej w chmurze za pomocą usługi Azure Relay

Ten artykuł przedstawia sposób tworzenia hybrydowej aplikacji w chmurze przy użyciu platformy Microsoft Azure i programu Visual Studio. Utwórz aplikację, która używa wielu zasobów platformy Azure w chmurze. Ten samouczek pomaga nauczyć się:

* Jak utworzyć lub przystosować istniejącą usługę sieci Web do użytku przez rozwiązanie sieci Web.
* Jak używać usługi Azure Windows Communication Foundation (WCF) Relay service do udostępniania danych między aplikacją platformy Azure a usługą sieci web hostowanych w innym miejscu.

W tym samouczku wykonaj następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Przegląd scenariusza.
> * Tworzenie przestrzeni nazw.
> * Utwórz serwer lokalny.
> * Tworzenie aplikacji ASP .NET.
> * Uruchom aplikację lokalnie.
> * Wdrażanie aplikacji sieci web na platformie Azure.
> * Uruchom aplikację na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). Przykłady w tym samouczku użyć visual studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Jak usługa Azure Relay pomaga w tworzeniu rozwiązań hybrydowych

Rozwiązania biznesowe zazwyczaj składają się z kombinacji kodu niestandardowego i istniejących funkcji. Niestandardowy kod spełnia nowe i unikalne wymagania biznesowe. Rozwiązania i systemy, które już istnieją, zapewniają istniejące funkcje.

Architekci rozwiązań zaczynają stosować usługi w chmurze w celu łatwiejszej obsługi wymagań skali i obniżenia kosztów operacyjnych. W ten sposób okazuje się, że istniejące zasoby usługi, które chcieliby używać jako bloków konstrukcyjnych dla swoich rozwiązań, znajdują się wewnątrz zapory firmowej i są niedostępne dla rozwiązania w chmurze. Wiele usług wewnętrznych nie są budowane lub hostowane w taki sposób, że mogą być łatwo widoczne na krawędzi sieci firmowej.

[Usługa Azure Relay](https://azure.microsoft.com/services/service-bus/) pobiera istniejące usługi sieci Web WCF i udostępnia te usługi w bezpieczny sposób dla rozwiązań znajdujących się poza granicami firmy bez konieczności natrętnych zmian w infrastrukturze sieci firmowej. Takie usługi przekazywania wciąż są hostowane wewnątrz istniejącego środowiska, ale delegują one nasłuchiwanie sesji i żądań przychodzących do usługi przekazywania hostowanej w chmurze. Usługa Azure Relay chroni także te usługi przed nieautoryzowanym dostępem przy użyciu uwierzytelniania za pomocą [sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md) (SAS, Shared Access Signature).

## <a name="review-the-scenario"></a>Przegląd scenariusza

W tym samouczku utworzysz witrynę internetową ASP.NET, która umożliwi wyświetlanie listy produktów na stronie spisu produktów.

![Scenariusz][0]

W samouczku założono, że informacje o produktach znajdują się w istniejącym systemie lokalnym i uzyskujesz dostęp do tego systemu za pomocą usługi Azure Relay. Usługa sieci web, która działa w prostej aplikacji konsoli symuluje tę sytuację. Zawiera zestaw produktów w pamięci. Tę aplikację konsoli można uruchomić na własnym komputerze i wdrożyć rolę sieci web na platformie Azure. W ten sposób zobaczysz, jak rola sieci web uruchomiona w wywołaniu centrum danych platformy Azure do komputera. To wywołanie ma miejsce, nawet jeśli komputer prawie na pewno będzie za co najmniej jedną zaporą i warstwą translacji adresów sieciowych (NAT).

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem tworzenia aplikacji dla platformy Azure pobierz potrzebne narzędzia i skonfiguruj swoje środowisko deweloperskie:

1. Zainstaluj zestaw Azure SDK dla platformy .NET ze [strony pobierania](https://azure.microsoft.com/downloads/) zestawów SDK.
1. W kolumnie **.NET** wybierz wersję programu [Visual Studio,](https://www.visualstudio.com) której używasz. W tym samouczku użyto programu Visual Studio 2019.
1. Po wyświetleniu monitu o uruchomienie lub zapisanie instalatora wybierz pozycję **Uruchom**.
1. W oknie dialogowym **Instalator platformy sieci Web** wybierz pozycję **Zainstaluj** i kontynuuj instalację.

Po zakończeniu instalacji masz wszystko, co niezbędne do rozpoczęcia tworzenia aplikacji. Zestaw SDK zawiera narzędzia, które pozwalają w łatwy sposób tworzyć aplikacje dla platformy Azure w programie Visual Studio.

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw

Pierwszym krokiem jest utworzenie przestrzeni nazw i uzyskanie klucza [sygnatury dostępu współdzielonego (SAS, Shared Access Signature)](../service-bus-messaging/service-bus-sas.md). Przestrzeń nazw wyznacza granice każdej aplikacji uwidacznianej za pośrednictwem usługi przekaźnika. Klucz Sygnatury dostępu Współdzielonego jest generowany automatycznie przez system podczas tworzenia obszaru nazw usługi. Kombinacja przestrzeni nazw i klucza sygnatury dostępu współdzielonego usługi dostarcza poświadczenia dla platformy Azure w celu uwierzytelnienia dostępu do aplikacji.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Tworzenie serwera lokalnego

Najpierw utworzysz symulowany system katalogu produktów w środowisku lokalnym.  Ten projekt jest aplikacją konsolową programu Visual Studio i używa [pakietu NuGet usługi Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) w celu uwzględnienia bibliotek i ustawień konfiguracji usługi Service Bus. <a name="create-the-project"></a>

1. Uruchom program Microsoft Visual Studio jako administrator. W tym celu kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz pozycję Aplikacja konsoli **(.NET Framework)** dla języka C# i wybierz pozycję **Dalej**.
1. Nazwij projekt *ProductsServer* i wybierz pozycję **Utwórz**.

   ![Konfigurowanie nowego projektu][11]

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ProductsServer,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
1. Wybierz **pozycję Przeglądaj**, a następnie wyszukaj i wybierz pozycję **WindowsAzure.ServiceBus**. Wybierz **pozycję Zainstaluj**i zaakceptuj warunki użytkowania.

   ![Wybieranie pakietu NuGet][13]

   Wymagane zestawy klientów są teraz przywoływane.

1. Dodaj nową klasę dla kontraktu produktu. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ProductsServer** i wybierz polecenie **Dodaj** > **klasę**.
1. W **obszarze Nazwa**wprowadź nazwę *ProductsContract.cs* i wybierz pozycję **Dodaj**.

W rozwiązaniu należy wprowadzić następujące zmiany w kodzie:

1. W pliku *ProductsContract.cs* zastąp definicję przestrzeni nazw następującym kodem, który definiuje kontrakt dla usługi.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. W *Program.cs*, zastąp definicję obszaru nazw następującym kodem, który dodaje usługę profilu i hosta dla niego.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. W **Eksploratorze rozwiązań**kliknij dwukrotnie **app.config,** aby otworzyć plik w edytorze Visual Studio. W dolnej `<system.ServiceModel>` części elementu, `<system.ServiceModel>`ale nadal w obrębie , dodać następujący kod XML. Pamiętaj, aby `yourServiceNamespace` zastąpić nazwą obszaru nazw `yourKey` i kluczem Sygnatury dostępu Współdzielonego pobranego wcześniej z portalu:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Błąd spowodowany `transportClientEndpointBehavior` przez jest tylko ostrzeżenie i nie jest problem blokowania w tym przykładzie.

1. Nadal w *App.config* `<appSettings>` , w elemencie, zastąp wartość ciągu połączenia ciągiem połączenia uzyskanym wcześniej z portalu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Wybierz ctrl+Shift+B lub wybierz **build** > **build solution,** aby utworzyć aplikację i zweryfikować dokładność dotychczasowej pracy.

## <a name="create-an-aspnet-application"></a>Tworzenie aplikacji ASP.NET

W tej sekcji utworzysz prostą aplikację ASP.NET, która będzie wyświetlać dane pobrane z usługi produktów.

### <a name="create-the-project"></a>Tworzenie projektu

1. Upewnij się, że program Visual Studio jest uruchomiony jako administrator.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W **obszarze Tworzenie nowego projektu**wybierz ASP.NET aplikacji sieci Web **(.NET Framework)** dla języka C# i wybierz pozycję **Dalej**.
1. Nazwij projekt *ProductsPortal* i wybierz pozycję **Utwórz**.
1. W **obszarze Tworzenie nowej ASP.NET aplikacji sieci Web**wybierz pozycję **MVC** i wybierz pozycję **Zmień** w obszarze **Uwierzytelnianie**.

   ![Wybieranie aplikacji internetowej ASP.NET][16]

1. W **obszarze Zmień uwierzytelnianie**wybierz pozycję **Brak uwierzytelniania,** a następnie wybierz przycisk **OK**. W tym samouczku wdrażasz aplikację, która nie potrzebuje użytkownika do logowania.

    ![Określanie uwierzytelniania][18]

1. Powrót **do Tworzenia nowej aplikacji sieci Web ASP.NET**wybierz pozycję **Utwórz,** aby utworzyć aplikację MVC.
1. Konfigurowanie zasobów platformy Azure dla nowej aplikacji sieci web. Wykonaj kroki opisane w [aplikacji Publikuj aplikację internetową](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Następnie wróć do tego samouczka i przejdź do następnego kroku.
1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy pozycję **Modele,** a następnie wybierz polecenie **Dodaj** > **klasę**.
1. Nazwij klasę *Product.cs*, a następnie wybierz pozycję **Dodaj**.

    ![Tworzenie modelu produktu][17]

### <a name="modify-the-web-application"></a>Modyfikowanie aplikacji internetowej

1. W pliku *Product.cs* w programie Visual Studio zastąp istniejącą definicję obszaru nazw następującym kodem:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. W **Eksploratorze rozwiązań**rozwiń węzeł **Kontrolery**, a następnie kliknij dwukrotnie **HomeController.cs,** aby otworzyć plik w programie Visual Studio.
1. W pliku *HomeController.cs* zastąp istniejącą definicję przestrzeni nazw następującym kodem:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. W **Eksploratorze rozwiązań**rozwiń rozwiń **widoki** > **udostępnione**, a następnie kliknij dwukrotnie **_Layout.cshtml,** aby otworzyć plik w edytorze Visual Studio.
1. Zmień wszystkie wystąpienia `My ASP.NET Application` produktów *Northwind Traders*.
1. Usuń `Home`, `About`i `Contact` łącza. W poniższym przykładzie usuń wyróżniony kod.

    ![Usuwanie wygenerowanych elementów listy][41]

1. W **Eksploratorze rozwiązań**rozwiń rozwiń **widoków** > **domowych**, a następnie kliknij dwukrotnie **index.cshtml,** aby otworzyć plik w edytorze Visual Studio. Zastąp całą zawartość pliku następującym kodem:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Aby sprawdzić dokładność dotychczasowej pracy, można wybrać klawisze Ctrl+Shift+B, aby utworzyć projekt.

### <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację, aby sprawdzić, czy działa.

1. Upewnij się, że projekt **ProductsPortal** jest aktywnym projektem. Kliknij prawym przyciskiem myszy nazwę projektu w **Eksploratorze rozwiązań** i wybierz polecenie **Ustaw jako projekt startowy**.
1. W programie Visual Studio wybierz pozycję F5.

Aplikacja powinna uruchomić się w przeglądarce.

![Aplikacja internetowa][21]

## <a name="put-the-pieces-together"></a>Składanie fragmentów

Następny krok polega na połączeniu lokalnego serwera produktów z aplikacją ASP.NET.

1. Jeśli nie jest jeszcze otwarty, w programie Visual Studio otwórz projekt **ProductsPortal** utworzony w sekcji [Tworzenie aplikacji ASP.NET.](#create-an-aspnet-application)
1. Podobnie jak w kroku w [sekcji Tworzenie serwera lokalnego,](#create-an-on-premises-server) dodaj pakiet NuGet do odwołań do projektu. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ProductsPortal,** a następnie wybierz polecenie **Zarządzaj pakietami NuGet**.
1. Wyszukaj ciąg *WindowsAzure.ServiceBus*, a następnie wybierz element **WindowsAzure.ServiceBus**. Następnie zakończ instalację i zamknij to okno dialogowe.
1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ProductsPortal,** a następnie wybierz polecenie **Dodaj** > **istniejący element**.
1. Przejdź do pliku *ProductsContract.cs* z projektu konsolowego **ProductsServer**. Wyróżnij *ProductsContract.cs*. Zaznacz strzałkę w dół obok **pozycji Dodaj,** a następnie wybierz pozycję **Dodaj jako łącze**.

   ![Dodaj jako link][24]

1. W edytorze programu Visual Studio otwórz plik *HomeController.cs* i zastąp definicję przestrzeni nazw następującym kodem. Pamiętaj, aby `yourServiceNamespace` zastąpić nazwą obszaru nazw usługi `yourKey` i kluczem Sygnatury dostępu Współdzielonego. Ten kod umożliwia klientowi wywołanie usługi lokalnej, zwracając wynik wywołania.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie **ProductsPortal.** Upewnij się, że kliknij prawym przyciskiem myszy rozwiązanie, a nie projekt. Wybierz **pozycję Dodaj** > **istniejący projekt**.
1. Przejdź do projektu **ProductsServer**, a następnie kliknij dwukrotnie plik rozwiązania *ProductsServer.csproj*, aby go dodać.
1. Aby wyświetlić dane na portalu **ProductsPortal,** musi być uruchomiony program **ProductsServer.** W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie **ProductsPortal** i wybierz polecenie **Właściwości,** aby wyświetlić **strony właściwości**.
1. Wybierz pozycję**Projekt uruchamiania** **wspólnych właściwości** > i wybierz pozycję **Wiele projektów startowych**. Upewnij się, że w tej kolejności pojawi się **ProductsServer** i **ProductsPortal,** a **akcja** dla obu jest **startowa**.

      ![Wiele projektów startowych][25]

1. Wybierz opcję**Zależności projektu** **właściwości** > wspólnych po lewej stronie.
1. W przypadku **opcji Projekty**wybierz pozycję **ProductsPortal**. Upewnij się, że projekt **ProductsServer** jest wybrany.

    ![Zależności projektu][26]

1. W przypadku **opcji Projekty**wybierz pozycję **ProductsServer**. Upewnij się, że **produktPortal** nie jest zaznaczony, a następnie wybierz **przycisk OK,** aby zapisać zmiany.

## <a name="run-the-project-locally"></a>Lokalne uruchamianie projektu

Aby przetestować aplikację lokalnie, w programie Visual Studio wybierz F5. Serwer **lokalny, ProductsServer,** należy uruchomić najpierw, a następnie **ProductsPortal** aplikacja powinna zostać uruchomiony w oknie przeglądarki. Tym razem pojawi się spis produktów zawierający dane pobrane z lokalnego systemu usługi produktów.

![Aplikacja internetowa][10]

Wybierz **pozycję Odśwież** na stronie **ProductsPortal.** Przy każdym odświeżeniu strony aplikacja serwera wyświetla komunikat podczas wywoływany metody `GetProducts()` z serwera **ProductsServer**.

Zamknij obie aplikacje przed przejściem do następnej sekcji.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Wdrażanie projektu ProductsPortal w aplikacji internetowej platformy Azure

Następnym krokiem jest ponowne opublikowanie aplikacji Sieci Web Usługi **AzurePortal** frontonijny:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **ProductsPortal,** a następnie wybierz polecenie **Publikuj**. Na stronie **Publikowanie** wybierz pozycję **Publikuj**.

   > [!NOTE]
   > Gdy projekt sieci Web **ProductsPortal** zostanie automatycznie uruchomiony po wdrożeniu, w oknie przeglądarki może zostać wyświetlony komunikat o błędzie. Jest to oczekiwane. Błąd występuje, ponieważ aplikacja **ProductsServer** nie jest jeszcze uruchomiona.
   >

1. Skopiuj adres URL wdrożonej aplikacji sieci web. Adres URL będzie potrzebny później. Ten adres URL można również uzyskać z okna **Aktywność usługi Azure App Service** w programie Visual Studio:

   ![Adres URL wdrożonej aplikacji][9]

1. Zamknij okno przeglądarki, aby zatrzymać działającą aplikację.

<a name="set-productsportal-as-web-app"></a>Przed uruchomieniem aplikacji w chmurze, należy upewnić się, że **ProductsPortal** jest uruchamiany z poziomu programu Visual Studio jako aplikacji sieci web.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **ProductsPortal** i wybierz polecenie **Właściwości**.
1. Wybierz **pozycję Sieć Web**. W obszarze **Akcja początkowa**wybierz pozycję **Start URL**. Wprowadź adres URL wcześniej wdrożonej aplikacji internetowej w `https://productsportal20190906122808.azurewebsites.net/`tym przykładzie .

    ![Początkowy adres URL][27]

1. Wybierz **pozycję Zapisz** > **wszystkie**pliki .
1. Wybierz **opcję Zbuduj** > **rozwiązanie do odbudowy**.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Wybierz F5, aby utworzyć i uruchomić aplikację. Serwer lokalny, który jest aplikacją konsoli **ProductsServer,** powinien zostać uruchomiony najpierw, a następnie aplikacja **ProductsPortal** powinna zostać uruchomiony w oknie przeglądarki, jak pokazano poniżej:

   ![Uruchamianie aplikacji internetowej na platformie Azure][1]

Spis produktów zawiera listę danych pobranych z usługi produktowej w systemie lokalnym i wyświetla te dane w aplikacji sieci web. Sprawdź adres URL, aby upewnić się, że aplikacja **ProductsPortal** działa w chmurze jako aplikacja internetowa platformy Azure.

   > [!IMPORTANT]
   > Aplikacja konsolowa **ProductsServer** musi działać i być w stanie udostępniać dane aplikacji **ProductsPortal**. Jeśli przeglądarka wyświetli błąd, poczekaj kilka sekund, aby **program ProductsServer** załadował i wyświetlił następujący komunikat, a następnie odśwież przeglądarkę.
   >

W przeglądarce odśwież stronę **ProductsPortal.** Przy każdym odświeżeniu strony aplikacja serwera wyświetla komunikat podczas wywoływany metody `GetProducts()` z serwera **ProductsServer**.

![Zaktualizowane dane wyjściowe][38]

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Uwidacznianie lokalnej usługi WCF dla klienta WCF spoza sieci](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
