---
title: 'Samouczek: Wdrażanie analizy przestrzennej IoT | Mapy platformy Microsoft Azure'
description: Integracja usługi IoT Hub z interfejsami API usługi Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cfea9aa7bfcc9a9698bb93bdf54797481b8539ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333960"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Samouczek: Implementowanie analizy przestrzennej IoT przy użyciu usługi Azure Maps

W scenariuszu IoT jest wspólne do przechwytywania i śledzenia istotnych zdarzeń, które występują w przestrzeni i czasie. Przykładowe scenariusze obejmują zarządzanie flotą, śledzenie zasobów, mobilność i aplikacje inteligentnego miasta. W tym samouczku można przejść przez wzorzec rozwiązania przy użyciu interfejsów API usługi Azure Maps. Odpowiednie zdarzenia są przechwytywane przez Centrum IoT, przy użyciu modelu subskrypcji zdarzeń udostępnianego przez siatkę zdarzeń.

W tym poradniku będziesz:

> [!div class="checklist"]
> * Tworzenie centrum IoT Hub.
> * Przekazywanie obszaru geofencingu w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych.
> * Tworzenie funkcji w usłudze Azure Functions, implementowanie logiki biznesowej na podstawie analizy przestrzennej usługi Azure Maps.
> * Subskrybuj zdarzenia telemetryczne urządzenia IoT z funkcji platformy Azure za pośrednictwem usługi Event Grid.
> * Filtrowanie zdarzeń telemetrycznych przy użyciu routingu komunikatów usługi IoT Hub.
> * Utwórz konto magazynu, aby przechowywać odpowiednie dane zdarzeń.
> * Symuluj urządzenie IoT w pojeździe.
    

## <a name="use-case"></a>Przypadek użycia

To rozwiązanie pokazuje scenariusz, w którym wypożyczalnia samochodów planuje monitorować i rejestrować zdarzenia dla swoich wypożyczonych samochodów. Wypożyczalnie samochodów zazwyczaj wynajmują samochody do określonego regionu geograficznego. Muszą śledzić miejsca pobytu samochodów, podczas gdy są one wynajmowane. Należy zarejestrować przypadki samochodu opuszczającego wybrany region geograficzny. Rejestrowanie danych gwarantuje prawidłowe zarządzanie zasadami, opłatami i innymi aspektami biznesowymi.

