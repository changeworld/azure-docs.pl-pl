---
title: Konfigurowanie konwersji modelu
description: Opis wszystkich parametrów konwersji modelu
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681573"
---
# <a name="configure-the-model-conversion"></a>Konfigurowanie konwersji modelu

W tym rozdziale jest dokumentowanie opcji konwersji modelu.

## <a name="settings-file"></a>Plik ustawień

Jeśli plik `ConversionSettings.json` wywoływany znajduje się w kontenerze wejściowym obok modelu wejściowego, a następnie jest używany do zapewnienia dodatkowej konfiguracji dla procesu konwersji modelu.

Zawartość pliku powinna spełniać następujące schematy json:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Przykładowy `ConversionSettings.json` plik może być:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parametry geometrii

* `scaling`- Ten parametr skaluje model równomiernie. Skalowanie może służyć do powiększania lub zmniejszania modelu, na przykład do wyświetlania modelu budynku na blacie stołu. Ponieważ aparat renderowania oczekuje długości, które mają być określone w metrach, inne ważne użycie tego parametru powstaje, gdy model jest zdefiniowany w różnych jednostkach. Na przykład jeśli model jest zdefiniowany w centymetrach, zastosowanie skali 0,01 powinno spowodować, że model będzie miał prawidłowy rozmiar.
Niektóre formaty danych źródłowych (na przykład .fbx) zapewniają wskazówkę skalowania jednostki, w którym to przypadku konwersja niejawnie skaluje model do jednostek miernika. Skalowanie niejawne dostarczone przez format źródłowy zostaną zastosowane na górze parametru skalowania.
Końcowy współczynnik skalowania jest stosowany do wierzchołków geometrii i lokalnych przekształceń węzłów wykresu sceny. Skalowanie transformacji jednostki głównej pozostaje niezmodyfikowane.

* `recenterToOrigin`- Stwierdza, że model powinien zostać przekonwertowany w taki sposób, aby jego obwiednia była wyśrodkowana na początku.
Centrowanie jest ważne, jeśli model źródłowy jest przesunięty z dala od źródła, ponieważ w tym przypadku zmiennoprzecinkowe problemy z precyzją mogą powodować renderowanie artefaktów.

