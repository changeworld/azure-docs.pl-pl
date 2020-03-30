---
title: Szablony obrazów w sdk azure maps web | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak używać szablonów obrazów ze znacznikami HTML i różnymi warstwami w sdku Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198228"
---
# <a name="how-to-use-image-templates"></a>Jak używać szablonów obrazów

Obrazy mogą być używane ze znacznikami HTML i różnymi warstwami w internetowym SDK usługi Azure Maps:

 - Warstwy symboli mogą renderować punkty na mapie za pomocą ikony obrazu. Symbole mogą być również renderowane wzdłuż ścieżki linii.
 - Warstwy wielokątów mogą być renderowane za pomocą obrazu wzoru wypełnienia. 
 - Znaczniki HTML mogą renderować punkty przy użyciu obrazów i innych elementów HTML.

Aby zapewnić dobrą wydajność warstw, przed renderowaniem należy załadować obrazy do zasobu sprite'a obrazu mapy. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), symbollayer, wstępnie ładuje kilka obrazów znaczników w kilku kolorach do ikonki obrazu mapy, domyślnie. Te obrazy znaczników i więcej są dostępne jako szablony SVG. Mogą być używane do tworzenia obrazów z niestandardowymi skalami lub używane jako kolor podstawowy i pomocniczy klienta. W sumie dostępne są 42 szablony obrazów: 27 ikon symboli i 15 wzorów wypełnienia wielokątami.

Szablony obrazów można dodawać do zasobów sprite `map.imageSprite.createFromTemplate` obrazów mapy za pomocą funkcji. Ta funkcja umożliwia przekazywanie maksymalnie pięciu parametrów;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Jest `id` to unikatowy identyfikator, który tworzysz. Obraz `id` jest przypisywany do obrazu po dodaniu go do ikonki obrazu map. Ten identyfikator w warstwach służy do określania zasobu obrazu do renderowania. Określa, `templateName` który szablon obrazu ma być używany. Opcja `color` ustawia podstawowy kolor obrazu, `secondaryColor` a opcje ustawia kolor pomocniczy obrazu. Opcja `scale` skaluje szablon obrazu przed zastosowaniem go do ikonki obrazu. Gdy obraz zostanie zastosowany do ikonki obrazu, jest konwertowany na png. Aby zapewnić wyraźne renderowanie, lepiej skalować szablon obrazu przed dodaniem go do ikonki, niż skalować go w warstwie.

Ta funkcja asynchronicznie ładuje obraz do ikonki obrazu. W związku z tym zwraca Promise, że można poczekać na tę funkcję, aby zakończyć.

Poniższy kod pokazuje, jak utworzyć obraz z jednego z wbudowanych szablonów i użyć go z warstwą symboli.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Używanie szablonu obrazu z warstwą symboli

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako symbol w warstwie `image` symboli, `iconOptions`odwołując się do identyfikatora zasobu obrazu w opcji programu .

Poniższa próbka renderuje warstwę symboli przy użyciu szablonu `marker-flat` obrazu z turkusowym kolorem podstawowym i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa symboli z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>warstwę symbol pióra z wbudowanym szablonem ikony</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Używanie szablonu obrazu wzdłuż ścieżki linii

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować wzdłuż ścieżki linii, dodając linestring do `lineSpacing`źródła danych i używając warstwy symbolu `image` z opcją `iconOptions`i odwołując się do identyfikatora zasobu obrazu w opcji th . 

Poniższa próbka renderuje różową linię na mapie i `car` używa warstwy symbolu przy użyciu szablonu obrazu z niebieskim kolorem podstawowym cwaniaka i białym kolorem pomocniczym. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa liniowa z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz warstwę Linii pióra <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>z wbudowanym szablonem ikony</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Jeśli szablon obrazu wskazuje, `rotation` ustaw opcję ikony warstwy symbolu na 90, jeśli chcesz, aby była ona skierowana w tym samym kierunku co linia.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Używanie szablonu obrazu z warstwą wielokąta

Po załadowaniu szablonu obrazu do ikonki obrazu mapy można go renderować jako wzór wypełnienia w warstwie wielokąta, odwołując się do identyfikatora zasobu obrazu w `fillPattern` opcji warstwy.

