---
title: Tworzenie aplikacji wyszukiwania w witrynie Azure portal
titleSuffix: Azure Cognitive Search
description: Uruchom Kreatora tworzenia aplikacji (podglądu), aby wygenerować strony HTML i skrypt dla operacyjnej aplikacji sieci Web. Strona zawiera pasek wyszukiwania, obszar wyników, pasek boczny i obsługę maszyn.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369709"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Szybki start: tworzenie aplikacji wyszukiwania w portalu (Azure Cognitive Search)

Użyj kreatora **tworzenia aplikacji wyszukiwania** portalu do generowania aplikacji sieci web w stylu "localhost" do pobrania, która działa w przeglądarce. W zależności od konfiguracji wygenerowana aplikacja działa przy pierwszym użyciu, z połączeniem na żywo z indeksem zdalnym. Domyślna aplikacja może zawierać pasek wyszukiwania, obszar wyników, filtry paska bocznego i obsługę maszyn.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

Uaktualnij do [najnowszej wersji przeglądarki Microsoft Edge](https://www.microsoft.com/edge) lub użyj przeglądarki Chrome Google do tego szybkiego startu.

[Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu. 

[Utwórz indeks](search-create-index-portal.md) do użycia jako podstawa aplikacji. 

Ten przewodnik Szybki start używa wbudowanych przykładowych danych i indeksu nieruchomości, ponieważ zawiera obrazy miniatur (kreator obsługuje dodawanie obrazów do strony wyników). Aby utworzyć indeks używany w tym ćwiczeniu, uruchom **Kreatora importu danych,** wybierając źródło danych z przykładowej nieruchomości.To create the index used in this exercise, run the Import data wizard, choosing the *realestate-us-sample* data source.

![strona źródła danych dla przykładowych danych](media/search-create-app-portal/import-data-realestate.png)

Gdy indeks jest gotowy do użycia, przejdź do następnego kroku.

## <a name="start-the-wizard"></a>Uruchamianie kreatora

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i [znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na stronie Przegląd na stronie Przegląd z łączy na środku strony wybierz pozycję **Indeksy**. 

1. Wybierz *realestate-us-sample-index* z listy istniejących indeksów.

1. Na stronie indeksu u góry wybierz pozycję **Utwórz aplikację wyszukiwania (podgląd),** aby uruchomić kreatora.

1. Na pierwszej stronie kreatora wybierz pozycję **Włącz udostępnianie zasobów między źródłami (CORS),** aby dodać obsługę cors do definicji indeksu. Ten krok jest opcjonalny, ale lokalna aplikacja sieci web nie połączy się z indeksem zdalnym bez niego.

## <a name="configure-search-results"></a>Konfigurowanie wyników wyszukiwania

Kreator udostępnia podstawowy układ renderowanych wyników wyszukiwania, który zawiera miejsce na miniaturę obrazu, tytuł i opis. Tworzenie kopii zapasowych każdego z tych elementów jest pole w indeksie, który udostępnia dane. 

1. W obszarze Miniatura wybierz pole *miniatury* w indeksie *realestate-us-sample.* W tym przykładzie znajdują się miniatury obrazów w postaci obrazów adresowanych adresami URL przechowywanych w polu o nazwie *miniatura*. Jeśli indeks nie zawiera obrazów, pozostaw to pole puste.

1. W polu Tytuł wybierz pole, które przekazuje unikatowość każdego dokumentu. W tym przykładzie identyfikator listy jest rozsądnym wyborem.

1. W opisie wybierz pole, które zawiera szczegółowe informacje, które mogą pomóc komuś zdecydować, czy kliknąć do danego dokumentu.

![strona źródła danych dla przykładowych danych](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Dodawanie paska bocznego

Usługa wyszukiwania obsługuje nawigacji aspektowej, która jest często renderowana jako pasek boczny. Aspekty są oparte na polach, które można filtrować i aspektowalne, wyrażone w schemacie indeksu.

W usłudze Azure Cognitive Search nawigacja fasetowana jest skumulowanym doświadczeniem filtrowania. W obrębie kategorii wybranie wielu filtrów powoduje rozszerzenie wyników (na przykład wybranie Seattle i Bellevue w mieście). W różnych kategoriach wybranie wielu filtrów zawęża wyniki.

> [!TIP]
> Pełny schemat indeksu można wyświetlić w portalu. Poszukaj łącza **Definicja indeksu (JSON)** na stronie przeglądu każdego indeksu. Pola, które kwalifikują się do nawigacji aspektowej mają atrybuty "filtrowalne: prawda" i "facetable: true".

Zaakceptuj bieżący wybór aspektów i przejdź do następnej strony.


## <a name="add-typeahead"></a>Dodaj typo-czoło

Funkcja typeahead jest dostępna w postaci autouzupełniania i sugestii zapytań. Kreator obsługuje sugestie kwerend. Na podstawie danych wejściowych naciśnięcia klawiszy dostarczonych przez użytkownika, usługa wyszukiwania zwraca listę "ukończonych" ciągów zapytań, które można wybrać jako dane wejściowe.

Sugestie są włączone w określonych definicjach pól. Kreator udostępnia opcje konfigurowania ilości informacji zawartych w sugestii. 

Poniższy zrzut ekranu przedstawia opcje kreatora zestawione z renderowanymi stronicowania w aplikacji. Możesz zobaczyć, jak używane są wybory pól i jak "Pokaż nazwę pola" jest używany do uwzględnienia lub wykluczenia etykietowania w sugestii.

![Konfiguracja sugestii kwerendy](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Tworzenie, pobieranie i wykonywanie

1. Wybierz **pozycję Utwórz aplikację wyszukiwania,** aby wygenerować plik HTML.

1. Po wyświetleniu monitu wybierz pozycję **Pobierz aplikację,** aby pobrać plik.

1. Otwórz ten plik. Powinna zostać wyświetlona strona podobna do poniższego zrzutu ekranu. Wprowadź termin i użyj filtrów, aby zawęzić wyniki. 

Podstawowy indeks składa się z fikcyjnych, wygenerowanych danych, które zostały zduplikowane w dokumentach, a opisy czasami nie pasują do obrazu. Możesz oczekiwać bardziej spójne środowisko podczas tworzenia aplikacji na podstawie własnych indeksów.

![Uruchomienie aplikacji](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

Podczas gdy domyślna aplikacja jest przydatna do wstępnej eksploracji i małych zadań, wczesne przeglądanie interfejsów API pomoże Ci zrozumieć pojęcia i przepływ pracy na głębszym poziomie:

> [!div class="nextstepaction"]
> [Tworzenie indeksu przy użyciu zestawu .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)