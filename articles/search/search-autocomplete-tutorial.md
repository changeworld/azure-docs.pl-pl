---
title: Przykład funkcji autouzupełniania dodawania typeahead do pola wyszukiwania — usługa Azure Search
description: Włącz typeahead akcji zapytania w usłudze Azure Search, tworząc sugestory i sformułowania żądań, które wypełnić pole wyszukiwania z ukończonych terminów ani fraz.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: b78fdf0c493e4631e4cdd7e26b154570b6226d1f
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369583"
---
# <a name="example-add-autocomplete-to-partial-term-inputs-in-azure-search"></a>Przykład: Dodawanie funkcji autouzupełniania do wejść częściowe termin w usłudze Azure Search

Tę funkcję wersji zapoznawczej "zakończy się" wprowadzanie terminu częściowe poprzez dostarczenie ukończone termin z dokumentów do indeksu usługi Azure Search. Zwróć uwagę, tę funkcję w komercyjnych wyszukiwarek. Obecnie w publicznej wersji zapoznawczej można teraz dodać tej funkcji, do rozwiązania usługi Azure Search, aby uprościć pobierać zapytania.

W tym przykładzie dowiesz się, jak używać [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions), [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) i [aspektami](search-faceted-navigation.md) w [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/) i [Zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) do kompilacji okno wyszukiwania zaawansowanego. 

+ *Sugestie* udostępniają rekomendowane rzeczywiste wyniki na podstawie tekstu wpisanego przez użytkownika w danym momencie. 
+ *Autouzupełnianie*, [nowa funkcja dostępna w wersji zapoznawczej](search-api-preview.md) w usłudze Azure Search, udostępnia terminy z indeksu na potrzeby uzupełniania tekstu aktualnie wpisywanego przez użytkownika. 

Porównamy wiele technik mających na celu zwiększenie efektywności użytkowników przez udostępnienie użytkownikowi bogatych możliwości wyszukiwania bezpośrednio podczas wpisywania.

W tym przykładzie przedstawiono aplikacji opartych na programie ASP.NET MVC, która używa C# do wywołania [biblioteki klienta .NET usługi Azure Search](https://aka.ms/search-sdk)i JavaScript do wywołania interfejsu API REST usługi Azure Search bezpośrednio. Indeks, wypełniony jest przeznaczony dla aplikacji, w tym przykładzie [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) przykładowych danych. Istnieje możliwość użycia indeksu w wersji już skonfigurowanej na potrzeby pokazu NYC Jobs lub wypełnienia własnego indeksu za pomocą modułu ładującego dane w przykładowym rozwiązaniu NYCJobs. W przykładzie użyto bibliotek JavaScript [jQuery UI](https://jqueryui.com/autocomplete/) i [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) do utworzenia pola wyszukiwania obsługującego autouzupełnianie. Używając tych składników i usługi Azure Search, zapoznasz się z wieloma przykładami obsługi autouzupełniania z uzupełnianiem przy wpisywaniu w polu wyszukiwania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Pobieranie i konfigurowanie rozwiązania
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Implementowanie pola wejściowego wyszukiwania
> * Dodawanie obsługi listy autouzupełniania pobierającej dane ze źródła zdalnego 
> * Pobierz sugestie i automatycznego uzupełniania za pomocą zestawu SDK platformy .NET i interfejsu API REST
> * Dodawanie obsługi buforowania po stronie klienta w celu poprawy wydajności 

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2017. Możesz korzystać z bezpłatnego programu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Pobierz przykład [kod źródłowy](https://github.com/azure-samples/search-dotnet-getting-started) dla przykładu.

* (Opcjonalnie) Aktywne konto platformy Azure i usługa Azure Search. Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). Aby uzyskać pomoc dotyczącą aprowizowania usługi, zobacz [Tworzenie usługi wyszukiwania](search-create-service-portal.md). Konta i usługi są opcjonalne, ponieważ w tym przykładzie można wykonać przy użyciu hostowanego indeksu NYCJobs już w miejscu dla różnych wersji demonstracyjnej.

* (Opcjonalnie) Pobierz kod przykładu [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs), aby zaimportować dane NYCJobs do indeksu w ramach własnej usługi Azure Search.

> [!Note]
> Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów. Moduł ładujący dane NYCJobs tworzy dwa indeksy. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych indeksów.

