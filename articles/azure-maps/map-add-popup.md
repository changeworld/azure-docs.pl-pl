---
title: Dodawanie okna podręcznego do punktu na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać okno podręczne do punktu przy użyciu zestawu SDK sieci Web microsoft azure maps.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055948"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie wyskakujących okienka do mapy

W tym artykule pokazano, jak dodać okno podręczne do punktu na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

Poniższy kod dodaje obiekt punktowy, który ma `name` i `description` właściwości, do mapy za pomocą warstwy symbolu. Wystąpienie [popup klasy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) jest tworzony, ale nie jest wyświetlany. Zdarzenia myszy są dodawane do warstwy symboli, aby wyzwolić otwieranie i zamykanie okna podręcznego. Gdy symbol znacznika jest najechany, właściwość okna podręcznego jest aktualizowana o położenie `position` znacznika, a `content` opcja jest aktualizowana o kod HTML, który otacza `name` właściwości `description` obiektu punktu, który jest najechany kursorem. Wyskakujące okienko jest następnie `open` wyświetlane na mapie przy użyciu jego funkcji.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wyskakujących okienka przy użyciu usługi Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>pióro Dodaj wyskakujące okienko przy użyciu usługi Azure Maps</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Ponowne czesanie wyskakującego okienka z wieloma punktami

Istnieją przypadki, w których najlepszym rozwiązaniem jest utworzenie jednego okna podręcznego i ponowne użycie go. Na przykład może mieć dużą liczbę punktów i chcesz pokazać tylko jedno wyskakujące okienko naraz. Po ponownym uruchomieniu okna podręcznego liczba elementów DOM utworzonych przez aplikację jest znacznie zmniejszona, co może zapewnić lepszą wydajność. Poniższy przykład tworzy 3-punktowe operacje. Jeśli klikniesz na którykolwiek z nich, zostanie wyświetlone okno podręczne z zawartością tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='Ponowne wyskakowanie z wieloma pinami' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz okno <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>podręczne Pióro ponowne z wieloma pinami</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Dostosowywanie wyskakującego okienka

Domyślnie okno podręczne ma białe tło, strzałkę wskaźnika u dołu i przycisk zamknięcia w prawym górnym rogu. Poniższa próbka zmienia kolor tła na czarny, korzystając z `fillColor` opcji okna podręcznego. Przycisk zamknij zostanie usunięty `CloseButton` przez ustawienie opcji false. Zawartość HTML w wyskakującym okienku używa wyściełane 10 pikseli od krawędzi okna podręcznego. Tekst jest biały, więc dobrze pokazuje się na czarnym tle.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Niestandardowe okno podręczne" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>wyskakujące okienko</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>dostosowane do pióra przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Dodawanie szablonów wyskakujących do mapy

Szablony wyskakujących okienków ułatwiają tworzenie układów opartych na danych dla wyskakujących okienków. Poniższe sekcje pokazują użycie różnych szablonów wyskakujących do generowania sformatowanej zawartości przy użyciu właściwości funkcji.

> [!NOTE]
> Domyślnie cała zawartość renderowana używa szablonu wyskakujących okienka będzie piaskownica wewnątrz elementu iframe jako funkcja zabezpieczeń. Istnieją jednak ograniczenia:
>
> - Wszystkie skrypty, formularze, blokada wskaźnika i górna funkcja nawigacji są wyłączone. Linki mogą otwierać się na nowej karcie po kliknięciu. 
> - Starsze przeglądarki, które nie `srcdoc` obsługują parametru w elementów iframe, będą ograniczone do renderowania niewielkiej ilości zawartości.
> 
> Jeśli ufasz, że dane są ładowane do wyskakujących okienk i potencjalnie chcesz, aby te skrypty ładowane do wyskakujących okienka mogły uzyskać dostęp do aplikacji, możesz wyłączyć to, ustawiając opcję szablonów wyskakujących `sandboxContent` na false. 

### <a name="string-template"></a>Szablon ciągu

Szablon Ciąg zastępuje symbole zastępcze wartościami właściwości operacji. Właściwości operacji nie muszą być przypisane wartość typu String. Na przykład `value1` przechowuje całkowitą. Wartości te są następnie przekazywane do `popupTemplate`właściwości zawartości . 

