---
title: Dodawanie kształtu przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać kształt do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7598bbc879351752580247e46bc986ee84fa0d56
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497248"
---
# <a name="add-a-shape-to-a-map"></a>Dodawanie kształtu do mapy

W tym artykule przedstawiono sposób dodawania [kształt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) mapy i zaktualizuj właściwości istniejącego kształtu na mapie.

<a id="addALine"></a>

## <a name="add-a-line"></a>Dodaj wiersz

<iframe height='500' scrolling='no' title='Dodaj wiersz do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qomaKv/'>dodać wiersz do mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie konstrukcji obiektu mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) obiekt zostanie utworzony i dodany do źródła danych.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje wiersz obiektów w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Ostatni blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii w [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwie wiersza są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy wiersz jest wyświetlany po mapy ładuje pełni.

## <a name="customize-a-line-layer"></a>Dostosowywanie warstwę linii

Wiersz w warstwie kilka opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>wiersz opcje warstwy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Dodaj koło

<iframe height='500' scrolling='no' title='Dodaj koło z mapą' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj koło z mapą</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie konstrukcji obiektu mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Okrąg jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) i ma `subType` ustawioną na "koło" i `radius` liczniki wartości właściwości. Po dodaniu funkcji punktu z podtypem okrąg ze źródłem danych konwertuje go na okręgu wielokąta w mapie.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwę wielokątów są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy koła jest wyświetlany po mapy ładuje pełni.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Dodaj wielokąta

Istnieją dwa różne sposoby wielokąta można dodać do mapy. Oba zostały wyjaśnione w poniższych przykładach.

### <a name="use-polygon-layer"></a>Użyj warstwę wielokątów 

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodawanie wielokąta do mapy </a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) zostanie utworzony przy użyciu tablicy współrzędnych i dodany do źródła danych. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwę wielokątów są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że wielokąta jest wyświetlany po mapy ładuje pełni.

### <a name="use-polygon-and-line-layer"></a>Użyj warstwy wielokątów i linii

<iframe height='500' scrolling='no' title='Warstwa wielokątów i linii do dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>warstwy wielokątów i linii, aby dodać wielokąta</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) zostanie utworzony przy użyciu tablicy współrzędnych i dodany do źródła danych. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) jest tablicą wierszy. Zobacz właściwości warstwy linii w [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Trzeci bloku kodu tworzy warstw wielokątów i linii.

Ostatni blok kodu dodaje warstw wielokątów i linii do mapy. Źródło danych i warstwy są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że wielokąta jest wyświetlany po mapy ładuje pełni.

## <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwę wielokątów

Warstwa wielokątów ma tylko kilka opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="update-a-shape"></a>Aktualizowanie kształtów

Klasa kształtu zawijania [geometrii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) lub [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i można łatwo aktualizować i obsługiwać je.
`new Shape(data: Feature<data.Geometry, any>)` Tworzy obiekt kształt i inicjuje ją przy użyciu określonej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>aktualizacji właściwości kształtu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Punkt znajduje się [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klasy. Drugi blok kodu inicjuje promienia dla elementu HTML suwaka i następnie tworzy i otacza obiekt point w [kształt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) obiektu klasy.

Trzeci blok kodu tworzy funkcję, która przyjmuje wartość z elementu HTML suwaka zakresu i zmienia wartość usługi radius przy użyciu klasy kształt [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metody.

W czwartym bloku kodu, obiekt źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Punkt jest dodawane do źródła danych.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Trzeci bloku kodu tworzy warstwę wielokątów. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródła danych, kliknij pozycję odbiornik zdarzeń i warstwę wielokątów są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że punkt jest wyświetlany po mapy ładuje pełni.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Znaczniki HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy cieplnej](./map-add-heat-map-layer.md)
