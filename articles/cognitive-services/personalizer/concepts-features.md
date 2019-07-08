---
title: 'Funkcje: Akcji i kontekstu - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer używa funkcji, informacje o akcji i kontekstu, aby zapewnić lepsze sugestie klasyfikacji. Funkcje może być bardzo ogólny lub specyficzne dla elementu.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: edjez
ms.openlocfilehash: 94eaeb6e34e74e1a0f1a3958c23cf33b86c4adcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620277"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkcje są informacje o akcji i kontekstu

Usługa Personalizer działa dzięki informacjom o jakie aplikacji powinien być wyświetlony dla użytkowników w danym kontekście.

Używa personalizer **funkcji**, czyli informacji o **bieżącego kontekstu** wybrać najlepszy **akcji**. Funkcje reprezentują wszystkie informacje, które Twoim zdaniem mogą pomóc spersonalizować do osiągnięcia wyższej nagrody. Funkcje może być bardzo ogólny lub specyficzne dla elementu. 

Na przykład masz **funkcji** temat:

* _Użytkownika_ takich jak `UserID`. 
* _Zawartości_ np. Jeśli wideo jest `Documentary`, `Movie`, lub `TV Series`, lub czy element handlu detalicznego dostępną w sklepie.
* _Bieżącego_ okresu czasu, takich jak dzień tygodnia był.

Personalizer nie określają, ograniczania i naprawić jakich funkcji możesz wysłać dla akcji i kontekstu:

* Jeśli ich nie masz, możesz wysłać niektóre funkcje, w przypadku niektórych działań, a nie dla innych. Na przykład rekordową popularność serialu mogą mieć atrybuty, które nie mają filmów.
* Może zawierać pewne funkcje, które są dostępne tylko kilka razy. Aplikacja mobilna może na przykład można podać więcej informacji, niż strony sieci web. 
* Wraz z upływem czasu użytkownik może dodawać i usuwać funkcji o kontekście i akcje. Personalizer w dalszym ciągu uczenie się z dostępnymi informacjami.
* Musi istnieć co najmniej jedną funkcję w kontekście. Personalizer nie obsługuje pustego kontekstu. Wysłanie tylko stały kontekstu każdym razem, Personalizer wybierze akcję dla klasyfikacji tylko dotyczące funkcji w akcji. 
* Personalizer podejmie próbę wybrać akcje, które najlepiej dla wszystkich użytkowników w dowolnym momencie.

## <a name="supported-feature-types"></a>Funkcja obsługiwane typy

Personalizer obsługuje funkcje ciągu, typów liczbowych i logicznych.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Wpływ wybranego typu funkcji usługi Machine Learning w Personalizer

* **Ciągi**: Dla typów ciągów każdej kombinacji klucza i wartości tworzy nowe wagi w modelu uczenia maszynowego Personalizer. 
* **Liczbowe**: Należy używać wartości liczbowe, gdy liczba proporcjonalnie wpływa na wynik personalizacji. To bardzo scenariusz zależnych. W uproszczony przykład np. gdy personalizowanie handlu detalicznego środowisko, NumberOfPetsOwned może być funkcją, która jest wartością liczbową, jak możesz zechcieć osobom Zwierzęta 2 lub 3 w celu wywierania wpływu na wynik personalizacji 1 pet występują dwa razy lub w trzy razy tyle. Funkcje, które są oparte na jednostkach liczbowych, ale których znaczenie nie jest liniowy — takie jak wiek, temperatury lub osoby Height - najlepiej są kodowane jako ciągi i jakości funkcji zazwyczaj można zwiększyć za pomocą zakresów. Na przykład może być kodowane wiek "Wiek": "0-5", "Wiek": "6-10" itp.
* **Wartość logiczna** wysłane o wartości "false" działanie tak, jakby nie została wysłana na wszystkich wartości.

Funkcje, które nie są obecne, należy pominąć z żądania. Unikanie wysyłania funkcji z wartością null, ponieważ będzie on przetworzony jako istniejące i o wartości "null" podczas uczenia modelu.

## <a name="categorize-features-with-namespaces"></a>Kategoryzowanie funkcji, korzystając z przestrzeni nazw