* `opaqueMaterialDefaultSidedness`- Silnik renderowania zakłada, że materiały nieprzezroczyste są dwustronne.
Jeśli nie jest to zamierzone zachowanie, ten parametr powinien być ustawiony na "SingleSided". Aby uzyskać więcej informacji, zobacz [renderowanie jednostronne](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Nadpisy

* `material-override`- Ten parametr pozwala na dostosowanie przetwarzania materiałów [podczas konwersji.](override-materials.md)

### <a name="color-space-parameters"></a>Parametry przestrzeni kolorów

Aparat renderowania oczekuje, że wartości kolorów będą w przestrzeni liniowej.
Jeśli model jest zdefiniowany przy użyciu przestrzeni gamma, opcje te powinny być ustawione na true.

* `gammaToLinearMaterial`- Konwersja kolorów materiału z przestrzeni gamma do przestrzeni liniowej
* `gammaToLinearVertex`- Konwersja kolorów wierzchołków z przestrzeni gamma na przestrzeń liniową

> [!NOTE]
> W przypadku plików FBX `true` ustawienia te są domyślnie ustawione na. Dla wszystkich innych typów plików `false`wartość domyślna to .

### <a name="scene-parameters"></a>Parametry sceny

* `sceneGraphMode`- Określa, jak wykres sceny w pliku źródłowym jest konwertowany:
  * `dynamic`(domyślnie): Wszystkie obiekty w pliku są udostępniane jako [jednostki](../../concepts/entities.md) w interfejsie API i mogą być przekształcane niezależnie. Hierarchia węzłów w czasie wykonywania jest identyczna ze strukturą w pliku źródłowym.
  * `static`: Wszystkie obiekty są widoczne w interfejsie API, ale nie można przekształcić niezależnie.
  * `none`: Wykres sceny jest zwinięty na jeden obiekt.

Każdy tryb ma inną wydajność środowiska uruchomieniowego. W `dynamic` trybie koszt wydajności skaluje się liniowo z liczbą [elementów](../../concepts/entities.md) na wykresie, nawet jeśli żadna część nie zostanie przeniesiona. Powinien być używany tylko wtedy, gdy części ruchome indywidualnie są niezbędne do aplikacji, na przykład dla animacji "widoku eksplozji".

Tryb `static` eksportuje pełny wykres sceny, ale części wewnątrz tego wykresu mają stałe przekształcenie względem jego części głównej. Węzeł główny obiektu nadal można jednak przenosić, obracać lub skalować bez znaczonych kosztów wydajności. Ponadto [zapytania przestrzenne](../../overview/features/spatial-queries.md) zwracają poszczególne części, a każda część może być modyfikowana za pomocą [nadpisań stanu.](../../overview/features/override-hierarchical-state.md) W tym trybie obciążenie środowiska wykonawczego na obiekt jest znikome. Jest to idealne rozwiązanie dla dużych scen, w których nadal potrzebujesz inspekcji na obiekt, ale nie ma zmian przekształcania na obiekt.

Tryb `none` ma najmniejsze obciążenie czasu wykonywania, a także nieco lepsze czasy ładowania. Inspekcja lub przekształcenie pojedynczych obiektów nie jest możliwe w tym trybie. Przypadki użycia to na przykład modele fotogrametrii, które nie mają na pierwszym miejscu znaczącego wykresu sceny.

> [!TIP]
> Wiele aplikacji załaduje wiele modeli. Parametry konwersji dla każdego modelu należy zoptymalizować w zależności od tego, jak będzie on używany. Na przykład, jeśli chcesz wyświetlić model samochodu, aby użytkownik rozjął się i `dynamic` sprawdził szczegółowo, musisz przekonwertować go w trybie. Jeśli jednak dodatkowo chcesz umieścić samochód w środowisku sali pokazowej, model `sceneGraphMode` ten `static` można `none`przekonwertować z zestawem lub nawet .

### <a name="physics-parameters"></a>Parametry fizyki

* `generateCollisionMesh`- Jeśli potrzebujesz obsługi [zapytań przestrzennych](../../overview/features/spatial-queries.md) w modelu, ta opcja musi być włączona. W najgorszym przypadku utworzenie siatki kolizji może podwoić czas konwersji. Ładowanie modeli z siatkami kolizji trwa `dynamic` dłużej, a podczas korzystania z wykresu sceny mają również wyższe obciążenie wydajnością środowiska uruchomieniowego. Aby uzyskać ogólną optymalną wydajność, należy wyłączyć tę opcję we wszystkich modelach, w których nie są potrzebne zapytania przestrzenne.

### <a name="unlit-materials"></a>Nieoświetlone materiały

* `unlitMaterials`- Domyślnie konwersja woli tworzyć [materiały PBR](../../overview/features/pbr-materials.md). Ta opcja informuje konwerter, aby zamiast tego traktował wszystkie materiały jako [materiały kolorowe.](../../overview/features/color-materials.md) Jeśli masz dane, które już zawierają oświetlenie, takie jak modele utworzone za pomocą fotogrametrii, ta opcja pozwala szybko wymusić prawidłową konwersję dla wszystkich materiałów, bez konieczności [zastępowania każdego materiału](override-materials.md) indywidualnie.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konwersja ze starszych formatów FBX, z modelem materiału Phong

* `fbxAssumeMetallic`- Starsze wersje formatu FBX definiują swoje materiały za pomocą modelu materiału Phong. Proces konwersji musi wywnioskować, jak te materiały mapują do [modelu PBR](../../overview/features/pbr-materials.md)modułu renderowania. Zwykle działa to dobrze, ale niejednoznaczność może pojawić się, gdy materiał nie ma tekstur, wysokich wartości lustrzanych i nieszare albedo kolor. W tej sytuacji konwersja musi wybrać między priorytetem wysokich wartości lustrzanych, definiowaniem wysoce odblaskowego, metalowego materiału, w którym kolor albedo rozpuszcza się, lub priorytetem koloru albedo, definiując coś w rodzaju błyszczącego kolorowego plastiku. Domyślnie proces konwersji zakłada, że wartości wysoce lustrzane oznaczają materiał metaliczny w przypadkach, gdy ma zastosowanie niejednoznaczność. Ten parametr można `false` ustawić, aby przełączyć się na odwrotnie.

### <a name="coordinate-system-overriding"></a>Zastępowanie układu współrzędnych

* `axis`- Aby zastąpić układ współrzędnych wektorów jednostki. Wartościami `["+x", "+y", "+z"]`domyślnymi są . Teoretycznie format FBX ma nagłówek, w którym zdefiniowano te wektory, a konwersja wykorzystuje te informacje do przekształcenia sceny. Format glTF definiuje również stały układ współrzędnych. W praktyce niektóre zasoby mają nieprawidłowe informacje w nagłówku lub zostały zapisane przy innej konwencji układu współrzędnych. Ta opcja umożliwia zastąpienie układu współrzędnych w celu skompensowania. Na przykład: `"axis" : ["+x", "+z", "-y"]` wymieni oś Z i oś Y i zachowa układ współrzędnych, odwracając kierunek osi Y.

### <a name="vertex-format"></a>Format wierzchołka

Możliwe jest dostosowanie formatu wierzchołka dla siatki, aby wymienić precyzję w celu oszczędności pamięci. Mniejsza ilość pamięci pozwala załadować większe modele lub osiągnąć lepszą wydajność. Jednak w zależności od danych niewłaściwy format może znacząco wpłynąć na jakość renderowania.

> [!CAUTION]
> Zmiana formatu wierzchołka powinna być ostatecznością, gdy modele nie pasują już do pamięci lub podczas optymalizacji w celu uzyskania najlepszej możliwej wydajności. Zmiany mogą łatwo wprowadzić artefakty renderowania, zarówno oczywiste, jak i subtelne. Jeśli nie wiesz, na co zwrócić uwagę, nie należy zmieniać wartości domyślnej.

Korekty te są możliwe:

* Określone strumienie danych mogą być jawnie uwzględnione lub wykluczone.
* Dokładność strumieni danych można zmniejszyć, aby zmniejszyć zużycie pamięci.

Poniższa `vertex` sekcja `.json` w pliku jest opcjonalna. Dla każdej części, która nie jest jawnie określona, usługa konwersji powraca do ustawienia domyślnego.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Wymuszając składnik `NONE`do , jest gwarantowane, że siatka wyjściowa nie ma odpowiedniego strumienia.

#### <a name="component-formats-per-vertex-stream"></a>Formaty komponentów na strumień wierzchołków

Formaty te są dozwolone dla odpowiednich składników:

| Składnik wierzchołka | Obsługiwane formaty (pogrubienie = domyślne) |
|:-----------------|:------------------|
|pozycja| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|kolor0| **8_8_8_8_UNSIGNED_NORMALIZED**, BRAK |
|kolor1| 8_8_8_8_UNSIGNED_NORMALIZED, **BRAK**|
|Normalne| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, BRAK |
|Stycznej| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, BRAK |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, BRAK |
|texcoord0 ( texcoord0 )| **32_32_FLOAT**, 16_16_FLOAT, BRAK |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, BRAK |

#### <a name="supported-component-formats"></a>Obsługiwane formaty składników

Rozmiary pamięci formatów są następujące:

| Format | Opis | Bajty na wierzchołek |
|:-------|:------------|:---------------|
|32_32_FLOAT|dwuskładnikowa pełna dokładność zmiennoprzecinkowa|8
|16_16_FLOAT|dwuskładnikowa pół zmiennoprzecinkowa precyzja|4
|32_32_32_FLOAT|trzyskładkowa pełna precyzja zmiennoprzecinkowa|12
|16_16_16_16_FLOAT|czteroskładnikowa pół zmiennoprzecinkowa precyzja|8
|8_8_8_8_UNSIGNED_NORMALIZED|bajt czteroskładnikowy, znormalizowany do `[0; 1]` zakresu|4
|8_8_8_8_SIGNED_NORMALIZED|bajt czteroskładnikowy, znormalizowany do `[-1; 1]` zakresu|4

#### <a name="best-practices-for-component-format-changes"></a>Najważniejsze wskazówki dotyczące zmian formatu składników

* `position`: Rzadko zdarza się, że wymagana jest zmniejszona dokładność. **16_16_16_16_FLOAT** wprowadza zauważalne artefakty kwantyzacji, nawet dla małych modeli.
* `normal`, `tangent` `binormal`: Zazwyczaj te wartości są zmieniane razem. O ile nie istnieją zauważalne artefakty świetlne, które wynikają z normalnej kwantyzacji, nie ma powodu, aby zwiększyć ich dokładność. W niektórych przypadkach jednak te składniki można ustawić na **NONE:**
  * `normal`, `tangent`i `binormal` są potrzebne tylko wtedy, gdy powinien świecić się co najmniej jeden materiał w modelu. W ARR jest to przypadek, gdy [materiał PBR](../../overview/features/pbr-materials.md) jest używany w modelu w dowolnym momencie.
  * `tangent`i `binormal` są potrzebne tylko wtedy, gdy którykolwiek z oświetlonych materiałów używa normalnej tekstury mapy.
* `texcoord0`, `texcoord1` : Współrzędne tekstury mogą używać zmniejszonej dokładności `[0; 1]` (**16_16_FLOAT),** gdy ich wartości pozostają w zakresie i gdy tekstury adresowane mają maksymalny rozmiar 2048 x 2048 pikseli. Jeśli te limity zostaną przekroczone, jakość mapowania tekstur ucierpi.

#### <a name="example"></a>Przykład

Załóżmy, że masz model fotogrametrii, który ma oświetlenie pieczone w teksturach. Wszystko, co jest potrzebne do renderowania modelu są pozycje wierzchołków i współrzędne tekstury.

Domyślnie konwerter musi zakładać, że w pewnym momencie można użyć materiałów `normal`PBR w modelu, więc wygeneruje program `tangent`, i `binormal` dane dla Ciebie. W związku z tym użycie `position` pamięci na wierzchołek jest (12 `texcoord0` bajtów) + (8 bajtów) + `normal` (4 bajty) + `tangent` (4 bajty) + `binormal` (4 bajtów) = 32 bajtów. Większe modele tego typu mogą łatwo mieć wiele milionów wierzchołków, co powoduje, że modele mogą zająć wiele gigabajtów pamięci. Tak duże ilości danych będą miały wpływ na wydajność i może nawet zabraknąć pamięci.

Wiedząc, że nigdy nie potrzebujesz dynamicznego oświetlenia w modelu `[0; 1]` i wiedząc, `normal`że `tangent`wszystkie `binormal` `NONE` współrzędne tekstury znajdują się w zasięgu, można ustawić , i do i `texcoord0` do połowy precyzji (`16_16_FLOAT`), co daje tylko 16 bajtów na wierzchołek. Zmniejszenie danych siatki na pół umożliwia ładowanie większych modeli i potencjalnie zwiększa wydajność.

## <a name="typical-use-cases"></a>Typowe przypadki użycia

Znalezienie dobrych ustawień importu dla danego przypadku użycia może być żmudnym procesem. Z drugiej strony ustawienia konwersji mogą mieć znaczący wpływ na wydajność środowiska uruchomieniowego.

Istnieją pewne klasy przypadków użycia, które kwalifikują się do określonych optymalizacji. Poniżej podano kilka przykładów.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Przypadek użycia: Wizualizacja architektoniczna / duże mapy zewnętrzne

* Tego typu sceny wydają się być statyczne, co oznacza, że nie potrzebują ruchomych części. W związku `sceneGraphMode` z tym `static` można `none`ustawić lub nawet , co poprawia wydajność środowiska uruchomieniowego. W `static` trybie węzeł główny sceny nadal można przenosić, obracać i skalować, na przykład dynamicznie przełączać się między skalą 1:1 (dla widoku z perspektywy pierwszej osoby) a widokiem z góry tabeli.

* Gdy trzeba przenieść części, zazwyczaj oznacza to również, że potrzebujesz wsparcia dla raycastów lub innych [zapytań przestrzennych,](../../overview/features/spatial-queries.md)aby można było wybrać te części w pierwszej kolejności. Z drugiej strony, jeśli nie zamierzasz coś poruszać, szanse są wysokie, że również nie potrzebujesz go do udziału `generateCollisionMesh` w zapytaniach przestrzennych i dlatego możesz wyłączyć flagę. Ten przełącznik ma znaczący wpływ na czas konwersji, czas ładowania, a także koszty aktualizacji czasu wykonywania na klatkę.

* Jeśli aplikacja nie używa płaszczyzn `opaqueMaterialDefaultSidedness` [cięcia,](../../overview/features/cut-planes.md)flaga powinna być wyłączona. Przyrost wydajności wynosi zazwyczaj 20%-30%. Wytnij płaszczyzny mogą być nadal używane, ale nie będzie tylnych ścian, patrząc na wewnętrzne części obiektów, co wygląda na sprzeczne z intuicją. Aby uzyskać więcej informacji, zobacz [renderowanie jednostronne](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Przypadek użycia: Modele fotogrametrii

Podczas renderowania modeli fotogrametrii zazwyczaj nie ma potrzeby tworzenia wykresu `sceneGraphMode` `none`sceny, więc można ustawić na . Ponieważ te modele rzadko zawierają złożony wykres sceny na początek, wpływ tej opcji powinien być jednak nieznaczny.

Ponieważ oświetlenie jest już pieczone w teksturach, nie jest potrzebne dynamiczne oświetlenie. Zatem:

* Ustaw `unlitMaterials` flagę `true` tak, aby wszystkie materiały zamieniły się w nieoświetlone [materiały kolorowe](../../overview/features/color-materials.md).
* Usuń niepotrzebne dane z formatu wierzchołka. Zobacz [powyższy przykład.](#example)

### <a name="use-case-visualization-of-compact-machines-etc"></a>Przypadek użycia: Wizualizacja kompaktowych maszyn itp.

W takich przypadkach modele często mają bardzo wysokie szczegóły w małej objętości. Moduł renderowania jest mocno zoptymalizowany do obsługi takich przypadków dobrze. Jednak większość optymalizacji wymienionych w poprzednim przypadku użycia nie ma zastosowania w tym miejscu:

* Poszczególne części powinny być wybieralne i `sceneGraphMode` ruchome, `dynamic`więc należy pozostawić do .
* Rzutki promieniowe są zazwyczaj integralną częścią aplikacji, więc siatki kolizji muszą być generowane.
* Wycięte samoloty `opaqueMaterialDefaultSidedness` wyglądają lepiej z włączoną flagą.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](model-conversion.md)
* [Materiały kolorowe](../../overview/features/color-materials.md)
* [Materiały PBR](../../overview/features/pbr-materials.md)
* [Zastępowanie materiałów podczas konwersji modelu](override-materials.md)