Poniższa próbka renderuje warstwę `dot` wielokąta przy użyciu szablonu obrazu z czerwonym kolorem podstawowym i przezroczystym kolorem pomocniczym.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wypełnianie wielokąta wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>wielokąt wypełniania pióra z wbudowanym szablonem ikony</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ustawienie koloru pomocniczego deseń wypełnienia ułatwia wyświetlanie mapy podstawowej nadal będzie dostarczać wzorzec podstawowy. 

## <a name="use-an-image-template-with-an-html-marker"></a>Używanie szablonu obrazu ze znacznikiem HTML

Szablon obrazu można pobrać za `altas.getImageTemplate` pomocą funkcji i użyć jako zawartość znacznika HTML. Szablon może zostać przekazany `htmlContent` do opcji znacznika, a `color`następnie `secondaryColor`dostosowany za pomocą opcji , i `text` opcji.

W poniższym przykładzie użyto szablonu `marker-arrow` z czerwonym kolorem podstawowym, różowym kolorem pomocniczym i wartością tekstową "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Znacznik HTML z wbudowanym szablonem ikony" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz znacznik HTML pióra <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>z wbudowanym szablonem ikony</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Tworzenie niestandardowych szablonów wielokrotnego pożyciem

Jeśli aplikacja używa tej samej ikony z różnymi ikonami lub jeśli tworzysz moduł, który dodaje dodatkowe szablony obrazów, można łatwo dodać i pobrać te ikony z zestawem SDK sieci Web usługi Azure Maps. Użyj następujących funkcji statycznych `atlas` w obszarze nazw.

| Nazwa | Typ zwracany | Opis | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Dodaje niestandardowy szablon obrazu SVG do obszaru nazw atlasu. |
|  `getImageTemplate(templateName: string, scale?: number)`| ciąg | Pobiera szablon SVG według nazwy. |
| `getAllImageTemplateNames()` | ciąg[] |  Pobiera szablon SVG według nazwy. |

Szablony obrazów SVG obsługują następujące wartości zastępcze:

| Symbol zastępczy | Opis |
|-|-|
| `{color}` | Kolor podstawowy. | 
| `{secondaryColor}` | Kolor pomocniczy. | 
| `{scale}` | Obraz SVG jest konwertowany na obraz png po dodaniu do ikonki obrazu mapy. Ten symbol zastępczy może służyć do skalowania szablonu przed jego konwersją, aby zapewnić wyraźne renderowanie. | 
| `{text}` | Lokalizacja renderowania tekstu, gdy jest używana ze znacznikiem HTML. |

W poniższym przykładzie pokazano, jak wziąć szablon SVG i dodać go do sdk sieci Web usługi Azure Maps jako szablon ikony wielokrotnegoużytnia. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie niestandardowego szablonu ikony do obszaru nazw atlasu" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz szablon <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>ikony Dodawania pióra do obszaru nazw atlasu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>kodzie CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista szablonów obrazów

W tej tabeli wymieniono wszystkie szablony obrazów aktualnie dostępne w zestawie SDK sieci Web usługi Azure Maps. Nazwa szablonu znajduje się nad każdym obrazem. Domyślnie kolor podstawowy to niebieski, a kolor pomocniczy jest biały. Aby kolor pomocniczy był lepiej widoczny na białym tle, na poniższych obrazach kolor pomocniczy jest ustawiony na czarny.

**Szablony ikon symboli**