Trwa personalizer w funkcjach zorganizowanych w przestrzenie nazw. Należy określić, w aplikacji, jeśli są używane przestrzenie nazw i co powinny one być. Przestrzenie nazw są używane do grupowania funkcji dotyczących podobnego tematu lub funkcji, które pochodzą z danego źródła.

Poniżej przedstawiono przykłady funkcji obszarów nazw używanych przez aplikacje:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Pogoda
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Możesz nazwać przestrzenie nazw funkcji zgodne z konwencjami własne tak długo, jak są one prawidłowe kluczy JSON.

W poniższym formacie JSON `user`, `state`, i `device` to funkcja w przestrzeni nazw.

Obiekty JSON może zawierać zagnieżdżone obiekty JSON i prostych wartości. Tablicę można uwzględnić tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak funkcja ustawia bardziej skuteczna Personalizer

Zestaw funkcji dobre pomaga Personalizer Dowiedz się, jak przewidywać akcję, która będą miały najwyższą nagrody. 

Należy wziąć pod uwagę, funkcje wysyłania do API ranga Personalizer, który tych zaleceń:

* Ma wystarczającej liczby funkcji personalizacji dysku. Bardziej precyzyjne docelowe zawartości musi być, więcej funkcji są potrzebne.

* Brak wystarczającej liczby funkcji w różnych *gęstości*. Funkcja jest *gęstą* Jeśli wiele elementów są zgrupowane w kilku zasobników. Na przykład, tysiące filmy wideo mogą być klasyfikowane jako "Long" (o długości ponad 5 min) oraz "Short" (poniżej 5 min długie). Jest to *bardzo gęstą* funkcji. Z drugiej strony tym samym tysięcy elementów może mieć atrybutu o nazwie "Title", który prawie nigdy nie będzie mieć taką samą wartość z jednego elementu na inny. Jest to bardzo bez stojaku lub *rozrzedzone* funkcji.  

O funkcji o wysokiej gęstości pomaga Personalizer ekstrapolację uczenia z jednego elementu. Ale jeśli istnieje tylko kilka funkcji, są one zbyt gęstą Personalizer podejmie próbę dokładnie docelowej zawartości za pomocą tylko kilku zasobników do wyboru.

### <a name="improve-feature-sets"></a>Poprawa zestawy funkcji 

Analizuj zachowania użytkowników, wykonując ocenę w trybie Offline. Dzięki temu można spojrzeć na ostatnich danych, aby zobaczyć, co funkcje są silnie Współtworzenie dodatnią korzyści w stosunku do tych, które są mniej współtworzenia. Możesz zobaczyć, jakie funkcje pomagają, a zadaniem Tobie i Twojej aplikacji, aby znaleźć lepsze funkcje wysyłać Personalizer umożliwiające poprawienie wyników jeszcze więcej.

Te sekcje są typowe rozwiązania dotyczące poprawy wysyłane do Personalizer funkcji.

#### <a name="make-features-more-dense"></a>Dostęp do bardziej gęstą funkcji

Istnieje możliwość zwiększyć swoje zestawy funkcji, edytując je w celu udostępnienia większej i bardziej lub mniej gęstą.

Na przykład sygnatury czasowej w dół, aby drugi jest funkcją bardzo rozrzedzone. Może on gęstą (efektywniejszymi) podczas klasyfikowania razy do "rano", "południe", "po południu" itd.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozwiń zestawy funkcji ekstrapolację informacje

Możesz także uzyskać większą liczbą funkcji planowanie nieeksplorowanych atrybutów, które mogą być uzyskane z informacji, którą już posiadasz. Na przykład w Personalizacja listy fikcyjne filmu, jest możliwe, weekend vs weekday elicits inaczej od użytkowników? Czas można rozszerzyć w taki sposób, aby mieć atrybut "weekend" lub "dzień roboczy". Czy świąt narodowych kultury dysku uwagi niektórych typów filmu? Na przykład atrybut "Halloween" przydaje się w miejscach, w których jest to stosowne. Czy jest możliwe, weather zawierającym wartość rainy ma znaczący wpływ na wybór filmu dla wielu osób? Z czasem i miejscem usługi pogody może dostarczyć, że informacje oraz możesz dodać je jako funkcja dodatkowa. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozwiń zestawy funkcji za pomocą sztucznej inteligencji i usług cognitive services

