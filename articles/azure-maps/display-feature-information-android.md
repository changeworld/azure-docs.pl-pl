---
title: Wyświetlanie informacji o funkcjach w sdk usługi Azure Maps dla systemu Android | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlić informacje o obiektach na mapie przy użyciu sdk Microsoft Azure Maps Android.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911692"
---
# <a name="display-feature-information"></a>Wyświetlanie informacji o funkcjach

Dane przestrzenne są często reprezentowane przy użyciu punktów, linii i wielokątów. Te dane często mają informacje o metadanych skojarzone z nim. Na przykład punkt może reprezentować lokalizację sklepu i metadane dotyczące tej restauracji może być jego nazwa, adres i rodzaj żywności, który służy. Te metadane można dodać jako właściwości `JsonObject`tych funkcji za pomocą pliku . Poniższy kod tworzy prostą `title` funkcję punktu z właściwością o wartości "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Gdy użytkownik wchodzi w interakcję z obiektem na mapie, zdarzenia mogą być używane do reagowania na te akcje. Typowym scenariuszem jest wyświetlenie komunikatu o właściwościach metadanych funkcji, z którą użytkownik wchodził w interakcję. Wydarzenie `OnFeatureClick` jest głównym wydarzeniem używanym do wykrywania, kiedy użytkownik stuknął obiekt na mapie. Jest też `OnLongFeatureClick` wydarzenie. Podczas dodawania `OnFeatureClick` zdarzenia do mapy, może być ograniczona do jednej warstwy, przekazując w identyfikatorze warstwy, aby ograniczyć go do. Jeśli identyfikator warstwy nie zostanie przekazany, dotknięcie dowolnego obiektu na mapie, niezależnie od warstwy, w której się znajduje, spowoduje rozpalinie tego zdarzenia. Poniższy kod tworzy warstwę symboli do renderowania `OnFeatureClick` danych punktowych na mapie, a następnie dodaje zdarzenie i ogranicza je do tej warstwy symboli.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Wyświetlanie wyskakującego komunikatu

Wyskakujące wiadomości jest jednym z najprostszych sposobów wyświetlania informacji dla użytkownika i jest dostępny we wszystkich wersjach systemu Android. Nie obsługuje żadnych typów danych wejściowych użytkownika i jest wyświetlany tylko przez krótki okres czasu. Jeśli chcesz szybko poinformować użytkownika o tym, co stuknął, dobrym rozwiązaniem może być wyskakujące wiadomości. Poniższy kod pokazuje, jak wyskakujący komunikat może być używany ze zdarzeniem. `OnFeatureClick`

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animacja stukniętej funkcji i wyświetlona wiadomość wyskakujących](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Oprócz wyskakujących wiadomości istnieje wiele innych sposobów prezentowania właściwości metadanych funkcji, takich jak:

- [Widżet Snakbar](https://developer.android.com/training/snackbar/showing.html) - Snackbars zapewniają lekką informację zwrotną o operacji. Pokazują one krótką wiadomość w dolnej części ekranu na telefon komórkowy i lewym dolnym po lewej stronie na większych urządzeniach. Snackbars pojawiają się ponad wszystkie inne elementy na ekranie i tylko jeden może być wyświetlany w czasie.
- [Okna dialogowe](https://developer.android.com/guide/topics/ui/dialogs) — okno dialogowe to małe okno, które monituje użytkownika o podjęcie decyzji lub wprowadzenie dodatkowych informacji. Okno dialogowe nie wypełnia ekranu i jest zwykle używane dla zdarzeń modalnych, które wymagają od użytkowników podjęcia akcji, zanim będą mogli kontynuować.
- Dodaj [Fragment](https://developer.android.com/guide/components/fragments) do bieżącego działania.
- Przejdź do innego działania lub widoku.

## <a name="next-steps"></a>Następne kroki

Aby dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](how-to-add-shapes-to-android-map.md)