### <a name="set-up-azure-search-optional"></a>Konfigurowanie usługi Azure Search (opcjonalnie)

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zaimportować dane przykładowej aplikacji NYCJobs do własnego indeksu. Ten krok jest opcjonalny.  Jeśli chcesz użyć udostępnionego przykładowego indeksu, przejdź do następnej sekcji, uruchamiając przykład.

1. W folderze DataLoader kodu przykładu NYCJobs otwórz plik rozwiązania DataLoader.sln w programie Visual Studio.

1. Zaktualizuj informacje o połączeniu dla usługi Azure Search.  Otwórz plik App.config w projekcie DataLoader i zmień elementy appSettings TargetSearchServiceName i TargetSearchServiceApiKey odpowiednio do używanej usługi Azure Search i używanego klucza interfejsu API usługi Azure Search.  Są one dostępne w witrynie Azure Portal.

1. Naciśnij klawisz F5, aby uruchomić aplikację.  Spowoduje to utworzenie 2 indeksów i zaimportowanie danych przykładu NYCJob.

1. W przykładowym kodzie przykład otwórz plik rozwiązania AutocompleteTutorial.sln w programie Visual Studio.  Otwórz plik Web.config w projekcie AutocompleteTutorial i zmień wartości SearchServiceName i SearchServiceApiKey na takie same jak powyżej.

### <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

Teraz można przystąpić do uruchomienia przykładu przykładowej aplikacji.  Otwórz plik rozwiązania AutocompleteTutorial.sln w programie Visual Studio, aby uruchomić przykład.  Rozwiązanie zawiera projekt platformy ASP.NET MVC.  Naciśnij klawisz F5, aby uruchomić projekt i załadować stronę w wybranej przeglądarce.  U góry strony zobaczysz opcję wyboru języka C# lub JavaScript.  C# Opcja wywoła HomeController w przeglądarce i korzysta z zestawu .NET SDK usługi Azure Search do pobierania wyników.  Opcja języka JavaScript powoduje wywołanie interfejsu API REST usługi Azure Search bezpośrednio z przeglądarki.  Ta opcja ma zazwyczaj zauważalnie lepszą wydajność, ponieważ eliminuje kontroler z przepływu.  Opcję można wybrać na podstawie potrzeb i preferencji językowych.  Na stronie znajduje się kilka przykładów autouzupełniania, a dla każdego z nich są dostępne wskazówki.  Każdy przykład ma zalecany tekst przykładowy, który możesz wypróbować.  Spróbuj wpisać kilka liter w każdym polu wyszukiwania, aby zobaczyć, co się dzieje.

## <a name="how-this-works-in-code"></a>Jak to działa w kodzie

Teraz, po zapoznaniu się z przykładami w przeglądarce, przejdźmy szczegółowo przez pierwszy przykład, aby przejrzeć używane składniki i sposób ich działania.

### <a name="search-box"></a>Pole wyszukiwania

Dla obu opcji języka pole wyszukiwania jest dokładnie takie samo.  Otwórz plik Index.cshtml w folderze \Views\Home. Samo pole wyszukiwania jest proste:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

To pole prosty tekst wejściowy, za pomocą klasy do określania stylu, identyfikator, aby odwoływać się do JavaScript i tekst zastępczy.  Cała „magia” znajduje się w kodzie JavaScript.

### <a name="javascript-code-c"></a>Kod JavaScript (C#)

Przykładowy kod C# używa języka JavaScript w pliku Index.cshtml w celu wykorzystania biblioteki autouzupełniania jQuery UI.  Ta biblioteka dodaje obsługę autouzupełniania do pola wyszukiwania realizowaną za pomocą asynchronicznych wywołań kontrolera MVC pobierających rekomendacje.  Przyjrzyjmy się kodowi JavaScript w pierwszym przykładzie:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Ten kod jest uruchamiany w przeglądarce przy ładowaniu strony w celu skonfigurowania autouzupełniania dla pola wejściowego „example1a”.  Parametr `minLength: 3` zapewnia, że rekomendacje będą wyświetlane tylko po wpisaniu co najmniej trzech znaków w polu wyszukiwania.  Ważna jest wartość źródłowa:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Ten wiersz informuje interfejs API autouzupełniania o tym, skąd ma pobrać listę elementów do wyświetlenia w polu wyszukiwania.  Ponieważ jest to projekt MVC, wywołuje on funkcję Suggest w pliku HomeController.cs.  Popatrzymy na to bardziej szczegółowo w następnej sekcji.  Przekazuje on także kilka parametrów kontrolujących wyróżnianie, dopasowywanie rozmyte i parametr term.  Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozszerzanie przykładu pod kątem obsługi dopasowywania rozmytego

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania.  Wypróbujmy to przez zmianę wiersza kodu źródłowego w celu włączenia dopasowywania rozmytego.

