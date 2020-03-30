---
title: 'Szybki start: tworzenie zestawu umiejętności w witrynie Azure portal'
titleSuffix: Azure Cognitive Search
description: W tym portalu Szybki start dowiedz się, jak użyć Kreatora importu danych, aby dodać umiejętności poznawcze do potoku indeksowania w usłudze Azure Cognitive Search. Umiejętności obejmują optyczne rozpoznawanie znaków (OCR) i przetwarzanie języka naturalnego.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472421"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Szybki start: tworzenie zestawu umiejętności poznawczych usługi Azure Cognitive Search w witrynie Azure portal

Zestaw umiejętności to funkcja sztucznej inteligencji, która wyodrębnia informacje i strukturę z dużych niezróżnionych plików tekstowych lub obrazów i umożliwia indeksowanie i wyszukiwanie zapytań pełnotekstowych w usłudze Azure Cognitive Search. 

W tym przewodniku Szybki start połączysz usługi i dane w chmurze platformy Azure, aby utworzyć zestaw umiejętności. Gdy wszystko będzie na swoim miejscu, uruchomisz **Kreatora importu danych** w portalu, aby pociągnąć to wszystko razem. Wynikiem końcowym jest przeszukiwalny indeks wypełniony danymi utworzonymi przez przetwarzanie AI, które można przeszukiwać w portalu ([Eksplorator wyszukiwania](search-explorer.md)).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki start korzysta z usługi Azure Cognitive Search, [usługi Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/)i usługi Azure Cognitive [Services](https://azure.microsoft.com/services/cognitive-services/) dla sztucznej inteligencji. 

Ponieważ obciążenie jest tak małe, usługi Cognitive Services są wykorzystywane w tle, aby zapewnić bezpłatne przetwarzanie dla maksymalnie 20 transakcji. W przypadku takiego małego zestawu danych można pominąć tworzenie lub dołączanie zasobu usług Cognitive Services.

1. [Pobierz przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) składające się z małego zestawu plików różnych typów. Rozpaj pliki.

1. [Utwórz konto magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub znajdź istniejące [konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Wybierz ten sam region co usługa Azure Cognitive Search, aby uniknąć opłat za przepustowość. 
   
   Wybierz typ konta StorageV2 (ogólnego przeznaczenia V2), jeśli chcesz wypróbować funkcję magazynu wiedzy później, w innym instruktażu. W przeciwnym razie wybierz dowolny typ.

1. Otwórz strony usług obiektów blob i utwórz kontener. Można użyć domyślnego poziomu dostępu publicznego. 

1. W kontenerze kliknij pozycję **Przekaż,** aby przekazać przykładowe pliki pobrane w pierwszym kroku. Należy zauważyć, że masz szeroki zakres typów zawartości, w tym obrazów i plików aplikacji, które nie są przeszukiwane w pełnym tekście w ich formatach natywnych.

   ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

Teraz można przystąpić do pracy w Kreatorze importu danych.

## <a name="run-the-import-data-wizard"></a>Uruchamianie kreatora importu danych

Na stronie Przegląd usługi wyszukiwania kliknij pozycję **Importuj dane** na pasku poleceń, aby skonfigurować wzbogacenie poznawcze w czterech krokach.

  ![Polecenie importu danych](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>Krok 1 - Tworzenie źródła danych

1. W **obszarze Połącz z danymi**wybierz pozycję Magazyn obiektów **Blob platformy Azure**wybierz konto magazynu i utworzony kontener. Podaj nazwę źródła danych i użyj wartości domyślnych dla pozostałych ustawień. 

   ![Konfiguracja obiektu blob platformy Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Przejdź do następnej strony.

### <a name="step-2---add-cognitive-skills"></a>Krok 2 - Dodaj umiejętności poznawcze

Następnie skonfiguruj wzbogacanie ai, aby wywołać OCR, analizę obrazu i przetwarzanie języka naturalnego. 

1. W tym przewodniku Szybki start używamy zasobu **bezpłatne** usługi Cognitive Services. Przykładowe dane składają się z 14 plików, więc bezpłatny przydział 20 transakcji w usługach Cognitive Services jest wystarczający do tego szybkiego startu. 

   ![Dołączanie usług Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Rozwiń **Dodaj wzbogacenia** i dokonaj czterech wyborów. 

   Włącz funkcję OCR, aby dodać umiejętności analizy obrazu do strony kreatora.

   Ustaw ziarnistość na strony, aby podzielić tekst na mniejsze fragmenty. Kilka umiejętności tekstowych są ograniczone do 5 KB wejść.

   Wybierz rozpoznawanie jednostek (osoby, organizacje, lokalizacje) i umiejętności analizy obrazu.

   ![Dołączanie usług Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Przejdź do następnej strony.

### <a name="step-3---configure-the-index"></a>Krok 3 - Konfigurowanie indeksu

Indeks zawiera zawartość z wyszukujną, a Kreator **importu danych** zwykle może utworzyć schemat, pobierając próbki ze źródła danych. W tym kroku przejrzyj wygenerowany schemat i potencjalnie popraw wszystkie ustawienia. Poniżej znajduje się domyślny schemat utworzony dla zestawu danych zestawu danych obiektów blob demo.

W przypadku tego przewodnika Szybki start kreator wykonuje dobrą pracę, ustawiając rozsądne wartości domyślne:  

+ Pola domyślne są oparte na właściwościach istniejących obiektów blob oraz `people`nowych `organizations` `locations`polach zawierających dane wyjściowe wzbogacania (na przykład , , ). Typy danych są wywnioskowane z metadanych i próbkowania danych.

+ Domyślny klucz dokumentu jest *metadata_storage_path* (zaznaczony, ponieważ pole zawiera unikatowe wartości).

+ Domyślne atrybuty to **Pobieranie** i **przeszukiwanie**. **Przeszukiwanie** umożliwia wyszukiwanie pełnotekstowe w polu. **Można pobrać** oznacza, że wartości pól mogą być zwracane w wynikach. W kreatorze przyjęto założenie, że chcesz, aby te pola oferowały możliwości pobierania i wyszukiwania, ponieważ zostały utworzone za pośrednictwem zestawu umiejętności.

  ![Pola indeksu](media/cognitive-search-quickstart-blob/index-fields.png)

Zwróć uwagę na przekreślenie i znak zapytania w atrybucie **Możliwość pobierania** obok pola `content`. W przypadku dokumentów obiektów blob z dużą ilością tekstu pole `content` zawiera większą część pliku, która może potencjalnie składać się z tysięcy wierszy. Takie pole jest nieporęczne w wynikach wyszukiwania i należy je wykluczyć dla tego demo. 

Jeśli jednak musisz przekazać zawartość pliku do kodu klienta, upewnij się, że **opcja Pobierania pozostaje** zaznaczona. W przeciwnym razie należy rozważyć `content` wyczyszczenie tego `people` `organizations`atrybutu, jeśli wyodrębnione elementy (takie jak , `locations`, i tak dalej) są wystarczające.

Oznaczenie pola jako **Możliwość pobierania** nie oznacza, że pole *musi* znajdować się w wynikach wyszukiwania. Można precyzyjnie kontrolować wyniki wyszukiwania za pomocą parametru zapytania **$select** w celu wybrania pól do uwzględnienia. W przypadku pól zawierających dużo tekstu, takich jak `content`, parametr **$select** to rozwiązanie, które oferuje użytkownikom aplikacji łatwe w zarządzaniu wyniki wyszukiwania, gwarantując jednocześnie, że kod klienta ma dostęp do wszystkich wymaganych informacji za pośrednictwem atrybutu **Możliwość pobierania**.
  
Przejdź do następnej strony.

### <a name="step-4---configure-the-indexer"></a>Krok 4 - Konfigurowanie indeksatora

Indeksator jest procesem wysokiego poziomu sterującym procesem indeksowania. Określa on nazwę źródła danych, docelowy indeks oraz częstotliwość wykonywania. Kreator **importu danych** tworzy kilka obiektów, a z nich jest zawsze indeksatorem, który można uruchamiać wielokrotnie.

1. Na stronie **Indeksator** można zaakceptować nazwę domyślną i kliknąć opcję **Raz** harmonogram, aby uruchomić ją natychmiast. 

   ![Definicja indeksatora](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kliknij przycisk **Prześlij**, aby utworzyć i od razu uruchomić indeksator.

## <a name="monitor-status"></a>Stan monitora

Indeksowanie umiejętności poznawczych trwa dłużej niż typowe indeksowanie tekstowe, zwłaszcza OCR i analizy obrazu. Aby monitorować postęp, przejdź do strony Przegląd i kliknij pozycję **Indeksatory** na środku strony.

  ![Powiadomienie usługi Azure Cognitive Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Ostrzeżenia są normalne, biorąc pod uwagę szeroki zakres typów zawartości. Niektóre typy zawartości nie są prawidłowe dla niektórych umiejętności i na niższych poziomach, często spotyka się z [limitami indeksatora.](search-limits-quotas-capacity.md#indexer-limits) Na przykład powiadomienia o obcięcie 32 000 znaków są limitem indeksatora w warstwie Bezpłatna. Jeśli uruchomisz to demo na wyższym poziomie, wiele ostrzeżeń o obcięciu zniknie.

Aby sprawdzić ostrzeżenia lub błędy, kliknij stan Ostrzeżenie na liście Indeksatory, aby otworzyć stronę Historia wykonania.

Na tej stronie kliknij ponownie pozycję Stan ostrzeżenia, aby wyświetlić listę ostrzeżeń podobną do pokazanej poniżej. 

  ![Lista ostrzeżeń indeksatora](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

Szczegóły są wyświetlane po kliknięciu określonego wiersza stanu. To ostrzeżenie mówi, że scalanie zatrzymał się po osiągnięciu maksymalnego progu (ten konkretny plik PDF jest duży).

  ![Szczegóły ostrzeżenia](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Zapytanie w Eksploratorze wyszukiwania

Po utworzeniu indeksu można uruchamiać kwerendy w celu zwrócenia wyników. W portalu użyj **Eksploratora wyszukiwania** dla tego zadania. 

1. Na stronie pulpitu nawigacyjnego usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać utworzony indeks.

1. Wprowadź ciąg wyszukiwania, aby wykonać zapytanie względem indeksu, np. `search=Microsoft&$select=people,organizations,locations,imageTags`.

Wyniki są zwracane jako JSON, które mogą być pełne i trudne do odczytania, szczególnie w dużych dokumentach pochodzących z obiektów blob platformy Azure. Niektóre wskazówki dotyczące wyszukiwania w tym narzędziu obejmują następujące techniki:

+ Dołącz, `$select` aby określić, które pola mają być uwzględniane w wynikach. 
+ Użyj CTRL-F, aby wyszukać w UwiO określone właściwości lub terminy.

W ciągach zapytań rozróżniana jest wielkość liter, więc jeśli zostanie wyświetlony komunikat "nieznane pole", sprawdź **pola** lub **definicję indeksu (JSON),** aby zweryfikować nazwę i wielkość liter. 

  ![Przykład Eksploratora wyszukiwania](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Wnioski

Teraz utworzono swój pierwszy zestaw umiejętności i nauczyłeś się ważnych pojęć przydatnych do tworzenia prototypów wzbogaconego rozwiązania wyszukiwania przy użyciu własnych danych.

Niektóre kluczowe założenia, które, mamy nadzieję, zostały wychwycone, obejmują zależności od źródeł danych platformy Azure. Zestaw umiejętności jest powiązany z indeksatorem, a indeksatory są azure i specyficzne dla źródła. Mimo że w tym przewodniku Szybki start jest używana usługa Azure Blob Storage, możliwe są inne źródła danych platformy Azure. Aby uzyskać więcej informacji, zobacz [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md). 

Inną ważną koncepcją jest to, że umiejętności działają nad typami zawartości, a podczas pracy z niejednorodną zawartością niektóre dane wejściowe zostaną pominięte. Ponadto duże pliki lub pola mogą przekraczać limity indeksatora warstwy usług. To normalne, aby zobaczyć ostrzeżenia, gdy wystąpią te zdarzenia. 

Dane wyjściowe są kierowane do indeksu wyszukiwania i istnieje mapowanie między parami nazwa-wartość utworzone podczas indeksowania i poszczególnych pól w indeksie. Wewnętrznie portal konfiguruje [adnotacje](cognitive-search-concept-annotations-syntax.md) i definiuje [zestaw umiejętności](cognitive-search-defining-skillset.md), ustanawiając kolejność operacji i ogólny przepływ. Te kroki są ukryte w portalu, ale po rozpoczęciu pisania kodu te koncepcje stają się istotne.

Na koniec dowiedziałeś się, że można zweryfikować zawartość, korzystając z kwerendy indeksu. Ostatecznie to, co zapewnia usługa Azure Cognitive Search, to indeks z możliwością wyszukiwania, który można wyszukiwać przy użyciu [prostej](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) lub [w pełni rozszerzonej składni kwerendy.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) Indeks zawierający wzbogacone pola jest taki sam, jak każdy inny. Jeśli chcesz włączyć standardowe lub [niestandardowe analizatory,](search-analyzers.md) [profile oceniania,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [synonimy,](search-synonyms.md) [filtry fasetowane,](search-filters-facets.md)wyszukiwanie geograficzne lub dowolną inną funkcję usługi Azure Cognitive Search, z pewnością możesz to zrobić.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

Zestawy umiejętności można tworzyć za pomocą portalu, interfejsu SDK .NET lub interfejsu API REST. Aby kontynuować swoją wiedzę, spróbuj interfejsu API REST przy użyciu listonosza i więcej przykładowych danych.

> [!div class="nextstepaction"]
> [Samouczek: Wyodrębnianie tekstu i struktury z obiektów blob JSON przy użyciu interfejsów API REST](cognitive-search-tutorial-blob.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub wypróbować inny przewodnik wzbogacania ai, usuń indeksatora w portalu. Usunięcie indeksatora resetuje licznik transakcji dziennego z powrotem do zera dla przetwarzania usług Cognitive Services.