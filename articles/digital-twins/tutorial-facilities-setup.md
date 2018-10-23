---
title: Wdrażanie usługi Azure Digital Twins | Microsoft Docs
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure Digital Twins i skonfigurować zasoby przestrzenne przy użyciu kroków opisanych w tym samouczku.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363337"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Samouczek: wdrażanie usługi Azure Digital Twins i konfigurowanie wykresu przestrzennego

Usługa Azure Digital Twins umożliwia połączenie osób, miejsc i urządzeń w spójny system przestrzenny. W tej serii samouczków przedstawiono sposób wykrywania zajętości pomieszczeń z optymalnymi warunkami temperatury i jakości powietrza za pomocą usługi Azure Digital Twins. Samouczki przeprowadzą Cię przez proces tworzenia w aplikacji konsoli .NET scenariusza budynku biurowego składającego się z wielu pięter i pomieszczeń na każdym piętrze. W pomieszczeniach znajdują się urządzenia z podłączonymi czujnikami, które wykrywają ruch, temperaturę otoczenia i jakość powietrza. Nauczysz się, jak za pomocą usługi Digital Twins replikować obszary i jednostki fizyczne w budynku jako obiekty cyfrowe. Przeprowadzisz symulację zdarzeń urządzeń przy użyciu innej aplikacji konsoli. Następnie dowiesz się, jak monitorować zdarzenia pochodzące z tych obszarów i jednostek fizycznych w czasie zbliżonym do rzeczywistego. Korzystając z tych informacji, administrator biura może ułatwić pracownikom tego budynku rezerwowanie sal konferencyjnych z optymalnymi warunkami. Menedżer pomieszczeń biurowych może za pomocą Twojej konfiguracji poznać trendy dotyczące wykorzystania pomieszczeń, a także monitorować warunki dla celów konserwacji.

W pierwszym samouczku tej serii poznasz następujące zagadnienia:

> [!div class="checklist"]
> * Wdrażanie usługi Digital Twins
> * Udzielanie uprawnień dla Twojej aplikacji
> * Modyfikowanie przykładowej aplikacji usługi Digital Twins
> * Aprowizowanie budynku


W tym samouczkach są używane i modyfikowane te same przykłady co w [przewodniku Szybki start dotyczącym znajdowania dostępnych pomieszczeń](quickstart-view-occupancy-dotnet.md), co pozwala uzyskać bardziej szczegółową wiedzę na temat poszczególnych pojęć.


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Przykładowe aplikacje usługi Digital Twins używane w tych samouczkach są napisane w języku C#. Zainstaluj [zestaw SDK .NET Core w wersji 2.1.403 lub nowszej](https://www.microsoft.com/net/download) na maszynie deweloperskiej w celu skompilowania i uruchomienia przykładu. Sprawdź, czy na Twojej maszynie jest zainstalowana właściwa wersja, uruchamiając polecenie `dotnet --version` w oknie poleceń.

