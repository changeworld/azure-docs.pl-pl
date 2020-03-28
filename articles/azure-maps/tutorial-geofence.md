---
title: 'Samouczek: Tworzenie geograficznej liczby i śledzenie urządzeń na mapie | Mapy platformy Microsoft Azure'
description: Dowiedz się, jak skonfigurować geofence i śledzić urządzenia względem geofence za pomocą usługi przestrzennej Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333868"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Samouczek: Konfigurowanie geograficznego znacznika za pomocą usługi Azure Maps

Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Rozważmy ten scenariusz, kierownik budowy musi monitorować potencjalne niebezpieczne urządzenia. Zarządca musi upewnić się, że sprzęt pozostaje w wybranych ogólnych obszarach budowlanych. Ten ogólny obszar konstrukcyjny jest parametrem twardym. Przepisy wymagają, aby sprzęt pozostawał w granicach tego parametru, a naruszenia są zgłaszane do działu operacyjnego.  

Używamy interfejsu API przesyłania danych do przechowywania geofence i używamy interfejsu API Geofence, aby sprawdzić lokalizację sprzętu w stosunku do geofence. Interfejs API przekazywania danych i interfejs API geofence pochodzą z usługi Azure Maps. Korzystamy również z usługi Azure Event Grid do przesyłania strumieniowego wyników geofence i konfigurowania powiadomień na podstawie wyników geofence. Aby dowiedzieć się więcej na temat usługi Event Grid, zobacz [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

W tym samouczku omówimy, jak:

> [!div class="checklist"]
> * Przekazywanie obszaru geofencingu w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych.
> *   Konfigurowanie usługi Event Grid na potrzeby obsługi zdarzeń geofencingu.
> *   Konfigurowanie obsługi zdarzeń geofencingu.
> *   Konfigurowanie alertów w odpowiedzi na zdarzenia geofencingu przy użyciu aplikacji logiki.
> *   Śledzenie przy użyciu interfejsów API usługi geofencingu usługi Azure Maps, czy zasób budowlany znajduje się na terenie budowy.


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Postępuj zgodnie z instrukcjami w [Tworzenie konta,](quick-demo-map-app.md#create-an-account-with-azure-maps) aby utworzyć subskrypcję konta usługi Azure Maps w warstwie cenowej S1. Kroki w [celu uzyskania klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) pokazują, jak pobrać klucz podstawowy konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Przekazywanie geofencingu

Zakładamy, że głównym geofence jest podwitryna1, który ma ustawiony czas wygaśnięcia. W zależności od potrzeb możesz utworzyć więcej zagnieżdżonych wirtualnych ogrodzeń. Te zestawy ogrodzeń mogą być wykorzystywane do śledzenia różnych obszarów budowlanych w całym obszarze budowy. Na przykład podwitryna1 może być miejscem, w którym trwają prace w tygodniu od 1 do 4 harmonogramu. podite2 może być miejscem, w którym prace odbywają się w tygodniu od 5 do 7. Wszystkie takie ogrodzenia mogą być ładowane jako pojedynczy zestaw danych na początku projektu. Ogrodzenia te są używane do śledzenia reguł na podstawie czasu i przestrzeni. 

Aby przesłać geofence dla placu budowy za pomocą interfejsu API przekazywania danych, używamy aplikacji listonosza. Zainstaluj [aplikację listonosza](https://www.getpostman.com/) i zagnieć darmowe konto. 

Po zainstalowaniu aplikacji Postman wykonaj następujące kroki, aby przekazać geofence witryny budowy przy użyciu interfejsu API azure maps, przekazywania danych.

1. Otwórz aplikację Postman i kliknij pozycję New | Create new (Nowe | Utwórz nowe), a następnie wybierz pozycję Request (Żądanie). Wprowadź nazwę żądania na potrzeby przekazywania danych geofencing, wybierz docelową kolekcję lub folder zapisywania, a następnie kliknij pozycję Save (Zapisz).

    ![Przekazywanie geofencingu przy użyciu narzędzia Postman](./media/tutorial-geofence/postman-new.png)

2. Na karcie konstruktora wybierz metodę POST HTTP i wprowadź poniższy adres URL, aby utworzyć żądanie POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr GEOJSON w ścieżce adresu URL reprezentuje format przekazywanych danych.

3. Kliknij pozycję **Params** (Parametry), a następnie wprowadź poniższą parę klucz-wartość, która będzie używana w przypadku adresu URL żądania POST. Zastąp {klucz subskrypcji} kluczem subskrypcji usługi Azure Maps, znanym również jako klucz podstawowy.
   
    ![Parametry przesyłania danych (geofence) w postman](./media/tutorial-geofence/postman-key-vals.png)

4. Kliknij **pozycję Treść,** a następnie wybierz nieprzetworzony format wejściowy i wybierz JSON jako format wejściowy z listy rozwijanej. Jako dane do przekazania podaj następujące dane JSON:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Kliknij przycisk wysyłania i przejrzyj nagłówek odpowiedzi. Po pomyślnym **żądaniu** nagłówek Lokalizacja będzie zawierał identyfikator URI stanu. Identyfikator URI stanu ma następujący format. Wartość uploadStatusId nie jest między { }. Jest powszechną praktyką, aby użyć { }, aby pokazać wartości, które użytkownik musi wprowadzić, lub wartości, które są różne dla różnych użytkowników.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Skopiuj identyfikator URI stanu i dołącz klucz subskrypcji. Format identyfikatora URI stanu powinien być podobny do poniższego. Należy zauważyć, że w poniższym formacie należy zmienić {subscription-key}, nie zawiera { }, z kluczem subskrypcji.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Aby uzyskać `udId`, otwórz nową kartę w aplikacji Postman i wybierz GET HTTP metody na karcie konstruktora. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz identyfikator udId w treści odpowiedzi. Skopiuj identyfikator udId do późniejszego użycia.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Konfigurowanie programu obsługi zdarzeń

W tej sekcji tworzymy program obsługi zdarzeń, który odbiera powiadomienia. Ten program obsługi zdarzeń powinien powiadamiać program Operations Manager o zdarzeniach wejścia i zakończenia dowolnego sprzętu.

Wprowadzamy dwie usługi [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) do obsługi zdarzeń enter i exit. Gdy zdarzenia w wyzwalaczu aplikacji logiki, więcej zdarzeń wyzwalać w sekwencji. Chodzi o to, aby wysyłać alerty, w tym przypadku wiadomości e-mail, do programu Operations Manager. Poniższa ilustracja przedstawia tworzenie aplikacji logiki na potrzeby zdarzenia wjazdu na obszar wirtualnego ogrodzenia. W podobny sposób można utworzyć zdarzenie wyjazdu. Aby uzyskać więcej informacji, możesz wyświetlić wszystkie [obsługiwane programy obsługi zdarzeń](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Tworzenie aplikacji logiki w witrynie Azure portal. Wybierz aplikację logiki w portalu Azure Marketplace. Następnie wybierz przycisk **Utwórz.**

   ![Tworzenie aplikacji Azure Logic Apps w celu obsługi zdarzeń geofence](./media/tutorial-geofence/logic-app.png)

2. W menu ustawień aplikacji logiki przejdź do **projektanta aplikacji logiki**

3. Wybierz wyzwalacz żądania HTTP, a następnie wybierz "Nowy krok". W łączniku programu Outlook wybierz opcję "Wyślij wiadomość e-mail" jako akcję
  
   ![Schemat usługi Logic Apps](./media/tutorial-geofence/logic-app-schema.png)

4. Wypełnij pola, aby wysłać wiadomość e-mail. Zostaw adres URL HTTP, zostanie on automatycznie wygenerowany po kliknięciu przycisku "Zapisz"

   ![Generowanie punktu końcowego aplikacji logiki](./media/tutorial-geofence/logic-app-endpoint.png)

5. Zapisz aplikację logiki, aby wygenerować punkt końcowy adresu URL HTTP i skopiuj adres URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Tworzenie subskrypcji zdarzeń usługi Azure Maps

Usługa Azure Maps obsługuje trzy typy zdarzeń. Obsługiwanym typom zdarzeń usługi Azure Maps można przyjrzeć się [tutaj](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Potrzebujemy dwóch różnych subskrypcji zdarzeń, jednej dla zdarzenia enter i jednej dla zdarzeń zakończenia.

Wykonaj poniższe kroki, aby utworzyć subskrypcję dla zdarzeń wjazdu wirtualnego ogrodzenia. Zdarzenia wyjazdu wirtualnego ogrodzenia możesz subskrybować w podobny sposób.

1. Przejdź do konta usługi Azure Maps. Na pulpicie nawigacyjnym wybierz pozycję Subskrypcje. Kliknij nazwę subskrypcji i wybierz **zdarzenia** z menu ustawień.

   ![Przechodzenie do zdarzeń konta usługi Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Aby utworzyć subskrypcję zdarzeń, wybierz pozycję Subskrypcja zdarzeń ze strony zdarzeń.

   ![Tworzenie subskrypcji zdarzeń usługi Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nadaj nazwę subskrypcji zdarzeń i rozpocznij subskrypcję typu zdarzenia Wjazd. Teraz wybierz web hook jako "Typ punktu końcowego". Kliknij "Wybierz punkt końcowy" i skopiuj punkt końcowy adresu URL HTTP aplikacji logiki do "{Endpoint}"

   ![Szczegóły subskrypcji zdarzeń usługi Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Używanie interfejsu API wirtualnego ogrodzenia

Za pomocą interfejsu API Geofence można sprawdzić, czy **urządzenie**, w tym przypadku sprzęt, znajduje się wewnątrz lub na zewnątrz geofence. Umożliwia wykonywanie zapytań o interfejs API Geofence GET w różnych lokalizacjach, w których określony sprzęt został przeniesiony w czasie. Poniższy rysunek przedstawia pięć lokalizacji z pięcioma urządzeniami budowlanymi. 

> [!Note]
> Scenariusz i zachowanie jest oparty na tym samym **identyfikatorze urządzenia,** dzięki czemu odzwierciedla pięć różnych lokalizacji, jak na poniższym rysunku.

"DeviceId" to unikatowy identyfikator, który można podać dla urządzenia w żądaniu GET podczas wykonywania zapytań o jego lokalizację. Po włączeniu asynchroniczna żądanie do **geofence wyszukiwania — GET API**, "deviceId" pomaga w publikowaniu zdarzeń geofence dla tego urządzenia, względem określonego geofence. W tym samouczku wykonaliśmy asynchroniczne żądania do interfejsu API z unikatowym "deviceId". Żądania w samouczku są wykonane w porządku chronologicznym, jak na diagramie. Właściwość "isEventPublished" w odpowiedzi zostanie opublikowana za każdym razem, gdy urządzenie wchodzi lub kończy działanie geograficzne. Nie musisz rejestrować urządzenia, aby wykonać ten samouczek.

Spójrzmy wstecz na diagram. Każda z tych pięciu lokalizacji służy do oceny stanu wjazdów i wyjazdów na terenie geofencingu względem ogrodzenia. W przypadku zmiany stanu usługa geofencingu wyzwala zdarzenie, które jest wysyłane do aplikacji logiki przez usługę Event Grid. W rezultacie menedżer operacji otrzyma odpowiednie powiadomienie o wejściu lub wyjściu za pośrednictwem wiadomości e-mail.

![Mapa geograficzna w mapach platformy Azure](./media/tutorial-geofence/geofence.png)

W aplikacji Postman otwórz nową kartę w tej samej kolekcji, która została utworzona powyżej. Na karcie konstruktora wybierz metodę GET HTTP:

Poniżej przedstawiono pięć żądań interfejsu API HTTP GET Geofencing z różnymi współrzędnymi lokalizacji sprzętu. Współrzędne są obserwowane w porządku chronologicznym. Po każdym żądaniu następuje treść odpowiedzi.
 
1. Lokalizacja 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Zapytanie geofencingu 1](./media/tutorial-geofence/geofence-query1.png)

   W powyższej odpowiedzi ujemna odległość od głównego geofence oznacza, że urządzenie znajduje się wewnątrz geofence. Dodatnia odległość od geofence podtytu oznacza, że urządzenie znajduje się poza geofence podwitryną. 

2. Lokalizacja 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Zapytanie geofencingu 2](./media/tutorial-geofence/geofence-query2.png)

   Jeśli spojrzysz uważnie na poprzednią odpowiedź JSON, sprzęt znajduje się poza podwitryną, ale znajduje się wewnątrz głównego ogrodzenia. Żadne zdarzenie nie jest wyzwalane i nie jest wysyłana żadna wiadomość e-mail.

3. Lokalizacja 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 3](./media/tutorial-geofence/geofence-query3.png)

   Nastąpiła zmiana stanu i sprzęt znajduje się teraz zarówno w obrębie głównego wirtualnego ogrodzenia, jak i wirtualnego ogrodzenia terenu podrzędnego. Ta zmiana powoduje, że zdarzenie do opublikowania i wiadomość e-mail z powiadomieniem zostanie wysłana do programu Operations Manager.

4. Lokalizacja 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Zapytanie geofencingu 4](./media/tutorial-geofence/geofence-query4.png)

   Obserwując dokładnie odpowiednią odpowiedź, możesz zauważyć, że w tym miejscu nie jest publikowane żadne zdarzenie, chociaż sprzęt wyjechał poza wirtualne ogrodzenie terenu podrzędnego. Jeśli spojrzysz na określony czas użytkownika w żądaniu GET, widać, że geofence podwitryny wygasła na ten czas. Sprzęt jest nadal w głównym geofence. W obszarze `expiredGeofenceGeometryId` w treści odpowiedzi możesz również sprawdzić identyfikator geometrii wirtualnego ogrodzenia terenu podrzędnego.


5. Lokalizacja 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 5](./media/tutorial-geofence/geofence-query5.png)

   Możesz zobaczyć, że sprzęt wyjechał poza wirtualne ogrodzenie głównego terenu budowy. Zdarzenie zostanie opublikowane, a wiadomość e-mail z alertem jest wysyłana do programu Operations Manager.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiedziałeś się: jak skonfigurować geofence, przekazując go w usłudze Azure Maps i danych przy użyciu interfejsu API przekazywania danych. Nauczyliśmy się również, jak za pomocą usługi Azure Maps Events Grid subskrybować i obsługiwać zdarzenia geofencingu. 

* Zobacz temat [Handle content types in Azure Logic Apps (Obsługa typów zawartości w usłudze Azure Logic Apps)](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), aby dowiedzieć się, jak za pomocą aplikacji logiki analizować dane JSON w celu tworzenia bardziej złożonej logiki.
* Aby dowiedzieć się więcej na temat programów obsługi zdarzeń w usłudze Event Grid, zobacz [Supported Events Handlers in Event Grid (Obsługiwane programy obsługi zdarzeń w usłudze Event Grid)](https://docs.microsoft.com/azure/event-grid/event-handlers).
