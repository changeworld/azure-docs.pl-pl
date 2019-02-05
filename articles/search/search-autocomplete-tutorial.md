---
title: Samouczek dotyczący dodawania autouzupełniania do pola wyszukiwania — Azure Search
description: Przykłady ulepszania środowiska użytkownika końcowego w aplikacjach opartych na danych przy użyciu funkcji autouzupełniania usługi Azure Search i interfejsów API sugestii.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: a0b4301177fa1307244a784031ec890b9a20f01a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099112"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Samouczek: Dodawanie autouzupełniania do pola wyszukiwania przy użyciu usługi Azure Search

Ten samouczek przedstawia sposób użycia [sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions), [funkcji autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) oraz [aspektów](search-faceted-navigation.md) dostępnych za pomocą [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/) i [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) do utworzenia pola wyszukiwania o zaawansowanych możliwościach. *Sugestie* udostępniają rekomendowane rzeczywiste wyniki na podstawie tekstu wpisanego przez użytkownika w danym momencie. *Autouzupełnianie*, [nowa funkcja dostępna w wersji zapoznawczej](search-api-preview.md) w usłudze Azure Search, udostępnia terminy z indeksu na potrzeby uzupełniania tekstu aktualnie wpisywanego przez użytkownika. Porównamy wiele technik mających na celu zwiększenie efektywności użytkowników oraz umożliwienie szybkiego i łatwego znajdowania wyszukiwanych treści przez udostępnienie użytkownikowi bogatych możliwości wyszukiwania bezpośrednio podczas wpisywania.

Ten samouczek przeprowadzi Cię przez aplikację opartą na platformie ASP.NET MVC, która używa języka C# do wywoływania [bibliotek klienta .NET usługi Azure Search](https://aka.ms/search-sdk) oraz języka JavaScript do bezpośredniego wywoływania interfejsu API REST usługi Azure Search. Aplikacja w tym samouczku posługuje się indeksem wypełnionym za pomocą danych przykładu [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Istnieje możliwość użycia indeksu w wersji już skonfigurowanej na potrzeby pokazu NYC Jobs lub wypełnienia własnego indeksu za pomocą modułu ładującego dane w przykładowym rozwiązaniu NYCJobs. W przykładzie użyto bibliotek JavaScript [jQuery UI](https://jqueryui.com/autocomplete/) i [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) do utworzenia pola wyszukiwania obsługującego autouzupełnianie. Używając tych składników i usługi Azure Search, zapoznasz się z wieloma przykładami obsługi autouzupełniania z uzupełnianiem przy wpisywaniu w polu wyszukiwania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Pobieranie i konfigurowanie rozwiązania
> * Dodawanie informacji o usłudze wyszukiwania do ustawień aplikacji
> * Implementowanie pola wejściowego wyszukiwania
> * Dodawanie obsługi listy autouzupełniania pobierającej dane ze źródła zdalnego 
> * Dodawanie obsługi pobierania sugestii i danych autouzupełniania za pomocą zestawu .NET SDK i interfejsu API REST
> * Dodawanie obsługi buforowania po stronie klienta w celu poprawy wydajności 

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2017. Możesz korzystać z bezpłatnego programu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Pobierz [kod źródłowy](https://github.com/azure-samples/search-dotnet-getting-started) przykładu dla tego samouczka.

* (Opcjonalnie) Aktywne konto platformy Azure i usługa Azure Search. Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/). Aby uzyskać pomoc dotyczącą aprowizowania usługi, zobacz [Tworzenie usługi wyszukiwania](search-create-service-portal.md). Konto i usługa są opcjonalne, ponieważ ten samouczek można ukończyć za pomocą hostowanego indeksu NYCJobs już dostępnego na potrzeby innego pokazu.

* (Opcjonalnie) Pobierz kod przykładu [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs), aby zaimportować dane NYCJobs do indeksu w ramach własnej usługi Azure Search.

> [!Note]
> Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów. Moduł ładujący dane NYCJobs tworzy dwa indeksy. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych indeksów.

### <a name="set-up-azure-search-optional"></a>Konfigurowanie usługi Azure Search (opcjonalnie)

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zaimportować dane przykładowej aplikacji NYCJobs do własnego indeksu. Ten krok jest opcjonalny.  Jeśli chcesz użyć udostępnionego przykładowego indeksu, przejdź do następnej sekcji, uruchamiając przykład.

1. W folderze DataLoader kodu przykładu NYCJobs otwórz plik rozwiązania DataLoader.sln w programie Visual Studio.

1. Zaktualizuj informacje o połączeniu dla usługi Azure Search.  Otwórz plik App.config w projekcie DataLoader i zmień elementy appSettings TargetSearchServiceName i TargetSearchServiceApiKey odpowiednio do używanej usługi Azure Search i używanego klucza interfejsu API usługi Azure Search.  Są one dostępne w witrynie Azure Portal.