Sztucznej inteligencji i gotowe do uruchomienia Cognitive Services może być bardzo zaawansowane oprócz Personalizer. 

Stosując przetwarzanie wstępne elementów przy użyciu usług sztucznej inteligencji, możesz automatycznie wyodrębniać informacje, które może być odpowiednie na potrzeby personalizacji.

Na przykład:

* Można uruchomić pliku filmu za pośrednictwem [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) można wyodrębnić elementy sceny, tekst, opinii i innych atrybutów. Te atrybuty następnie można wprowadzić bardziej gęstą, aby uwzględnić cechy, które nie mają odpowiednich oryginalnych metadanych elementu. 
* Obrazy mogą być uruchamiane za pomocą obiektu wykrywania, twarzy, za pośrednictwem tonacji itp.
* Informacje w formacie tekstowym może zostać rozszerzony o wyodrębnianie podmiotów, opinii i rozszerzanie jednostek z grafu wiedzy Bing, itd.

Można użyć kilku innych [usług Azure Cognitive Services](https://www.microsoft.com/cognitive-services), takiej jak

* [Łączenie jednostek](../entitylinking/home.md)
* [Analiza tekstu](../text-analytics/overview.md)
* [Rozpoznawania emocji](../emotion/home.md)
* [Przetwarzanie obrazów](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Akcje reprezentuje listę opcji

Każda akcja:

* Ma identyfikator.
* Zawiera listę funkcji.
* Lista funkcji mogą być duże (setki), ale firma Microsoft zaleca oceny funkcji skuteczność można usunąć funkcji, które nie są Współtworzenie Uzyskiwanie korzyści. 
* Funkcje w **akcje** może być lub może nie mieć wszelka korelacja funkcji **kontekstu** posługują się Personalizer.
* Funkcje dla akcji mogą być obecne w pewne działania, a innych nie. 
* Funkcje dla Identyfikatora działania może być dostępny jeden dzień, ale później staną się niedostępne. 

Algorytmów uczenia maszynowego firmy personalizer będą działać lepiej, gdy istnieją zestawy funkcji stabilny, ale wywołania rangi zakończy się niepowodzeniem, jeśli zmiany zestawu funkcji wraz z upływem czasu.

Nie będą wysyłane w więcej niż 50 akcji wykonywanych podczas klasyfikacji akcji. Może to być te same akcje 50 każdym lub mogą one ulec zmianie. Na przykład jeśli masz katalog produktów, 10 000 elementów w aplikacji handlu elektronicznego, może umożliwia zalecenie lub filtrowania aparatu górnej 40, klient może, takich jak i użyj Personalizer, aby znaleźć ten, który zostanie wygenerowany przez większość osób trzecich (na przykład określić użytkownik doda do koszyka) dla bieżącego kontekstu.


### <a name="examples-of-actions"></a>Przykłady akcji

Akcje, które możesz wysłać do interfejsu API ranga będzie zależeć od tego, co chcesz spersonalizować.

Oto kilka przykładów:

|Przeznaczenie|Akcja|
|--|--|
|Spersonalizuj artykułu, który jest wyróżniona na witryny sieci Web grup dyskusyjnych.|Każda akcja jest potencjalnym artykułu z wiadomościami.|
|Optymalizuj umieszczania ad w witrynie internetowej.|Każda akcja będzie układu lub reguły, aby utworzyć układ reklam (np. na górze w prawo, małe obrazy, duże obrazy).|
|Wyświetlanie spersonalizowanych klasyfikacji polecane elementy na zakupów witryna sieci Web.|Każda akcja jest określony produkt.|
|Zaproponuj elementy interfejsu użytkownika, takie jak filtrów do zastosowania do określonego zdjęcia.|Każde działanie może być inny filtr.|
|Wybierz odpowiedź czatbot wyjaśnienia intencji użytkownika lub zasugerować akcji.|Każda akcja jest opcją jak interpretować odpowiedzi.|
|Wybierz elementy do pokazania w górnej części listy wyników wyszukiwania|Każda akcja jest jednym z najważniejszych kilku wyników wyszukiwania.|


### <a name="examples-of-features-for-actions"></a>Przykłady funkcji dla akcji

Poniżej przedstawiono dobre przykłady funkcji dla akcji. Te zależy w dużej mierze każdej aplikacji.

* Funkcje o właściwościach akcji. Na przykład jest filmów lub telewizji serii?
* Funkcje o jak użytkownicy mogą mieć dostosowany za pomocą tej akcji w przeszłości. Na przykład ten film przede wszystkim jest widoczne dla osób z dane demograficzne, A lub B, jest zwykle nie więcej niż jeden raz w gry.
* Funkcje o charakterystyki jak użytkownik *widzi* akcje. Na przykład czy plakatu dla filmu objętego twarzy miniatury include, samochodach czy zapewniały realizację niezbędnych zadań?

### <a name="load-actions-from-the-client-application"></a>Ładowanie działań z aplikacji klienta

Funkcje z akcji zazwyczaj mogą pochodzić z systemów zarządzania zawartością, katalogi i polecania systemów. Twoja aplikacja jest odpowiedzialna za ładowanie informacji o akcjach z odpowiednie bazy danych i systemów, które masz. Jeśli Twoje działania nie zmieniaj lub usuniętych załadować każdym razem, gdy ma niepotrzebne wpływ na wydajność, można dodać logikę w aplikacji, aby te informacje w pamięci podręcznej.

### <a name="prevent-actions-from-being-ranked"></a>Zapobiegaj akcji są pozycjonowane

W niektórych przypadkach istnieją akcje, które nie mają być wyświetlane użytkownikom. Najlepszym sposobem, aby zapobiec akcję ze strony są pozycjonowane jako najwyższego poziomu jest przede wszystkim dołączanie do interfejsu API rangi na liście akcji.

W niektórych przypadkach można tylko ustalić później w logice biznesowej, jeśli wynikowa _akcji_ API ranga wywołanie ma być wyświetlana użytkownikowi. W takich przypadkach należy użyć _nieaktywne zdarzenia_.

## <a name="json-format-for-actions"></a>Format JSON działania

Podczas wywoływania rangę, będzie wysyłać wielu akcji do wyboru:

Obiekty JSON może zawierać zagnieżdżone obiekty JSON i prostych wartości. Tablicę można uwzględnić tylko wtedy, gdy elementy tablicy są liczbami. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Przykłady informacji kontekstowych

Informacje dotyczące _kontekstu_ zależy od każdego przypadku zastosowania i użycia, ale zazwyczaj może ono obejmować informacje takie jak:

* Informacje demograficzne i profil o użytkowniku.
* Informacje wyodrębnione z nagłówków HTTP, takich jak agent użytkownika lub pochodzić od protokołu HTTP informacje, takie jak wyszukiwania wstecznego geograficznego na podstawie adresów IP.
* Informacje dotyczące bieżącego czasu, takich jak dni tygodnia, weekend lub nie, rano lub po południu, navaneetha krishnan lub nie itp.
* Informacjami wyodrębnionymi z aplikacji mobilnych, takich jak lokalizacja, przenoszenia lub poziom naładowania baterii.
* Historyczne agregacje zachowania użytkowników — takich jak co to są gatunki filmu tego użytkownika wyświetlił najbardziej.

Aplikacja jest odpowiedzialna za ładowanie informacji o kontekście z odpowiednie bazy danych, czujników i systemów, które mogą wiązać Ciebie. Jeśli informacje o kontekście nie powoduje zmiany, możesz dodać logikę w aplikacji, aby te informacje, przed wysłaniem ich do interfejsu API rangi w pamięci podręcznej.

## <a name="json-format-for-context"></a>Format JSON dla kontekstu 

Kontekst jest wyrażona jako obiekt JSON, który jest wysyłany do interfejsu API ranga:

Obiekty JSON może zawierać zagnieżdżone obiekty JSON i prostych wartości. Tablicę można uwzględnić tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

[Uczenia przez wzmacnianie](concepts-reinforcement-learning.md) 