- Program [Visual Studio Code](https://code.visualstudio.com/) umożliwiający eksplorowanie przykładowego kodu. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Wdrażanie usługi Digital Twins

Wykonaj kroki opisane w tej sekcji, aby utworzyć nowe wystąpienie usługi Digital Twins. Dla jednej subskrypcji możesz utworzyć tylko jedno wystąpienie. Jeśli wystąpienie jest już uruchomione, przejdź do następnej sekcji. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Udzielanie uprawnień dla Twojej aplikacji

Usługa Digital Twins steruje [dostępem do odczytu/zapisu](../active-directory/develop/v1-permissions-and-consent.md) w usłudze, korzystając z usługi [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Każda aplikacja, która wymaga połączenia z wystąpieniem usługi Digital Twins, musi być zarejestrowana w usłudze Azure Active Directory. W tej sekcji opisano procedurę rejestrowania przykładowej aplikacji.

Jeśli już masz *zarejestrowaną aplikację*, możesz wykorzystać ją jako przykład. Jednak przejrzyj tę sekcję, aby upewnić się, że Twoja rejestracja aplikacji jest poprawnie skonfigurowana.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Konfigurowanie przykładowej aplikacji usługi Digital Twins

W tej sekcji przejdziesz przez aplikację usługi Digital Twins, która komunikuje się z [interfejsami API REST usługi Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Pobierz przykład
Jeśli już masz przykłady pobrane na potrzeby [przewodnika Szybki start dotyczącego znajdowania dostępnych pomieszczeń](quickstart-view-occupancy-dotnet.md), możesz pominąć te kroki.

1. Pobierz [przykładowe aplikacje usługi Digital Twins dla platformy .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Wyodrębnij zawartość folderu ZIP na swojej maszynie. 

### <a name="explore-the-sample"></a>Eksplorowanie przykładu
W folderze z wyodrębnionym przykładem otwórz plik **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** w programie Visual Studio Code. Zawiera on dwa projekty: 

1. Przykład aprowizacji **_occupancy-quickstart_** pozwala skonfigurować i aprowizować [wykres analizy przestrzennej](concepts-objectmodel-spatialgraph.md#graph), czyli cyfrowy obraz miejsc fizycznych oraz znajdujących się w nich zasobów. Korzysta on z [modelu obiektów](concepts-objectmodel-spatialgraph.md#model) definiującego obiekty dla inteligentnego budynku. Aby uzyskać pełną listę obiektów usługi Digital Twins i interfejsów API REST, odwiedź stronę [dokumentacji interfejsu API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) lub skorzystaj z adresu URL **interfejsu API zarządzania**, który został utworzony na potrzeby [Twojego wystąpienia](#deploy).

   Aby zobaczyć, jak przykładowa aplikacja komunikuje się z wystąpieniem usługi Digital Twins, możesz zacząć od folderu **_src\actions_**. W plikach w tym folderze zaimplementowano polecenia, które będą używane w tych samouczkach:
    - plik *provisionSample.cs* pokazuje, jak aprowizować wykres przestrzenny,
    - plik *getSpaces.cs* pobiera informacje o aprowizowanych miejscach,
    - plik *getAvailableAndFreshSpaces.cs* pobiera wyniki funkcji niestandardowej nazywanej funkcją zdefiniowaną przez użytkownika, a
    - plik *createEndpoints.cs* tworzy punkty końcowe do interakcji z innymi usługami.

1. Przykład symulacji **_device-connectivity_** symuluje dane z czujników i wysyła je do centrum IoT aprowizowanego na potrzeby Twojego wystąpienia usługi Digital Twins. Ten przykład zostanie wykorzystany w [kolejnym samouczku po przeprowadzeniu aprowizacji wykresu przestrzennego](tutorial-facilities-udf.md#simulate). Identyfikatory czujników i urządzeń używane do skonfigurowania tego przykładu powinny być takie same jak te, które będą używane do aprowizacji wykresu.

### <a name="configure-the-provisioning-sample"></a>Konfigurowanie przykładu aprowizacji
1. Otwórz okno polecenia i przejdź do pobranego przykładu. Uruchom następujące polecenie:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Przywróć zależności w przykładowym projekcie, uruchamiając następujące polecenie:

    ```cmd/sh
    dotnet restore
    ```

1. W programie Visual Studio Code otwórz plik **_appSettings.json_** należący do projektu **occupancy-quickstart** i zaktualizuj następujące wartości:
    1. *ClientId*: wprowadź **identyfikator aplikacji** Twojej rejestracji aplikacji w usłudze AAD, który został zanotowany w sekcji poświęconej [określaniu uprawnień aplikacji](#permissions).
    1. *Tenant*: wprowadź **identyfikator katalogu** Twojej [dzierżawy usługi AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), który również został zanotowany w sekcji poświęconej [określaniu uprawnień aplikacji](#permissions).
    1. *BaseUrl*: wprowadź adres URL Twojego wystąpienia usługi Digital Twins. Aby uzyskać ten adres URL, zamień symbole zastępcze w tym adresie URL na wartości odpowiadające Twojemu wystąpieniu: **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. Możesz również uzyskać ten adres URL, modyfikując adres URL **interfejsu API zarządzania** z [sekcji poświęconej wdrażaniu](#deploy) przez zamianę ciągu **swagger/** na **api/v1.0/**.

1. Uruchom poniższe polecenie, aby wyświetlić listę funkcji usługi Digital Twins, które możesz eksplorować przy użyciu tego przykładu.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>Omówienie procesu aprowizacji
W tej sekcji pokazano, jak przykładowa aplikacja aprowizuje wykres przestrzenny budynku. 

W programie Visual Studio Code przejdź do folderu **_occupancy-quickstart\src\actions_** i otwórz plik *provisionSample.cs*. Zwróć uwagę na następującą funkcję:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Ta funkcja używa pliku *provisionSample.yaml* z tego samego folderu. Otwórz ten plik i zanotuj hierarchię budynku biura: *Venue* (Miejsce), *Floor* (Piętro), *Area* (Obszar) i *Rooms* (Pomieszczenia). W każdym z tych miejsc fizycznych mogą znajdować się *urządzenia* i *czujniki*. Każdy wpis ma wstępnie zdefiniowany typ (`type`), na przykład *Floor* (Piętro) lub *Room* (Pomieszczenie). 

Przykładowy plik *yaml* przedstawia wykres przestrzenny przy użyciu domyślnego (`Default`) modelu obiektów usługi Digital Twins. Ten model zawiera ogólne nazwy większości typów (na przykład Temperature dla SensorDataType, Map dla SpaceBlobType) oraz typy miejsc (na przykład Room z podtypami FocusRoom, ConferenceRoom itd.), co jest wystarczające w przypadku budynku. Jeśli musisz utworzyć wykres przestrzenny dla innego typu miejsca, na przykład dla fabryki, możesz potrzebować innego modelu obiektów. Uruchamiając polecenie `dotnet run GetOntologies` w wierszu polecenia dla przykładu aprowizacji, możesz dowiedzieć się, jakie modele są dostępne do użycia. Aby uzyskać więcej szczegółowych informacji na temat wykresów przestrzennych i modeli obiektów, przeczytaj [Omówienie modelu obiektów i wykresu analizy przestrzennej usługi Digital Twins](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-sample-spatial-graph"></a>Modyfikowanie przykładowego wykresu przestrzennego
Plik *provisionSample.yaml* zawiera następujące węzły:

- **resources**: węzeł `resources` tworzy w konfiguracji zasób usługi IoT Hub do komunikacji z urządzeniami. Centrum IoT w węźle głównym wykresu może komunikować się ze wszystkimi urządzeniami i czujnikami na wykresie.  

- **spaces**: w modelu obiektów usługi Digital Twins węzeł `spaces` reprezentuje lokalizacje fizyczne. Każde miejsce ma typ (`Type`), na przykład *Region*, *Venue* (Miejsce) lub *Customer* (Klient), oraz przyjazną nazwę (`Name`). Miejsca mogą należeć do innych miejsc tworzących strukturę hierarchiczną. Plik *provisionSample.yaml* zawiera wykres przestrzenny wymyślonego budynku. Zwróć uwagę na logiczne zagnieżdżanie miejsc typu `Floor` w węźle `Venue`, typu `Area` na piętrze i węzłów `Room` w obszarze. 

- **devices**: miejsca mogą zawierać urządzenia (`devices`) będące jednostkami fizycznymi lub wirtualnymi, które zarządzają pewną liczbą czujników. Na przykład urządzeniem może być telefon użytkownika, zasobnik czujników urządzenia Raspberry Pi, brama itp. W wymyślonym budynku z Twojego przykładu zwróć uwagę na sposób umieszczenia urządzenia *Raspberry Pi 3 A1* w pomieszczeniu *Focus Room*. Każdy węzeł urządzenia jest identyfikowany przez unikatowy identyfikator `hardwareId` trwale zakodowany w przykładzie. Aby skonfigurować ten przykład na potrzeby rzeczywistego środowiska produkcyjnego, zamień te wartości na wartości z Twojej konfiguracji.  

- **sensors**: urządzenie może zawierać wiele czujników (`sensors`), które mogą wykrywać i rejestrować zmiany fizyczne, takie jak temperatura, ruch, poziom naładowania baterii itp. Każdy węzeł czujnika jest unikatowo identyfikowany za pomocą identyfikatora `hardwareId` trwale zapisanego w tym miejscu. Na potrzeby rzeczywistej aplikacji zamień te identyfikatory na unikatowe identyfikatory czujników z Twojej konfiguracji. Plik *provisionSample.yaml* ma dwa czujniki umożliwiające rejestrowanie ruchu (*Motion*) i dwutlenku węgla (*CarbonDioxide*). Dodaj kolejny czujnik, aby rejestrować temperaturę (*Temperature*), dodając poniższe wiersze pod wierszami dotyczącymi czujnika CarbonDioxide. Zwróć uwagę, że znajdują się one w pliku *provisionSample.yaml* jako wiersze oznaczone jako komentarze; możesz po prostu usunąć komentarz, usuwając znak „#” na początku każdego wiersza. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Upewnij się, że klucze `dataType` i `hardwareId` pasują do instrukcji powyżej tego fragmentu kodu. Upewnij się również, że edytor nie zastępuje spacji tabulatorami. 

Zapisz i zamknij plik *provisionSample.yaml*. W następnym samouczku dodasz więcej informacji do tego pliku, a następnie przeprowadzisz aprowizację przykładowego budynku w usłudze Azure Digital Twins.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z usługą Azure Digital Twins, możesz usunąć zasoby utworzone w tym samouczku:

1. W menu po lewej stronie w witrynie [Azure Portal](http://portal.azure.com) kliknij przycisk **Wszystkie zasoby**, wybierz grupę zasobów usługi Digital Twins i kliknij polecenie **Usuń**.
2. W razie potrzeby możesz również przejść do usunięcia przykładowej aplikacji na Twojej maszynie roboczej. 


## <a name="next-steps"></a>Następne kroki

Przejdź do kolejnego samouczka w serii, aby dowiedzieć się, jak zaimplementować logikę niestandardową w celu monitorowania warunków w przykładowym budynku. 
> [!div class="nextstepaction"]
> [Samouczek: aprowizowanie budynku i monitorowanie warunków pracy](tutorial-facilities-udf.md)