|||||
|:-:|:-:|:-:|:-:|
| Znacznik | marker grubości | okrąg znacznika | marker płaski |
|![ikona znacznika](./media/image-templates/marker.png)|![ikona o grubości znacznika](./media/image-templates/marker-thick.png)|![ikona okręgu znacznika](./media/image-templates/marker-circle.png)|![ikona znacznika płaskiego](./media/image-templates/marker-flat.png)|
||||
| marker-kwadrat | znacznik-kwadrat-klaster | strzałka znacznika | marker-ball-pin | 
|![ikona znacznika-kwadratu](./media/image-templates/marker-square.png)|![ikona znacznika-kwadrat-klastra](./media/image-templates/marker-square-cluster.png)|![ikona strzałki znacznika](./media/image-templates/marker-arrow.png)|![ikona marker-ball-pin](./media/image-templates/marker-ball-pin.png)|
||||
| marker kwadratowy zaokrąglone | znacznik-kwadrat zaokrąglony-klaster | flag | flaga-trójkąt |
| ![ikona znacznika zaokrąglona](./media/image-templates/marker-square-rounded.png) | ![ikona znacznika zaokrąglone-klaster](./media/image-templates/marker-square-rounded-cluster.png) | ![ikona flagi](./media/image-templates/flag.png) | ![ikona trójkąta flagi](./media/image-templates/flag-triangle.png) |
||||
| trójkąt | trójkąt grubości | trójkąt-strzałka w górę | trójkąt-strzałka w lewo |
| ![ikona trójkąta](./media/image-templates/triangle.png) | ![ikona trójkąta](./media/image-templates/triangle-thick.png) | ![ikona strzałki trójkąta w górę](./media/image-templates/triangle-arrow-up.png) | ![ikona strzałki trójkąta w lewo](./media/image-templates/triangle-arrow-left.png) |
||||
| Sześciokąt | sześciokątny | sześciokątny zaokrąglony | sześciokątny zaokrąglony grubości |
| ![Ikona sześciokąta](./media/image-templates/hexagon.png) | ![ikona sześciokątna](./media/image-templates/hexagon-thick.png) | ![ikona sześciokątna](./media/image-templates/hexagon-rounded.png) | ![ikona o grubości sześciokątnej](./media/image-templates/hexagon-rounded-thick.png) |
||||
| przypinanie | pin-round | zaokrąglony kwadrat | zaokrąglona kwadratowa gruba |
| ![ikona przypinania](./media/image-templates/pin.png) | ![ikona pin-round](./media/image-templates/pin-round.png) | ![ikona zaokrąglony kwadrat](./media/image-templates/rounded-square.png) | ![zaokrąglona ikona o grubości kwadratu](./media/image-templates/rounded-square-thick.png) |
||||
| strzałka w górę | strzałka w górę cienki | car ||
| ![ikona strzałki w górę](./media/image-templates/arrow-up.png) | ![ikona strzałki w górę](./media/image-templates/arrow-up-thin.png) | ![ikona samochodu](./media/image-templates/car.png) | |

**Szablony wzorków wypełnienia wielokątów**

|||||
|:-:|:-:|:-:|:-:|
| Kontroler | kontroler obrócony | Okręgi | koła rozmieszczone |
| ![ikona sprawdzania](./media/image-templates/checker.png) | ![ikona obrócona przez kontroler](./media/image-templates/checker-rotated.png) | ![ikona kół](./media/image-templates/circles.png) | ![ikona odstępów między okręgami](./media/image-templates/circles-spaced.png) |
|||||
| ukośne linie-up | ukośne linie w dół | ukośne paski-up | ukośne paski w dół |
| ![ikona ukośnych linii](./media/image-templates/diagonal-lines-up.png) | ![ikona ukośnych linii w dół](./media/image-templates/diagonal-lines-down.png) | ![ikona ukośnych pasków](./media/image-templates/diagonal-stripes-up.png) | ![ikona ukośnych pasków w dół](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linie siatki | obróconych linii siatki | paski z kratą | x-wypełnienie |
| ![ikona linii siatki](./media/image-templates/grid-lines.png) | ![Ikona linii obróconych siatką](./media/image-templates/rotated-grid-lines.png) | ![Ikona pasków z obróconą siatką](./media/image-templates/rotated-grid-stripes.png) | ![Ikona x-fill](./media/image-templates/x-fill.png) |
|||||
| zygzakowatego | zygzakowato-pionowe | Kropki |  |
| ![ikona zygzakowatego](./media/image-templates/zig-zag.png) | ![ikona zygzakowatego pionu](./media/image-templates/zig-zag-vertical.png) | ![ikona kropki](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Wypróbuj teraz narzędzie

Za pomocą następującego narzędzia można renderować różne wbudowane szablony obrazów na różne sposoby i dostosowywać kolory podstawowe i pomocnicze oraz skalę.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opcje szablonu ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>opcje szablonu ikony</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [obszar nazw atlasu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, w których można używać szablonów obrazów:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie twórców HTML](map-add-bubble-layer.md)
