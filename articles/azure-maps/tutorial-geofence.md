---
title: 'Samouczek: Tworzenie geoogrodzenia i śledzenie urządzeń na mapie | Mapy Microsoft Azure'
description: W tym samouczku dowiesz się, jak skonfigurować geoogrodzenie i śledzić urządzenia względem geoogrodzenia przy użyciu usługi Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0e408adfe1daed402ef690224368e846bd0a97c8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910942"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Samouczek: Konfigurowanie geoogrodzenia przy użyciu Azure Maps

Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Scenariusz, który omawiamy w tym samouczku, ma pomóc kierownikom budowy w monitorowaniu potencjalnie niebezpiecznych wyjazdów sprzętu poza wyznaczone tereny budowy. Na terenie budowy znajduje się kosztowny sprzęt i obowiązują pewne przepisy. Zazwyczaj wymagają one, aby urządzenia znajdowały się na terenie budowy i nie opuszczały go bez zezwolenia.

Za pomocą interfejsu API przekazywania danych usługi Azure Maps zapiszemy geofencing, natomiast interfejs API geofencingu usługi Azure Maps pomoże nam sprawdzić położenie sprzętu względem geofencingu. Użyjemy usługi Azure Event Grid, aby przesłać strumieniowo wyniki geofencingu i na ich podstawie skonfigurować powiadomienie.
Aby dowiedzieć się więcej na temat usługi Event Grid, zobacz [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przekazywanie obszaru geofencingu w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych.
> *   Konfigurowanie usługi Event Grid na potrzeby obsługi zdarzeń geofencingu.
> *   Konfigurowanie obsługi zdarzeń geofencingu.
> *   Konfigurowanie alertów w odpowiedzi na zdarzenia geofencingu przy użyciu aplikacji logiki.
> *   Śledzenie przy użyciu interfejsów API usługi geofencingu usługi Azure Maps, czy zasób budowlany znajduje się na terenie budowy.


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Aby wykonać kroki opisane w tym samouczku, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) w celu utworzenia subskrypcji konta Azure Maps przy użyciu warstwy cenowej S1 i postępuj zgodnie z instrukcjami w sekcji [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby pobrać klucz podstawowy dla konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Przekazywanie geofencingu

Aby przekazać geofencing terenu budowy przy użyciu interfejsu API przekazywania danych, skorzystamy z aplikacji Postman. Dla celów tego samouczka przyjęliśmy założenie, że istnieje ogólny teren budowy będący niezmiennym parametrem, którego sprzęt budowlany nie powinien naruszać. Naruszenia tego ogrodzenia stanowią poważne zagrożenie i są zgłaszane do programu Operations Manager. Za pomocą zoptymalizowanego zestawu dodatkowych ogrodzeń można zgodnie z harmonogramem śledzić różne obszary w obrębie ogólnego terenu budowy. Przyjęliśmy założenie, że główne wirtualne ogrodzenie zawiera teren podrzędny subsite1, który ma ustawiony czas wygaśnięcia i wygaśnie po upływie tego czasu. W zależności od potrzeb możesz utworzyć więcej zagnieżdżonych wirtualnych ogrodzeń. Na przykład w obszarze subsite1 praca może odbywać się w tygodniach 1–4 harmonogramu, a w obszarze subsite2 praca może odbywać się w tygodniach 5–7. Wszystkie tego rodzaju ogrodzenia można załadować jako pojedynczy zestaw danych na początku projektu, aby używać ich do śledzenia reguł opartych na czasie i miejscu. Aby uzyskać więcej informacji na temat formatu danych geofencingu, zobacz [Geofence GeoJSON data (Geofencing danych GeoJSON)](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Aby uzyskać więcej informacji na temat przekazywania danych do usługi Azure Maps, zobacz [Dokumentacja interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

Otwórz aplikację Postman i wykonaj poniższe kroki, aby przekazać geofencing terenu budowy przy użyciu interfejsu API przekazywania danych usługi Azure Maps.

1. Otwórz aplikację Postman i kliknij pozycję New | Create new (Nowe | Utwórz nowe), a następnie wybierz pozycję Request (Żądanie). Wprowadź nazwę żądania na potrzeby przekazywania danych geofencing, wybierz docelową kolekcję lub folder zapisywania, a następnie kliknij pozycję Save (Zapisz).

    ![Przekazywanie geofencingu przy użyciu narzędzia Postman](./media/tutorial-geofence/postman-new.png)

2. Na karcie konstruktora wybierz metodę POST HTTP i wprowadź poniższy adres URL, aby utworzyć żądanie POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Parametr GEOJSON w ścieżce adresu URL reprezentuje format przekazywanych danych.

3. Kliknij pozycję **Params** (Parametry), a następnie wprowadź poniższą parę klucz-wartość, która będzie używana w przypadku adresu URL żądania POST. Zastąp wartość klucza subskrypcji kluczem Azure Maps.
   
    ![Parametry przekazywania danych (geoogrodzenia) w programie Poster](./media/tutorial-geofence/postman-key-vals.png)

4. Kliknij pozycję **Body** (Treść), a następnie wybierz nieprzetworzony format danych wejściowych i z listy rozwijanej wybierz format danych wejściowych JSON. Jako dane do przekazania podaj następujące dane JSON:

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

5. Kliknij przycisk wysyłania i przejrzyj nagłówek odpowiedzi. Po pomyślnym żądaniu nagłówek **lokalizacji** będzie zawierać identyfikator URI stanu, aby sprawdzić bieżący stan żądania przekazywania. Identyfikator URI stanu będzie mieć następujący format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Skopiuj identyfikator URI stanu i Dołącz do niego parametr `subscription-key`, podając jego wartość klucza subskrypcji konta usługi Azure Maps. Format identyfikatora URI stanu powinien wyglądać podobnie do przedstawionego poniżej:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Aby uzyskać `udId` Otwórz nową kartę w aplikacji Poster i wybierz pozycję Pobierz metodę HTTP na karcie Konstruktor i wprowadź żądanie GET w identyfikatorze URI stanu. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz udId w treści odpowiedzi. Skopiuj udId do późniejszego użycia.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Konfigurowanie programu obsługi zdarzeń

W celu powiadamiania programu Operations Manager o zdarzeniach wjazdu i wyjazdu powinniśmy utworzyć program obsługi zdarzeń odbierający powiadomienia.

Utworzymy dwie usługi [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) do obsługi zdarzeń wjazdu i wyjazdu. Zostaną także utworzone wyzwalacze zdarzeń w usłudze Logic Apps, które będą wyzwalane przez te zdarzenia. Chodzi o to, aby wysyłać alerty, w tym przypadku wiadomości e-mail do programu Operations Manager, za każdym razem, gdy sprzęt wjeżdża na teren budowy lub wyjeżdża z terenu budowy. Poniższa ilustracja przedstawia tworzenie aplikacji logiki na potrzeby zdarzenia wjazdu na obszar wirtualnego ogrodzenia. W podobny sposób można utworzyć zdarzenie wyjazdu.
Aby uzyskać więcej informacji, możesz wyświetlić wszystkie [obsługiwane programy obsługi zdarzeń](https://docs.microsoft.com/azure/event-grid/event-handlers).

1. Tworzenie aplikacji logiki w witrynie Azure Portal

   ![Tworzenie Azure Logic Apps do obsługi zdarzeń geoogrodzenia](./media/tutorial-geofence/logic-app.png)

2. Wybierz wyzwalacz żądania HTTP, a następnie wybierz pozycję „Wyślij wiadomość e-mail” jako akcję w łączniku programu Outlook
  
   ![Schemat usługi Logic Apps](./media/tutorial-geofence/logic-app-schema.png)

3. Zapisz aplikację logiki, aby wygenerować punkt końcowy adresu URL HTTP i skopiuj adres URL HTTP.

   ![Generowanie punktu końcowego Logic Apps](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Tworzenie subskrypcji zdarzeń usługi Azure Maps

Usługa Azure Maps obsługuje trzy typy zdarzeń. Obsługiwanym typom zdarzeń usługi Azure Maps można przyjrzeć się [tutaj](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Utworzymy dwie różne subskrypcje — jedną dla zdarzeń wjazdu i jedną dla zdarzeń wyjazdu.

Wykonaj poniższe kroki, aby utworzyć subskrypcję dla zdarzeń wjazdu wirtualnego ogrodzenia. Zdarzenia wyjazdu wirtualnego ogrodzenia możesz subskrybować w podobny sposób.

1. Przejdź do swojego konta usługi Azure Maps za pomocą [tego linku do portalu](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) i wybierz kartę zdarzeń.

   ![Przejdź do Azure Maps zdarzeń konta](./media/tutorial-geofence/events-tab.png)

2. Aby utworzyć subskrypcję zdarzeń, wybierz pozycję Subskrypcja zdarzeń ze strony zdarzeń.

   ![Tworzenie subskrypcji zdarzeń usługi Azure Maps](./media/tutorial-geofence/create-event-subscription.png)

3. Nadaj nazwę subskrypcji zdarzeń i rozpocznij subskrypcję typu zdarzenia Wjazd. Teraz jako „Typ punktu końcowego” wybierz element webhook i skopiuj punkt końcowy adresu URL HTTP aplikacji logiki do pola „Punkt końcowy”

   ![Szczegóły subskrypcji zdarzeń Azure Maps](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Używanie interfejsu API wirtualnego ogrodzenia

Interfejs API geofencingu umożliwia sprawdzanie, czy **urządzenie** (sprzęt jest częścią stanu) znajduje się wewnątrz, czy na zewnątrz geofencingu. Aby lepiej zrozumieć interfejs API GET geofencingu. Wysyłamy zapytania do różnych lokalizacji, w których określony sprzęt poruszał się na przestrzeni czasu. Poniższa ilustracja przedstawia pięć lokalizacji określonego sprzętu budowlanego o unikatowym **identyfikatorze urządzenia** obserwowanych w porządku chronologicznym. Każda z tych pięciu lokalizacji służy do oceny stanu wjazdów i wyjazdów na terenie geofencingu względem ogrodzenia. W przypadku zmiany stanu usługa geofencingu wyzwala zdarzenie, które jest wysyłane do aplikacji logiki przez usługę Event Grid. W wyniku tego program Operations Manager otrzymuje odpowiednie powiadomienie pocztą e-mail o wjeździe lub wyjeździe.

> [!Note]
> Powyższy scenariusz i zachowanie są oparte na tym samym **identyfikatorze urządzenia**, dzięki czemu odzwierciedlają one pięć różnych lokalizacji, jak na poniższej ilustracji.

![Mapa geoogrodzenia w Azure Maps](./media/tutorial-geofence/geofence.png)

W aplikacji Postman otwórz nową kartę w tej samej kolekcji, która została utworzona powyżej. Na karcie konstruktora wybierz metodę GET HTTP:

Poniżej przedstawiono pięć żądań interfejsu API wirtualnego ogrodzenia GET HTTP z różnymi odpowiadającymi im współrzędnymi położenia sprzętu zaobserwowanymi w porządku chronologicznym. Po każdym żądaniu następuje treść odpowiedzi.
 
1. Lokalizacja 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Zapytanie geofencingu 1](./media/tutorial-geofence/geofence-query1.png)

   Jeśli przyjrzysz się powyższej odpowiedzi, ujemna odległość od głównego wirtualnego ogrodzenia oznacza, że sprzęt znajduje się w obrębie wirtualnego ogrodzenia, a dodatnia odległość od wirtualnego ogrodzenia terenu podrzędnego oznacza, że sprzęt znajduje się poza wirtualnym ogrodzeniem terenu podrzędnego. 

2. Lokalizacja 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Zapytanie geofencingu 2](./media/tutorial-geofence/geofence-query2.png)

   Jeśli dokładnie przyjrzysz się poprzedniej odpowiedzi w formacie JSON, sprzęt znajduje się poza terenem podrzędnym, ale w obrębie głównego ogrodzenia. Nie powoduje to wyzwolenia zdarzenia ani wysłania wiadomości e-mail.

3. Lokalizacja 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 3](./media/tutorial-geofence/geofence-query3.png)

   Nastąpiła zmiana stanu i sprzęt znajduje się teraz zarówno w obrębie głównego wirtualnego ogrodzenia, jak i wirtualnego ogrodzenia terenu podrzędnego. Powoduje to opublikowanie zdarzenia i wysłanie powiadomienia e-mail do programu Operations Manager.

4. Lokalizacja 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Zapytanie geofencingu 4](./media/tutorial-geofence/geofence-query4.png)

   Obserwując dokładnie odpowiednią odpowiedź, możesz zauważyć, że w tym miejscu nie jest publikowane żadne zdarzenie, chociaż sprzęt wyjechał poza wirtualne ogrodzenie terenu podrzędnego. Jeśli przyjrzysz się czasowi określonemu przez użytkownika w żądaniu GET, możesz zobaczyć, że wirtualne ogrodzenie terenu podrzędnego wygasło względem tego czasu i sprzęt nadal znajduje się w obrębie głównego wirtualnego ogrodzenia. W obszarze `expiredGeofenceGeometryId` w treści odpowiedzi możesz również sprawdzić identyfikator geometrii wirtualnego ogrodzenia terenu podrzędnego.


5. Lokalizacja 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Zapytanie geofencingu 5](./media/tutorial-geofence/geofence-query5.png)

   Możesz zobaczyć, że sprzęt wyjechał poza wirtualne ogrodzenie głównego terenu budowy. Powoduje to opublikowanie zdarzenia (jest to poważne naruszenie), a do programu Operations Manager jest wysyłana wiadomość e-mail z alertem krytycznym.

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczyliśmy się, jak skonfigurować geofencing przez przekazanie w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych. Nauczyliśmy się również, jak za pomocą usługi Azure Maps Events Grid subskrybować i obsługiwać zdarzenia geofencingu. 

* Zobacz temat [Handle content types in Azure Logic Apps (Obsługa typów zawartości w usłudze Azure Logic Apps)](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), aby dowiedzieć się, jak za pomocą aplikacji logiki analizować dane JSON w celu tworzenia bardziej złożonej logiki.
* Aby dowiedzieć się więcej na temat programów obsługi zdarzeń w usłudze Event Grid, zobacz [Supported Events Handlers in Event Grid (Obsługiwane programy obsługi zdarzeń w usłudze Event Grid)](https://docs.microsoft.com/azure/event-grid/event-handlers).