Opcja `numberFormat` określa format liczby do wyświetlenia. Jeśli `numberFormat` nie jest określony, a następnie kod użyje formatu daty szablonów wyskakujących. Opcja `numberFormat` formatuje liczby za pomocą funkcji [Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Aby sformatować duże `numberFormat` liczby, należy rozważyć użycie opcji z funkcjami z [numberformat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Na przykład fragment kodu poniżej używa `maximumFractionDigits` do ograniczenia liczby cyfr ułamków do dwóch.

> [!Note]
> Jest tylko jeden sposób, w którym szablon String może renderować obrazy. Po pierwsze, szablon String musi mieć znacznik obrazu w nim. Wartość przekazywana do znacznika obrazu powinna być adresem URL obrazu. Następnie szablon Ciąg musi `isImage` mieć ustawioną `HyperLinkFormatOptions`wartość true w pliku . Opcja `isImage` określa, że hiperłącze jest dla obrazu, a hiperłącze zostanie załadowane do znacznika obrazu. Po kliknięciu hiperłącza obraz zostanie otwarty.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Szablon PropertyInfo

Szablon PropertyInfo wyświetla dostępne właściwości operacji. Opcja `label` określa tekst do wyświetlenia użytkownikowi. Jeśli `label` nie zostanie określony, zostanie wyświetlone hiperłącze. A jeśli hiperłącze jest obrazem, zostanie wyświetlona wartość przypisana do tagu "alt". Określa `dateFormat` format daty, a jeśli format daty nie jest określony, data będzie renderowana jako ciąg. Opcja `hyperlinkFormat` renderuje klikalne linki, `email` podobnie, opcja może służyć do renderowania klikalnych adresów e-mail.

Zanim szablon PropertyInfo wyświetli właściwości użytkownikowi końcowemu, cyklicznie sprawdza, czy właściwości są rzeczywiście zdefiniowane dla tej funkcji. Ignoruje również wyświetlanie właściwości stylu i tytułu. Na przykład nie będzie `color`wyświetlany `size` `anchor`, `strokeOpacity`, `visibility`, i . Tak więc po zakończeniu sprawdzania ścieżki właściwości w zapleczu szablon PropertyInfo pokazuje zawartość w formacie tabeli.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Wiele szablonów zawartości

Funkcja może również wyświetlać zawartość przy użyciu kombinacji szablonu String i szablonu PropertyInfo. W takim przypadku szablon Ciąg renderuje wartości symboli zastępczych na białym tle.  I Szablon PropertyInfo renderuje obraz o pełnej szerokości wewnątrz tabeli. Właściwości w tym przykładzie są podobne do właściwości wyjaśnione w poprzednich przykładach.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punkty bez zdefiniowanego szablonu

Gdy szablon wyskakujących okienka nie jest zdefiniowany jako szablon String, szablon PropertyInfo lub kombinacja obu tych ów, używa ustawień domyślnych. Gdy `title` i `description` są tylko przypisane właściwości, szablon wyskakujących wyświetla białe tło, przycisk zamknięcia w prawym górnym rogu. A na małych i średnich ekranach pokazuje strzałkę u dołu. Ustawienia domyślne są wyświetlane wewnątrz tabeli `title` dla `description`wszystkich właściwości innych niż i . Nawet po dołączeniu do ustawień domyślnych szablon wyskakujących okienka nadal można manipulować programowo. Na przykład użytkownicy mogą wyłączyć wykrywanie hiperłączy, a ustawienia domyślne nadal będą miały zastosowanie do innych właściwości.

Kliknij punkty na mapie w CodePen. Na mapie znajduje się punkt dla każdego z następujących szablonów wyskakujących okienka: szablon ciągu, szablon PropertyInfo i szablon wiele zawartości. Istnieją również trzy punkty, aby pokazać, jak szablony renderowania przy użyciu ustawień domyślnych.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>popuptemplates pióra</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Ponowne używanie szablonu podręcznego

Podobnie jak w wyskakującym okienku, można ponownie użyć szablonów wyskakujących. Takie podejście jest przydatne, gdy chcesz pokazać tylko jeden szablon wyskakujących w czasie, dla wielu punktów. Po ponownym uruchomieniu szablonu wyskakującego zmniejsza się liczba elementów DOM utworzonych przez aplikację, co zwiększa wydajność aplikacji. W poniższym przykładzie użyto tego samego szablonu wyskakujących dla trzech punktów. Jeśli klikniesz na którykolwiek z nich, zostanie wyświetlone okno podręczne z zawartością tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='Ponowne użyciePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz ponowne użycie pióraPopupTemplate przez usługi Azure Maps ( <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>codepen</a>.
</iframe>

## <a name="popup-events"></a>Zdarzenia wyskakujące

Wyskakujące okienka można otwierać, zamykać i przeciągać. Klasa popup zawiera zdarzenia, aby pomóc deweloperom reagować na te zdarzenia. W poniższym przykładzie wyróżniono, które zdarzenia są uruchamiane po otwarciu, zamknięciu lub przeciągnięciu okna podręcznego. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia wyskakujące" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>zdarzenia wyskakujące</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [Wyskakujące okienka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Zobacz następujące wspaniałe artykuły dla pełnych przykładów kodu:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie znacznika HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)