W naszym przypadku użycia wynajem samochodów są wyposażone w urządzenia IoT, które regularnie wysyłają dane telemetryczne do usługi Azure IoT Hub. Dane telemetryczne obejmują bieżącą lokalizację i wskazują, czy silnik samochodu jest uruchomiony. Schemat lokalizacji urządzenia jest zgodny ze [schematem IoT Plug and Play dla danych geoprzestrzennych](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Schemat telemetrii urządzenia wypożyczonego samochodu wygląda następująco:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": { 
            "location": { 
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            } 
        } 
    }
}
```

Użyjmy telemetrii urządzenia w pojeździe, aby osiągnąć nasz cel. Chcemy wykonać reguły geofencingu. I chcemy reagować za każdym razem, gdy otrzymamy zdarzenie wskazujące, że samochód się poruszał. W tym celu zasubskrybujemy zdarzenia telemetryczne urządzenia z usługi IoT Hub za pośrednictwem usługi Event Grid. 

Istnieje kilka sposobów subskrybowania usługi Event Grid, w tym samouczku używamy usługi Azure Functions. Usługa Azure Functions reaguje na zdarzenia opublikowane w siatce zdarzeń. Implementuje również logikę biznesową wynajmu samochodów, która jest oparta na analizie przestrzennej usługi Azure Maps. 

Kod wewnątrz funkcji platformy Azure sprawdza, czy pojazd opuścił geofence. Jeśli pojazd opuścił geofence, funkcja platformy Azure zbiera dodatkowe informacje, takie jak adres skojarzony z bieżącą lokalizacją. Funkcja implementuje również logikę do przechowywania istotnych danych zdarzeń w magazynie obiektów blob danych, który pomaga podać opis okoliczności zdarzenia. 

Okoliczności zdarzenia mogą być pomocne dla wypożyczalni samochodów i klienta wynajmu. Na poniższym diagramie przedstawiono omówienie systemu na wysokim poziomie.

 
  <center>

  ![Omówienie systemu](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Poniższy rysunek przedstawia obszar geofence wyróżniony na niebiesko. Trasa wypożyczonego pojazdu jest oznaczona zieloną linią.

  ![Trasa Geofence](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Wymagania wstępne 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby wykonać kroki opisane w tym samouczku, należy najpierw utworzyć grupę zasobów w witrynie Azure portal. Aby utworzyć grupę zasobów, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Wybierz pozycję **Grupy zasobów**.
    
   ![Grupy zasobów](./media/tutorial-iot-hub-maps/resource-group.png)

3. W obszarze **Grupy zasobów**wybierz pozycję **Dodaj**.
    
   ![Dodawanie grupy zasobów](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Wprowadź następujące wartości właściwości:
    * **Subskrypcja:** Wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** Wprowadź "ContosoRental" jako nazwę grupy zasobów.
    * **Region:** Wybierz region dla grupy zasobów.  

    ![Szczegóły grupy zasobów](./media/tutorial-iot-hub-maps/resource-details.png)

    Wybierz **pozycję Recenzja + utwórz**, a następnie wybierz pozycję **Utwórz** na następnej stronie.

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Aby zaimplementować logikę biznesową opartą na analizie przestrzennej usługi Azure Maps, musimy utworzyć konto usługi Azure Maps w utworzonej przez nas grupie zasobów. Postępuj zgodnie z instrukcjami w [Tworzenie konta,](quick-demo-map-app.md#create-an-account-with-azure-maps) aby utworzyć subskrypcję konta usługi Azure Maps w warstwie cenowej S1. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby rejestrować dane zdarzeń, utworzymy ogólnego przeznaczenia **v2storage,** który zapewnia dostęp do wszystkich usług usługi Azure Storage: obiekty blob, pliki, kolejki, tabele i dyski.  Musimy umieścić to konto magazynu w grupie zasobów "ContosoRental", aby przechowywać dane jako obiekty blob. Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami [dotyczącymi tworzenia konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Następnie musimy utworzyć kontener do przechowywania obiektów blob. Wykonaj poniższe czynności, aby to zrobić:

1. W "konto magazynu - obiekt blob, plik, tabela, kolejka", przejdź do kontenerów.

    ![Bloków blob](./media/tutorial-iot-hub-maps/blobs.png)

2. Kliknij przycisk kontenera w lewym górnym rogu i nazwij kontener "contoso-rental-logs" i kliknij "OK".

    ![kontener obiektu blob](./media/tutorial-iot-hub-maps/blob-container.png)

3. Przejdź do bloku **kluczy dostępu** na koncie magazynu i skopiuj "nazwę konta magazynu" i "klucz dostępu". Są one potrzebne w późniejszym kroku.

    ![klawisze dostępu](./media/tutorial-iot-hub-maps/access-keys.png)


Teraz mamy konto magazynu i kontener do rejestrowania danych zdarzeń. Następnie utworzymy centrum IoT Hub.

### <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Centrum IoT to usługa zarządzana w chmurze. Centrum IoT działa jako centralne centrum komunikatów dla dwukierunkowej komunikacji między aplikacją IoT a urządzeniami przez nią zarządzanymi. Aby rozsyłać komunikaty telemetryczne urządzenia do siatki zdarzeń, utwórz Centrum IoT w grupie zasobów "ContosoRental". Skonfiguruj integrację trasy komunikatów, w której będziemy filtrować wiadomości na podstawie stanu silnika samochodu. Będziemy również wysyłać komunikaty telemetryczne urządzenia do siatki zdarzeń, gdy samochód jest w ruchu.

> [!Note] 
> Funkcja usługi IoT Hub do publikowania zdarzeń telemetrycznych urządzenia w usztywnieniu zdarzeń znajduje się w publicznej wersji zapoznawczej. Publiczne funkcje w wersji zapoznawczej są dostępne we wszystkich regionach z wyjątkiem wschodnich stanów **USA, zachodnich stanów USA, Europy Zachodniej, platformy Azure Government, platformy Azure China 21Vianet** i **platformy Azure Germany.** 

Utwórz Centrum Iot, wykonując kroki opisane w [sekcji Centrum IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Rejestrowanie urządzenia 

Aby połączyć się z centrum IoT Hub, urządzenie musi być zarejestrowane. Aby zarejestrować urządzenie w centrum IoT Hub, wykonaj poniższe czynności:

1. W centrum IoT Hub kliknij ostrze "Urządzenia IoT" i kliknij "Nowy".

    ![urządzenie dodatkowe](./media/tutorial-iot-hub-maps/add-device.png)

2. Na stronie utwórz urządzenie nazwij urządzenie IoT i kliknij "Zapisz".
    
    ![zarejestruj urządzenie](./media/tutorial-iot-hub-maps/register-device.png)

3. Zapisz **ciąg połączenia podstawowego** urządzenia, aby użyć go w późniejszym kroku, w którym należy zmienić symbol zastępczy za pomocą tego ciągu połączenia.

    ![urządzenie dodatkowe](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Przesyłanie geograficznego

Użyjemy [aplikacji Postman](https://www.getpostman.com) do [przekazywania geograficznego znaczników](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) do usługi Azure Maps przy użyciu interfejsu API przekazywania danych usługi Azure Maps. Każde zdarzenie, gdy samochód znajduje się poza tym geofence będą rejestrowane.

Otwórz aplikację Postman i wykonaj poniższe czynności, aby przekazać geofence przy użyciu interfejsu API azure maps, przekazywania danych.  

1. W aplikacji Listonosz kliknij nowy | Utwórz nowy i wybierz pozycję Żądanie. Wprowadź nazwę żądania na potrzeby przekazywania danych geofencing, wybierz docelową kolekcję lub folder zapisywania, a następnie kliknij pozycję Save (Zapisz).

    ![Przekazywanie geofencingu przy użyciu narzędzia Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Na karcie konstruktora wybierz metodę POST HTTP i wprowadź poniższy adres URL, aby utworzyć żądanie POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Wartość "geojson" względem `dataFormat` parametru w ścieżce adresu URL reprezentuje format przesyłanych danych.

3. Kliknij pozycję **Params** (Parametry), a następnie wprowadź poniższą parę klucz-wartość, która będzie używana w przypadku adresu URL żądania POST. Zamień wartość klucza subskrypcji kluczem usługi Azure Maps.
   
    ![Parametry klucz-wartość narzędzia Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Kliknij **pozycję Treść,** a następnie wybierz **nieprzetworzony** format wejściowy i wybierz **JSON (aplikację/tekst)** jako format wejściowy z listy rozwijanej. Otwórz plik danych JSON [w tym miejscu](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)i skopiuj Json w sekcji treści jako dane do przekazania i kliknij przycisk **Wyślij**.
    
    ![publikowanie danych](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Przejrzyj nagłówki odpowiedzi. Po pomyślnym **żądaniu** nagłówek Lokalizacja będzie zawierał identyfikator URI stanu, aby sprawdzić bieżący stan żądania przekazania. Identyfikator URI stanu będzie miał następujący format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Skopiuj identyfikator URI stanu i dołącz do niego `subscription-key` parametr. Przypisz wartość klucza subskrypcji konta `subscription-key` usługi Azure Maps do parametru. Format identyfikatora URI stanu powinien być `{Subscription-key}` podobny do poniższego i zastąpiony kluczem subskrypcji.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Aby uzyskać, `udId` otwórz nową kartę w aplikacji Postman i wybierz metodę GET HTTP na karcie konstruktora i zgładź żądanie GET przy identyfikatorze URI stanu. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz identyfikator udId w treści odpowiedzi. Skopiuj identyfikator udId do późniejszego użycia.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Następnie utworzymy funkcję platformy Azure w ramach grupy zasobów "ContosoRental", a następnie skonfigurujemy trasę wiadomości w Centrum IoT, aby filtrować komunikaty telemetryczne urządzenia.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Tworzenie funkcji platformy Azure i dodawanie subskrypcji usługi Event Grid

Usługa Azure Functions to usługa obliczeniowa bez użycia serwera, która umożliwia nam uruchamianie kodu na żądanie, bez konieczności jawnego inicjowania obsługi administracyjnej lub zarządzania infrastrukturą obliczeniową. Aby dowiedzieć się więcej o usłudze Azure Functions, zapoznaj się z dokumentacją [funkcji platformy Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

Logika implementująca w funkcji używa danych lokalizacji pochodzących z telemetrii urządzenia w pojeździe do oceny stanu geofence. W przypadku, gdy dany pojazd wykracza poza geofence, funkcja będzie zbierać więcej informacji, takich jak adres lokalizacji za pośrednictwem [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Ten interfejs API tłumaczy współrzędną danej lokalizacji na ludzki, zrozumiały adres ulicy. 

Wszystkie istotne informacje o zdarzeniu są następnie przechowywane w magazynie obiektów blob. Krok 5 poniżej wskazuje na kod wykonywalny implementujący taką logikę. Wykonaj poniższe kroki, aby utworzyć funkcję platformy Azure, która wysyła dzienniki danych do kontenera obiektów blob na koncie magazynu obiektów blob i dodaj do niego subskrypcję usługi Event Grid.

1. Na pulpicie nawigacyjnym portalu Azure wybierz pozycję Utwórz zasób. Wybierz **pozycję Oblicz** z listy dostępnych typów zasobów, a następnie wybierz pozycję Aplikacja **funkcji**.

    ![tworzenie-zasób](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na stronie Tworzenie **aplikacji funkcji** nazwij aplikację funkcji. W obszarze **Grupa zasobów**wybierz pozycję **Użyj istniejącego**i wybierz "ContosoRental" z listy rozwijanej. Wybierz ".NET Core" jako stos środowiska wykonawczego. W obszarze **Hosting**dla **konta magazynowania**wybierz nazwę konta magazynu z poprzedniego kroku. W naszym poprzednim kroku nazwaliśmy konto pamięci **masowej v2storage**.  Następnie wybierz pozycję **Recenzja+Utwórz**.
    
    ![tworzenie aplikacji](./media/tutorial-iot-hub-maps/rental-app.png)

2. Przejrzyj szczegóły aplikacji funkcji i wybierz "Utwórz".

3. Po utworzeniu aplikacji musimy dodać do niej funkcję. Przejdź do aplikacji funkcji. Kliknij **pozycję Nowa funkcja,** aby dodać funkcję, a następnie wybierz pozycję **W portalu** jako środowisko programistyczne. Następnie wybierz przycisk **Kontynuuj**.

    ![tworzenie-funkcja](./media/tutorial-iot-hub-maps/function.png)

4. Wybierz **pozycję Więcej szablonów** i kliknij pozycję **Zakończ i wyświetl szablony**. 

5. Wybierz szablon za pomocą **wyzwalacza siatki zdarzeń platformy Azure**. Zainstaluj rozszerzenia, jeśli zostanie wyświetlony monit, nazwij funkcję i wybierz pozycję **Utwórz**.

    ![szablon funkcji](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Wyzwalacz usługi Azure Event Hub** i **wyzwalacz siatki zdarzeń platformy Azure** mają podobne ikony. Upewnij się, że wybrano **wyzwalacz siatki zdarzeń platformy Azure**.

6. Skopiuj [kod C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do swojej funkcji.
 
7. W skrypcie języka C# zastąp następujące parametry. Kliknij przycisk **Zapisz**. Nie klikaj jeszcze **przycisku Uruchom**
    * Zastąp **SUBSCRIPTION_KEY** kluczem subskrypcji konta usługi Azure Maps.
    * Wymień **UDID** na identyfikator udId przesłanej geofence, 
    * Funkcja **CreateBlobAsync** w skrypcie tworzy obiekt blob na zdarzenie na koncie magazynu danych. Zastąp **ACCESS_KEY** **, ACCOUNT_NAME**i **STORAGE_CONTAINER_NAME** kluczem dostępu konta magazynu, nazwą konta i kontenerem magazynu.

10. Kliknij subskrypcję **Dodaj siatkę zdarzeń**.
    
    ![siatka zdarzenia dodatku](./media/tutorial-iot-hub-maps/add-egs.png)

11. Wypełnij szczegóły subskrypcji w obszarze **SZCZEGÓŁY SUBSKRYPCJI WYDARZENIA** nazwij subskrypcję wydarzenia. Dla schematu zdarzeń wybierz "Schemat siatki zdarzeń". W obszarze **SZCZEGÓŁY TEMATU** wybierz "Konta usługi Azure IoT Hub" jako typ tematu. Wybierz tę samą subskrypcję, która została użyta do utworzenia grupy zasobów, wybierz "ContosoRental" jako "Grupa zasobów". Wybierz Centrum IoT utworzone jako "Zasób". Wybierz **dane telemetryczne urządzenia** jako typ zdarzenia. Po wybraniu tych opcji zostanie automatycznie wyświetlna zmiana "Typ tematu" na "Centrum IoT".

    ![subskrypcja usługi event-grid](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrowanie zdarzeń przy użyciu routingu komunikatów usługi IoT Hub

Po dodaniu subskrypcji usługi Event Grid do funkcji platformy Azure zostanie wyświetlona domyślna trasa wiadomości do siatki zdarzeń w bloku **routingu wiadomości** usługi IoT Hub. Routing wiadomości umożliwia kierowanie różnych typów danych do różnych punktów końcowych. Na przykład można rozsyłać komunikaty telemetryczne urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany bliźniaczej reprezentacji urządzenia. Aby dowiedzieć się więcej o routingu komunikatów w centrum IoT, zobacz [Korzystanie z routingu komunikatów w centrum IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![trasa hub-EG](./media/tutorial-iot-hub-maps/hub-route.png)

W naszym przykładzie chcemy odfiltrować wszystkie wiadomości, w których porusza się wypożyczony pojazd. Aby opublikować takie zdarzenia telemetryczne urządzenia w siatce zdarzeń, użyjemy kwerendy routingu do filtrowania zdarzeń, w `Engine` których właściwość jest **"ON".** Istnieją różne sposoby wykonywania zapytań dotyczących komunikatów z urządzenia do chmury IoT, aby dowiedzieć się więcej o składni routingu wiadomości, zobacz [Routing wiadomości w centrum IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Aby utworzyć kwerendę routingu, kliknij trasę **RouteToEventGrid** i zastąp **kwerendę routingu** **"Engine='ON",** a następnie kliknij przycisk **Zapisz**. Teraz centrum IoT będzie publikować dane telemetryczne tylko wtedy, gdy aparat jest włączony.

![hub-EG-filtr](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Wysyłanie danych telemetrycznych do usługi IoT Hub

Po uruchomieniu naszej funkcji platformy Azure możemy teraz wysyłać dane telemetryczne do centrum IoT Hub, które będą kierować je do siatki zdarzeń. Użyjmy aplikacji C#, aby symulować dane o lokalizacji dla urządzenia w pojeździe wypożyczonego samochodu. Aby uruchomić aplikację, potrzebujesz .NET Core SDK 2.1.0 lub większej na komputerze deweloperskim. Wykonaj poniższe czynności, aby wysłać symulowane dane telemetryczne do usługi IoT Hub.

1. Pobierz projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#. 

2. Otwórz simulatedCar.cs plik w wybranym edytorze tekstu i zastąp wartość `connectionString` zapisanej podczas rejestracji urządzenia i zapisanie zmian w pliku.
 
3. Upewnij się, że na komputerze jest zainstalowany program .NET Core. W oknie terminala lokalnego przejdź do folderu głównego projektu języka C# i uruchom następujące polecenie, aby zainstalować wymagane pakiety dla symulowanej aplikacji urządzenia:
    
    ```cmd/sh
    dotnet restore
    ```

4. W tym samym terminalu uruchom następujące polecenie, aby zbudować i uruchomić aplikację symulacyjna wynajmu samochodów:

    ```cmd/sh
    dotnet run
    ```

  Twój lokalny terminal powinien wyglądać jak poniżej.

  ![Wyjście zacisku](./media/tutorial-iot-hub-maps/terminal.png)

Jeśli teraz otworzysz kontener magazynu obiektów blob, powinieneś być w stanie zobaczyć cztery obiekty blob dla lokalizacji, w których pojazd znajdował się poza geofence.

![Wprowadź obiekt blob](./media/tutorial-iot-hub-maps/blob.png)

Poniższa mapa pokazuje cztery punkty, w których pojazd znajdował się poza geofence, rejestrowany w regularnych odstępach czasu.

![mapa naruszeń](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z interfejsami API usługi Azure Maps używanymi w tym samouczku, zobacz:

* [Odwróć adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Uzyskaj geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby uzyskać pełną listę interfejsów API REST usługi Azure Maps, zobacz:

* [Interfejsy API REST usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby dowiedzieć się więcej o uszczypnieniu IoT Plug and Play, zobacz:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Aby uzyskać listę urządzeń z certyfikatem platformy Azure dla IoT, odwiedź stronę:

* [Urządzenia z certyfikatem platformy Azure](https://catalog.azureiotsolutions.com/)

Aby dowiedzieć się więcej o wysyłaniu urządzenia do telemetrii w chmurze i odwrotnie, zobacz:

* [Wysyłanie danych telemetrycznych z urządzenia](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