Zmień następujący wiersz z takiego:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

na taki:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Uruchom aplikację, naciskając klawisz F5.

Spróbuj wpisać tekst w rodzaju „execative” i zwróć uwagę, jak zwracane wyniki dotyczą wyrazu „executive”, mimo że nie pasują dokładnie do wpisanych liter.

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Teraz, gdy przejrzeliśmy kod JavaScript przykładu, popatrzmy na kod C# kontrolera, który faktycznie pobiera rekomendacje przy użyciu zestawu .NET SDK usługi Azure Search.

1. Otwórz plik HomeController.cs w katalogu Controllers. 

1. Pierwszą rzeczą, którą możesz zauważyć, jest metoda na początku klasy o nazwie InitSearch.  Umożliwia ona utworzenie uwierzytelnianego klienta HTTP indeksu dla usługi Azure Search.  Jeśli chcesz dowiedzieć się więcej o tym, jak to działa, można znaleźć w następującym przykładzie: [Jak używać usługi Azure Search z poziomu aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Przejdź do funkcji Suggest.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkcja Suggest przyjmuje dwa parametry, które określają, czy są zwracane wyróżnienia trafień oraz czy oprócz wprowadzonego terminu wyszukiwania jest stosowane dopasowywanie rozmyte.  Metoda tworzy obiekt SuggestParameters, który jest następnie przekazywany do interfejsu API funkcji Suggest. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.
(Opcjonalnie) Dodaj punkt przerwania na początku funkcji Suggest i przejdź krokami przez kod.  Zwróć uwagę na odpowiedź zwróconą przez zestaw SDK i sposób jej konwertowania na wynik zwracany z metody.

Inne przykłady na stronie korzystają z tego samego wzorca na potrzeby dodania wyróżniania trafień, uzupełniania przy wpisywaniu dla rekomendacji autouzupełniania i aspektów obsługujących buforowanie wyników autouzupełniania po stronie klienta.  Przejrzyj każdy z nich, aby zrozumieć, jak działają i jak można je wykorzystać w środowisku wyszukiwania.

### <a name="javascript-language-example"></a>Przykład dla języka JavaScript

W przypadku przykładu w języku JavaScript kod JavaScript na stronie IndexJavaScript.cshtml korzysta z funkcji autouzupełniania biblioteki jQuery UI.  Jest to biblioteka, która wykonuje większość skomplikowanych zadań związanych z prezentacją ładnego pola wyszukiwania i ułatwia wykonywanie wywołań asynchronicznych do usługi Azure Search w celu pobrania rekomendacji.  Przyjrzyjmy się kodowi JavaScript w pierwszym przykładzie:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Jeśli porównasz go z przykładem powyżej wywołującym kontroler Home, zauważysz kilka podobieństw.  Konfiguracja autouzupełniania dla elementów `minLength` i `position` jest dokładnie taka sama.  Istotna zmiana w tym przypadku to kod źródłowy.  Zamiast wywoływać metodę sugerowanej na głównym kontrolerze, żądania REST jest tworzony w funkcji języka JavaScript i wykonywane za pomocą interfejsu Ajax.  Odpowiedź jest następnie przetwarzana w sekcji „success” i używana jako źródło.

## <a name="takeaways"></a>Wnioski

W tym przykładzie przedstawiono podstawowe kroki tworzenia pole wyszukiwania, które obsługuje Autouzupełnianie i sugestie.  Pokazano, jak można zbudować aplikację ASP.NET MVC i pobrać sugestie za pomocą zestawu .NET SDK usługi Azure Search lub interfejsu API REST.

## <a name="next-steps"></a>Kolejne kroki

Integrowanie sugestii i autouzupełniania w środowisku wyszukiwania.  Należy wziąć pod uwagę, jak przy użyciu zestawu .NET SDK lub interfejsu API REST bezpośrednio może pomóc korzystaj z zaawansowanych możliwości usługi Azure Search dla użytkowników, podczas ich wpisywania, aby stały się bardziej produktywne.

> [!div class="nextstepaction"]
> [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Interfejs API REST sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atrybut indeksu aspektów dotyczący interfejsu API REST tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

