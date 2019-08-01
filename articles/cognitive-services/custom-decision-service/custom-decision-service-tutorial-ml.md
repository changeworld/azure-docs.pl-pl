---
title: 'Samouczek: cechowanie i specyfikacja cech — Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Samouczek dotyczący cechowania i specyfikacji cech uczenia maszynowego w usłudze Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 409dcf7dc224eaf2a3f51325010507b9182fadf2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707042"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Samouczek: cechowanie i specyfikacja cech

Ten samouczek dotyczy zaawansowanych funkcji uczenia maszynowego w usłudze Custom Decision Service. Samouczek składa się z dwóch części: [cechowanie](#featurization-concepts-and-implementation) i [specyfikacji cech](#feature-specification-format-and-apis). Cechowanie oznacza reprezentowanie danych w postaci konkretnych cech odnoszących się do uczenia maszynowego. Specyfikacja cech obejmuje format JSON i dodatkowe interfejsy API.

Domyślnie uczenie maszynowe w usłudze Custom Decision Service jest niewidoczne dla klienta. Cechy są automatycznie wyodrębniane z zawartości i używany jest standardowy algorytm uczenia przez wzmacnianie. Funkcja wyodrębniania korzysta z kilku innych usług Azure Cognitive Services: [łączenia jednostek](../entitylinking/home.md), [analizy tekstu](../text-analytics/overview.md), [rozpoznawania emocji](../emotion/home.md) i [przetwarzania obrazów](../computer-vision/home.md). Ten samouczek można pominąć w przypadku korzystania wyłącznie z funkcji domyślnych.

## <a name="featurization-concepts-and-implementation"></a>Cechowanie: koncepcje i implementacja

Usługa Custom Decision Service podejmuje decyzje pojedynczo. Każda decyzja wymaga dokonania wyboru spośród kilku alternatywnych rozwiązań, czyli akcji. W zależności od aplikacji decyzja może wiązać się z wyborem jednej akcji lub (krótkiej) uporządkowanej listy akcji.

Przykładem może być personalizacja artykułów wyświetlanych na stronie głównej witryny internetowej. W tym przypadku akcje odpowiadają artykułom, a każda decyzja sprowadza się do wyboru artykułów, które mają zostać zaprezentowane danemu użytkownikowi.

Każda akcja jest reprezentowana przez wektor właściwości, zwanych odtąd *cechami*. Oprócz cech wyodrębnionych automatycznie można też określić nowe cechy. Możliwe jest też skonfigurowanie usługi Custom Decision Service w taki sposób, aby można było rejestrować niektóre cechy i jednocześnie ignorować je w przypadku uczenia maszynowego.

### <a name="native-vs-internal-features"></a>Porównanie cech natywnych i cech wewnętrznych

Cechy można określić w formacie najbardziej naturalnym dla danej aplikacji — może to być liczba, ciąg lub tablica. Cechy te są nazywane „cechami natywnymi”. Usługa Custom Decision Service tłumaczy każdą natywną cechę na jedną lub więcej cech liczbowych, zwanych „cechami wewnętrznymi”.

Tłumaczenie na cechy wewnętrzne odbywa się następująco:

- Cechy liczbowe pozostają niezmienione.
- Tablica liczbowa jest tłumaczona na kilka cech liczbowych — po jednej dla każdego elementu tablicy.
- Cecha o wartości ciągu `"Name":"Value"` jest domyślnie tłumaczona na cechę o nazwie `"NameValue"` i wartości 1.
- Opcjonalnie ciąg może być reprezentowany jako [zbiór wyrazów](https://en.wikipedia.org/wiki/Bag-of-words_model). Następnie tworzona jest cecha wewnętrzna dla każdego wyrazu w ciągu, której wartością jest liczba wystąpień tego wyrazu.
- Cechy wewnętrzne o wartości zero są pomijane.

### <a name="shared-vs-action-dependent-features"></a>Porównanie cech wspólnych i cech zależnych od akcji

Niektóre cechy odnoszą się do całej decyzji i są takie same dla wszystkich akcji. Nazywamy je *cechami wspólnymi*. Niektóre inne cechy są specyficzne dla określonej akcji. Nazywamy je *cechami zależnymi od akcji*.

W tym przykładzie cechy wspólne mogą opisywać użytkownika i/lub stan świata. Może to być np. geolokalizacja, wiek i płeć użytkownika oraz najważniejsze wydarzenia mające obecnie miejsce. Cechy zależne od akcji mogą opisywać właściwości danego artykułu, takie jak tematy, które są w nim poruszane.

### <a name="interacting-features"></a>Cechy interakcji

Cechy często „wchodzą w interakcje”, co oznacza, że działanie jednej zależy od innych. Na przykład cecha X wskazuje, czy użytkownik jest zainteresowany sportem. Cecha Y wskazuje, czy dany artykuł dotyczy sportu. W ostateczności skutek cechy Y jest w dużym stopniu zależny od cechy X.

Aby uwzględnić interakcję między cechami X i Y, należy utworzyć cechę *drugiego stopnia*, której wartość to X\*Y. (Mówi się też o „krzyżowaniu” cech X i Y). Można wybrać, które pary cech zostaną skrzyżowane.

> [!TIP]
> Cechę wspólną należy skrzyżować z cechami zależnymi od akcji, aby miała wpływ na ich pozycję. Aby wpływ na personalizację był zauważalny, cecha zależna od akcji powinna zostać skrzyżowana z cechami wspólnymi.

Innymi słowy, cecha wspólna nieskrzyżowana z żadnymi cechami zależnymi od akcji wpływa na każdą akcję w taki sam sposób. Cecha zależna od akcji, która nie została skrzyżowana z żadną cechą wspólną, również wpływa na każdą decyzję. Tego typu cechy mogą zmniejszyć wariancję szacunków dotyczących nagrody.

### <a name="implementation-via-namespaces"></a>Implementacja za pomocą przestrzeni nazw

Cechy krzyżowe można zaimplementować (podobnie jak inne koncepcje związane z personalizacją) za pomocą wiersza polecenia VW w portalu. Składnia opiera się na wierszu polecenia [Vowpal Wabbit](http://hunch.net/~vw/).

Decydującym znaczeniem dla implementacji jest koncepcja *przestrzeni nazw*, czyli nazwanego podzestawu cech. Każda cecha należy do dokładnie jednej przestrzeni nazw. Przestrzeń nazw można określić jawnie, gdy wartość cechy zostanie przekazana do usługi Custom Decision Service. To jest jedyny sposób odniesienia się do cechy w wierszu polecenia VW.

Przestrzeń nazw jest „współdzielona” lub „zależna od akcji”. Oznacza to, że w ramach jednej akcji może składać się tylko z cech wspólnych lub tylko cech zależnych od akcji.

> [!TIP]
> Dobrą praktyką jest opakowanie cech w jawnie określonych przestrzeniach nazw. Należy pogrupować powiązane cechy w tej samej przestrzeni nazw.

Jeśli nie określono przestrzeni nazw, cecha zostanie automatycznie przypisana do domyślnej przestrzeni nazw.

> [!IMPORTANT]
> Cechy i przestrzenie nazw nie muszą być spójne we wszystkich akcjach. Przestrzeń nazw może zawierać różne cechy dla różnych akcji. Co więcej, daną przestrzeń nazw można zdefiniować dla niektórych akcji, a dla innych już nie.

Wiele cech wewnętrznych pochodzących od tej samej cechy natywnej o wartości ciągu jest zgrupowanych w tej samej przestrzeni nazw. Dowolne dwie cechy natywne znajdujące się w różnych przestrzeniach nazw są traktowane jako odrębne — nawet jeśli mają taką samą nazwę.

> [!IMPORTANT]
> Chociaż występowanie długich i opisowych identyfikatorów przestrzeni nazw jest powszechne, to wiersz polecenia VW nie rozróżnia przestrzeni nazw, których identyfikator zaczyna się od tej samej litery. W dalszej części tego artykułu identyfikatory przestrzeni nazw są pojedynczymi literami: `x` i `y`.

Oto szczegóły dotyczące implementacji:

- Aby skrzyżować przestrzenie nazw `x` i `y`, wpisz polecenie `-q xy` lub `--quadratic xy`. To spowoduje, że każda cecha w przestrzeni nazw `x` zostanie skrzyżowana ze wszystkimi cechami w przestrzeni nazw `y`. Wpisz `-q x:`, aby skrzyżować przestrzeń nazw `x` z każdą przestrzenią nazw, oraz `-q ::`, aby skrzyżować wszystkie pary przestrzeni nazw.

- Aby zignorować wszystkie cechy w przestrzeni nazw `x`, wpisz `--ignore x`.

Te polecenia są stosowane do każdej akcji oddzielnie zawsze wtedy, gdy przestrzenie nazw są zdefiniowane.

### <a name="estimated-average-as-a-feature"></a>Szacowana średnia jako cecha

Przeprowadźmy mały eksperyment myślowy. Jaka byłaby średnia wartość nagrody danej akcji, gdyby została ona wybrana dla wszystkich decyzji? Taka średnia wartość nagrody może być wykorzystana jako miara „ogólnej jakości” tej akcji. Nie wiadomo dokładnie, kiedy inne akcje zostały wybrane zamiast niektórych decyzji. Można to jednak oszacować za pomocą technik uczenia przez wzmacnianie. Jakość tego oszacowania zazwyczaj ulega poprawie wraz z upływem czasu.

Tę „szacowaną średnią wartość nagrody” można uwzględnić jako cechę danej akcji. Następnie usługa Custom Decision Service automatycznie zaktualizuje te dane szacunkowe wraz z pojawianiem się nowych danych. Taka cecha akcji nazywana jest *cechą krańcową*. Cechy krańcowe mogą być używane w ramach uczenia maszynowego oraz przeprowadzanej inspekcji.

Aby dodać cechy krańcowe, wpisz `--marginal <namespace>` w wierszu polecenia VW. Zdefiniuj wartość `<namespace>` w formacie JSON w następujący sposób:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Dla danej akcji wstaw tę przestrzeń nazw wraz z innymi cechami zależnymi od akcji. Podaj tę definicję dla każdej decyzji, używając tych samych wartości `mf_name` i `action_id` dla wszystkich decyzji.

Cecha krańcowa jest dodawana do każdej akcji zawierającej wartość `<namespace>`. `action_id` może być dowolną nazwą cechy, która jednoznacznie identyfikuje akcję. Nazwę cechy ustawiono na `mf_name`. W szczególności cechy krańcowe o różnych nazwach `mf_name` są traktowane jako odmienne cechy — każda nazwa `mf_name` posiada inną wagę.

Domyślnie wartość `mf_name` jest taka sama dla wszystkich akcji. Następnie otrzymuje się jedną wagę dla wszystkich cech krańcowych.

Można również określić wiele cech krańcowych dla tej samej akcji, z tymi samymi wartościami, ale z różnymi nazwami cech.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>Kodowanie 1-hot

Niektóre cechy mogą być przedstawiane jako wektory bitowe, w których każdy bit odpowiada zakresowi możliwych wartości. Bit jest ustawiony na wartość 1, tylko wtedy, gdy cecha znajduje się w tym zakresie. W związku z tym istnieje jeden bit ustawiony na wartość 1, a pozostałe są ustawione na wartość 0. Taki sposób reprezentacji jest powszechnie nazywany *kodowaniem 1-hot*.

Kodowanie 1-hot jest typowe dla cech kategorii, takich jak „region geograficzny”, które nie mają z zasady istotnej reprezentacji liczbowej. Wskazane jest również, aby cechy liczbowe, których wpływ na nagrodę jest prawdopodobny, były nieliniowe. Na przykład dany artykuł może mieć znaczenie dla określonej grupy wiekowej i nie mieć znaczenia dla osób, które mają więcej i mniej lat.

Dowolna cecha o wartości ciągu jest domyślnie kodowana typem 1-hot: dla każdej możliwej wartości tworzona jest odrębna cecha wewnętrzna. Automatyczne kodowanie 1-hot dla cech liczbowych z niestandardowymi zakresami nie jest obecnie dostępne.

> [!TIP]
> Algorytmy uczenia maszynowego traktują wszystkie możliwe wartości danej cechy wewnętrznej w jednolity sposób, opierając się na wspólnej „wadze”. Kodowanie 1-hot pozwala przyjmować oddzielną „wagę” dla każdego zakresu wartości. Zmniejszanie zakresów prowadzi do lepszej jakości nagród dostępnych po zebraniu wystarczającej ilości danych, ale może też zwiększyć ilość danych potrzebnych do uzyskania zbieżności z lepszymi nagrodami.

## <a name="feature-specification-format-and-apis"></a>Specyfikacja cech: format i interfejsy API

Cechy można określić za pomocą kilku dodatkowych interfejsów API. Wszystkie interfejsy API używają typowego formatu JSON. Poniżej przedstawiono interfejsy API oraz format na poziomie koncepcyjnym. Specyfikację uzupełnia schemat struktury Swagger.

Podstawowy szablon JSON dotyczący specyfikacji cech jest następujący:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

W tym przypadku `<name>` i `<value>` oznaczają odpowiednio nazwę cechy i wartość cechy. `<value>` może być ciągiem, liczbą całkowitą, liczbą zmiennoprzecinkową, wartością logiczną lub tablicą. Cecha, której nie opakowano przestrzenią nazw, zostanie automatycznie przypisana do domyślnej przestrzeni nazw.

Aby przedstawić ciąg w postaci zbioru wyrazów, należy użyć specjalnej składni `"_text":"string"` zamiast `"<name>":<value>`. W efekcie dla każdego wyrazu w ciągu tworzona jest osobna cecha wewnętrzna. Jego wartość jest liczbą wystąpień tego wyrazu.

Jeśli wartość `<name>` rozpoczyna się od „_” (i nie jest to `"_text"`), cecha jest ignorowana.

> [!TIP]
> Czasami scalane są cechy z wielu źródeł JSON. Dla ułatwienia mogą być reprezentowane w następujący sposób:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

W tym przypadku wartość `<features>` odwołuje się do podstawowej specyfikacji cech zdefiniowanej wcześniej. Dozwolone są również głębsze poziomy „zagnieżdżania”. Usługa Custom Decision Service automatycznie znajduje „najgłębiej” zagnieżdżone obiekty JSON, które mogą być interpretowane jako `<features>`.

#### <a name="feature-set-api"></a>Interfejs Feature Set API

Interfejs Feature Set API zwraca listę cech w omówionym wcześniej formacie JSON. Można użyć kilku punktów końcowych interfejsu Feature Set API. Każdy punkt końcowy jest identyfikowany przez identyfikator zestawu cech i adres URL. Ustawienia mapowania między identyfikatorami zestawów cech i adresami URL wprowadza się w portalu.

Aby wywołać interfejs Feature Set API, należy wstawić odpowiedni identyfikator zestawu cech we właściwym miejscu w pliku JSON. W przypadku cech zależnych od akcji wywołanie zostanie automatycznie sparametryzowane przez identyfikator akcji. Dla tej samej akcji można określić kilka identyfikatorów zestawu cech.

#### <a name="action-set-api-json-version"></a>Interfejs Action Set API (wersja w formacie JSON)

Interfejs Action Set API zawiera wersję, w której akcje i cechy są określane w formacie JSON. Cechy mogą być określone jawnie i/lub za pośrednictwem interfejsów Feature Set API. Cechy wspólne można określić jednorazowo dla wszystkich akcji.

#### <a name="ranking-api-http-post-call"></a>Interfejs Ranking API (wywołanie metody HTTP POST)

Interfejs Ranking API zawiera wersję, która używa wywołania metody HTTP POST. Treść tego wywołania określa akcje i cechy za pomocą elastycznej składni JSON.

Akcje mogą być określone jawnie i/lub za pośrednictwem identyfikatorów zestawu akcji. Za każdym razem, gdy napotkany zostanie identyfikator zestawu akcji, wykonywane jest wywołanie odpowiedniego punktu końcowego interfejsu Action Set API.

Podobnie jak w przypadku interfejsu Action Set API, cechy mogą być określone jawnie i/lub za pośrednictwem interfejsów Feature Set API. Cechy wspólne można określić jednorazowo dla wszystkich akcji.
