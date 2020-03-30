---
title: Pokaż dane o ruchu drogowym na mapie Androida | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlić dane o ruchu na mapie przy użyciu microsoft azure maps android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335372"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Pokazywale danych o ruchu drogowym na mapie przy użyciu usługi Azure Maps Android SDK

Dane przepływu i zdarzenia są dwa typy danych o ruchu, które mogą być wyświetlane na mapie. W tym przewodniku pokazano, jak wyświetlić oba typy danych o ruchu drogowym. Dane dotyczące incydentów składają się z danych punktowych i liniowych dotyczących takich rzeczy, jak konstrukcje, zamknięcia dróg i wypadki. Dane przepływu pokazują dane dotyczące przepływu ruchu na drodze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było pokazać ruch na mapie, należy [wykonać konto platformy Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)i uzyskać klucz [subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account). Następnie należy zainstalować [zestaw SDK usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) w systemie Android i załadować mapę.

## <a name="incidents-traffic-data"></a>Dane o ruchu drogowym incydentów 

Aby wywołać, `setTraffic` należy zaimportować `incidents`następujące biblioteki, a także:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Poniższy fragment kodu pokazuje, jak wyświetlić dane o ruchu drogowym na mapie. Przekazujemy wartość logiczną `incidents` do metody i przekazujemy go do `setTraffic` metody. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dane o ruchu przepływów

Najpierw musisz zaimportować następujące biblioteki, aby zadzwonić, `setTraffic` i: `flow`

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Użyj następującego fragmentu kodu, aby ustawić dane przepływu ruchu. Podobnie jak kod w poprzedniej sekcji, przekazujemy `flow` zwracaną `setTraffic` wartość metody do metody. Istnieją cztery wartości, które `flow`mogą być przekazywane `flow` do , a każda wartość wyzwoli, aby zwrócić odpowiednią wartość. Zwracana wartość `flow` zostanie następnie przekazana jako `setTraffic`argument do . Zobacz poniższą tabelę dla tych czterech wartości:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Nie wyświetla danych o ruchu drogowym na mapie |
| TrafficFlow.WZGLĘDNY | Pokazuje dane o ruchu drogowym, które są związane z prędkością swobodnego przepływu na drodze |
| TrafficFlow.RELATIVE_DELAY | Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie |
| TrafficFlow.ABSOLUTE (Niem. | Pokazuje absolutną prędkość wszystkich pojazdów na drodze |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Wyświetlanie danych o zdarzeniu, klikając funkcję

Aby uzyskać zdarzenia dla określonej funkcji, można użyć poniższego kodu. Po kliknięciu funkcji logika kodu sprawdza zdarzenia i tworzy komunikat o zdarzeniu. U dołu ekranu pojawi się komunikat ze szczegółami.

1. Najpierw musisz edytować **układ res > > activity_main.xml**, aby wyglądał jak poniższy. Można zastąpić `mapcontrol_centerLat`, `mapcontrol_centerLng`i `mapcontrol_zoom` żądane wartości. Przypomnijmy, poziom powiększenia jest wartością od 0 do 22. Przy poziomie powiększenia 0 cały świat mieści się na jednej kafelku.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Dodaj następujący kod do pliku **MainActivity.java.** Pakiet jest domyślnie dołączony, więc upewnij się, że pakiet jest utrzymywany u góry.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Po włączeniu powyższego kodu do aplikacji, będziesz mógł kliknąć na funkcję i zobaczyć szczegóły zdarzeń drogowych. W zależności od szerokości, długości geograficznej i wartości poziomu powiększenia użytych w pliku **activity_main.xml** zostaną wyświetlone wyniki podobne do następujących obrazów:

   <center>

   ![Incydent-ruch na mapie](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi przewodnikami, aby dowiedzieć się, jak dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)
