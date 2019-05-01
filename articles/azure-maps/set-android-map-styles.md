---
title: Mapowanie funkcji usługi Azure Maps stylu | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Maps stylu związane z nim funkcje zestawu SDK systemu Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870975"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ustaw styl mapy za pomocą usługi Azure Maps Android SDK

W tym artykule przedstawiono dwa sposoby ustawiania stylów mapy za pomocą usługi Azure Maps SDK dla systemu Android. Usługi Azure Maps zawiera sześć style różnych mapowań do wyboru. Aby uzyskać więcej informacji na temat obsługiwanych mapy style zobacz [obsługiwane style mapy w usługi Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć proces, w tym artykule, musisz zainstalować [zestawu SDK usługi Azure Maps Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) można załadować mapy.


## <a name="set-map-style-in-the-layout"></a>Ustaw styl mapy w układzie

Styl mapę można ustawić w pliku układu dla swojej klasy działania. Edytuj **res > Układ > activity_main.xml**, więc wygląda na to podobne do pokazanego poniżej:

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style` Atrybut powyżej ustawia styl mapy **grayscale_dark**. 

<center>

![Styl grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ustaw styl mapy w klasie działania

Style mapy można ustawić w klasie działania. Skopiuj poniższy fragment kodu do **onCreate()** metody usługi `MainActivity.java` klasy. Spowoduje to ustawienie style mapy na **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>