1. Naciśnij klawisz F5, aby uruchomić aplikację.  Spowoduje to utworzenie 2 indeksów i zaimportowanie danych przykładu NYCJob.

1. Otwórz plik rozwiązania AutocompleteTutorial.sln należący do kodu przykładu dla samouczka w programie Visual Studio.  Otwórz plik Web.config w projekcie AutocompleteTutorial i zmień wartości SearchServiceName i SearchServiceApiKey na takie same jak powyżej.

### <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

Teraz możesz uruchomić przykładową aplikację przeznaczoną dla samouczka.  Otwórz plik rozwiązania AutocompleteTutorial.sln w programie Visual Studio, aby uruchomić samouczek.  Rozwiązanie zawiera projekt platformy ASP.NET MVC.  Naciśnij klawisz F5, aby uruchomić projekt i załadować stronę w wybranej przeglądarce.  U góry strony zobaczysz opcję wyboru języka C# lub JavaScript.  Opcja języka C# powoduje wywołanie elementu HomeController z przeglądarki i korzysta z zestawu .Net SDK usługi Azure Search do pobrania wyników.  Opcja języka JavaScript powoduje wywołanie interfejsu API REST usługi Azure Search bezpośrednio z przeglądarki.  Ta opcja ma zazwyczaj zauważalnie lepszą wydajność, ponieważ eliminuje kontroler z przepływu.  Opcję można wybrać na podstawie potrzeb i preferencji językowych.  Na stronie znajduje się kilka przykładów autouzupełniania, a dla każdego z nich są dostępne wskazówki.  Każdy przykład ma zalecany tekst przykładowy, który możesz wypróbować.  Spróbuj wpisać kilka liter w każdym polu wyszukiwania, aby zobaczyć, co się dzieje.

## <a name="how-this-works-in-code"></a>Jak to działa w kodzie

Teraz, po zapoznaniu się z przykładami w przeglądarce, przejdźmy szczegółowo przez pierwszy przykład, aby przejrzeć używane składniki i sposób ich działania.

### <a name="search-box"></a>Pole wyszukiwania

Dla obu opcji języka pole wyszukiwania jest dokładnie takie samo.  Otwórz plik Index.cshtml w folderze \Views\Home. Samo pole wyszukiwania jest proste:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Jest to proste wejściowe pole tekstowe z klasą określającą styl, identyfikatorem na potrzeby przywoływania z kodu JavaScript i tekstem zastępczym.  Cała „magia” znajduje się w kodzie JavaScript.

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

1. Pierwszą rzeczą, którą możesz zauważyć, jest metoda na początku klasy o nazwie InitSearch.  Umożliwia ona utworzenie uwierzytelnianego klienta HTTP indeksu dla usługi Azure Search.  Jeśli chcesz dowiedzieć się więcej o tym, jak to działa, odwiedź następujący samouczek: [Jak używać usługi Azure Search z poziomu aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

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

Inne przykłady na stronie korzystają z tego samego wzorca na potrzeby dodania wyróżniania trafień, uzupełniania przy wpisywaniu dla rekomendacji autouzupełniania i aspektów obsługujących buforowanie po stronie klienta wyników autouzupełniania.  Przejrzyj każdy z nich, aby zrozumieć, jak działają i jak można je wykorzystać w środowisku wyszukiwania.

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

Jeśli porównasz go z przykładem powyżej wywołującym kontroler Home, zauważysz kilka podobieństw.  Konfiguracja autouzupełniania dla elementów `minLength` i `position` jest dokładnie taka sama.  Istotna zmiana w tym przypadku to kod źródłowy.  Zamiast wywoływać metodę Suggest w kontrolerze strony głównej, w funkcji JavaScript jest tworzone żądanie REST wykonywane przy użyciu technologii AJAX.  Odpowiedź jest następnie przetwarzana w sekcji „success” i używana jako źródło.

## <a name="takeaways"></a>Wnioski

Ten samouczek przedstawia podstawowe kroki tworzenia pola wyszukiwania, które obsługuje autouzupełnianie i sugestie.  Przedstawiono tworzenie aplikacji ASP.NET MVC i użycie zestawu .Net SDK usługi Azure Search lub interfejsu API REST do pobrania sugestii.

## <a name="next-steps"></a>Następne kroki

Integrowanie sugestii i autouzupełniania w środowisku wyszukiwania.  Zastanów się nad tym, jak użycie zestawu .Net SDK lub bezpośrednio interfejsu API REST może ułatwić udostępnienie możliwości usługi Azure Search użytkownikom podczas wpisywania, zwiększając ich efektywność.

> [!div class="nextstepaction"]
> [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Interfejs API REST sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atrybut indeksu aspektów dotyczący interfejsu API REST tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

