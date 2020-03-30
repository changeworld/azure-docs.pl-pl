---
title: Ustawianie stylu mapy przy użyciu zestawu Azure Maps android SDK| Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o funkcjach związanych ze stylem usługi Microsoft Azure Maps dla zestawu SDK systemu Android.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334357"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Ustawianie stylu mapy przy użyciu zestawu SDK usługi Azure Maps dla systemu Android

W tym artykule przedstawiono dwa sposoby ustawiania stylów map przy użyciu zestawu SDK usługi Azure Maps dla systemu Android. Usługa Azure Maps ma do wyboru sześć różnych stylów map. Aby uzyskać więcej informacji na temat obsługiwanych stylów map, zobacz [obsługiwane style map w usłudze Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby zakończyć proces w tym artykule, należy zainstalować [zestaw SDK systemu Azure Maps dla systemu Android,](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) aby załadować mapę.


## <a name="set-map-style-in-the-layout"></a>Ustawianie stylu mapy w układzie

Styl mapy można ustawić w pliku układu dla klasy aktywności. Edytuj **układ res > > activity_main.xml**, więc wygląda jak ten poniżej:

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

Powyższy `mapcontrol_style` atrybut ustawia styl mapy na **grayscale_dark**. 

<center>

![styl grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Ustawianie stylu mapy w klasie aktywności

Styl mapy można ustawić w klasie aktywności. Skopiuj poniższy fragment kodu do metody **onCreate()** danej `MainActivity.java` klasy. Ten kod ustawi styl mapy na **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satelita-road-etykiety](./media/set-android-map-styles/satellite-road-labels.png)</center>