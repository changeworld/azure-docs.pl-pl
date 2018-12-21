---
title: 'Szybki start: potok wyszukiwania poznawczego w witrynie Azure Portal – Azure Search'
description: Przykład umiejętności wyodrębniania danych, przetwarzania języka naturalnego i przetwarzania obrazów w witrynie Azure Portal z wykorzystaniem przykładowych danych.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7d579bfdaf38b6c06b26cfa7b36f8e4d2ac5a1f2
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386268"
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>Przewodnik Szybki start: tworzenie potoku wyszukiwania poznawczego przy użyciu umiejętności i przykładowych danych

Wyszukiwanie poznawcze (wersja zapoznawcza) dodaje umiejętności wyodrębniania danych, przetwarzania języka naturalnego i przetwarzania obrazów do potoku indeksowania usługi Azure Search, co ułatwia wyszukiwanie zawartości nieobsługującej wyszukiwania lub pozbawionej struktury. Informacje utworzone za pomocą umiejętności, takiej jak rozpoznawanie jednostek lub analiza obrazu, zostają dodane do indeksu w usłudze Azure Search.

W tym przewodniku Szybki start wypróbujesz potok wzbogacania w witrynie [Azure Portal](https://portal.azure.com), zanim napiszesz jakikolwiek wiersz kodu:

* Rozpocznij z przykładowymi danymi w usłudze Azure Blob Storage.
* Skonfiguruj [Kreatora importu danych](search-import-data-portal.md) na potrzeby indeksowania i wzbogacania. 
* Uruchom kreatora (umiejętność rozpoznawania jednostek wykrywa osoby, lokalizacje i organizacje).
* Użyj [Eksploratora wyszukiwania](search-explorer.md), aby wykonywać zapytania o wzbogacone dane.

## <a name="supported-regions"></a> Obsługiwane regiony

Wyszukiwanie poznawcze możesz wypróbować w usłudze Azure Search utworzonej w następujących regionach:

* Środkowo-zachodnie stany USA
* Środkowo-południowe stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA 2
* Kanada Środkowa
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Europa Północna
* Brazylia Południowa
* Azja Południowo-Wschodnia
* Indie Środkowe
* Australia Wschodnia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Od 21 grudnia 2018 roku będziesz mieć możliwość skojarzenia swoich zasobów w usługach Cognitive Services z zestawem umiejętności usługi Azure Search. Rozpoczniemy wówczas naliczanie opłat za wykonywanie zestawu umiejętności. Od tego dnia zaczniemy też naliczać opłaty za wyodrębnianie obrazów w ramach etapu analizowania dokumentów. Wyodrębnianie tekstu z dokumentów nadal będzie oferowane bez dodatkowych opłat.
>
> Opłaty za wykonywanie wbudowanych umiejętności będą naliczane na podstawie istniejącej [ceny przy płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/details/cognitive-services/) za usługi Cognitive Services. Opłaty za wyodrębnianie obrazów będą naliczane zgodnie z cenami w wersji zapoznawczej. Opisano to [na stronie z cennikiem usługi Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Dowiedz się [więcej](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Wymagania wstępne

Artykuł [„Co to jest wyszukiwanie poznawcze?”](cognitive-search-concept-intro.md) zawiera wprowadzenie do architektury i składników wzbogacania. 

Usługi platformy Azure są używane wyłącznie w tym scenariuszu. Tworzenie potrzebnych usług jest częścią przygotowania.

+ Usługa Azure Blob Storage udostępnia dane źródłowe.
+ Usługa Azure Search obsługuje pozyskiwanie danych i indeksowanie, wzbogacanie wyszukiwania poznawczego oraz zapytania z użyciem wyszukiwania pełnotekstowego.

### <a name="set-up-azure-search"></a>Konfigurowanie usługi Azure Search

Najpierw utwórz konto usługi Azure Search. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Kliknij pozycję **Utwórz zasób**, wyszukaj usługę Azure Search i kliknij pozycję **Utwórz**. Zobacz [Tworzenie usługi Azure Search w portalu](search-create-service-portal.md), jeśli konfigurujesz usługę wyszukiwania po raz pierwszy i potrzebujesz dodatkowej pomocy.

  ![Pulpit nawigacyjny portalu](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Tworzenie usługi Azure Search w portalu")

1. W obszarze Grupa zasobów utwórz grupę zasobów, która będzie zawierała wszystkie zasoby utworzone w tym przewodniku Szybki start. Ułatwi to wyczyszczenie zasobów po ukończeniu tego przewodnika Szybki start.

1. Jako lokalizację wybierz jeden z [obsługiwanych regionów](#supported-regions) dla usługi Cognitive Search.

1. W obszarze Warstwa cenowa możesz utworzyć usługę w warstwie **Bezpłatna**, aby ukończyć samouczki i przewodniki Szybki start. Na potrzeby głębszej analizy z wykorzystaniem własnych danych możesz utworzyć [płatną usługę](https://azure.microsoft.com/pricing/details/search/), taką jak usługa w warstwie **Podstawowa** lub **Standardowa**. 

  Usługa w warstwie Bezpłatna jest ograniczona do 3 indeksów, maksymalnego rozmiaru obiektu blob równego 16 MB i 2 minut indeksowania, co nie wystarcza do korzystania z pełnych możliwości wyszukiwania poznawczego. Aby przejrzeć limity dla różnych warstw, zobacz [ograniczenia usługi](search-limits-quotas-capacity.md).

  ![Strona definicji usługi w portalu](./media/cognitive-search-tutorial-blob/create-search-service1.png "Strona definicji usługi w portalu")
  ![Strona definicji usługi w portalu](./media/cognitive-search-tutorial-blob/create-search-service2.png "Strona definicji usługi w portalu")
  > [!NOTE]
  > Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Wykonywanie zestawu umiejętności jest obecnie dostępne we wszystkich warstwach, w tym warstwie Bezpłatna. Bez skojarzenia z płatnym zasobem w usługach Cognitive Services będzie można wykonywać ograniczoną liczbę czynności wzbogacania. Dowiedz się [więcej](cognitive-search-attach-cognitive-services.md).

1. Przypnij usługę do pulpitu nawigacyjnego, aby uzyskać szybki dostęp do informacji o niej.

  ![Strona definicji usługi w portalu](./media/cognitive-search-tutorial-blob/create-search-service3.png "Strona definicji usługi w portalu")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Konfigurowanie usługi Azure Blob Service i ładowanie przykładowych danych

Potok wzbogacania ściąga dane ze źródeł danych platformy Azure obsługiwanych przez [indeksatory usługi Azure Search](search-indexer-overview.md). Należy pamiętać, że usługa Azure Table Storage nie jest obsługiwana w usłudze wyszukiwania poznawczego. Na potrzeby tego ćwiczenia będziemy korzystać z usługi Blob Storage, aby zaprezentować wiele typów zawartości.

1. [Pobierz przykładowe dane](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) składające się z małego zestawu plików różnych typów. 

1. Zarejestruj się w usłudze Azure Blob Storage, utwórz konto magazynu, zaloguj się w Eksploratorze usługi Storage i utwórz kontener. Instrukcje dotyczące wszystkich kroków zawiera [Przewodnik Szybki start dotyczący Eksploratora usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. Korzystając z Eksploratora usługi Azure Storage, w utworzonym kontenerze kliknij pozycję **Przekaż**, aby przekazać przykładowe pliki.

  ![Pliki źródłowe w usłudze Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Tworzenie potoku wzbogacania

Przejdź z powrotem na stronę pulpitu nawigacyjnego usługi Azure Search i na pasku poleceń kliknij pozycję **Importuj dane**, aby skonfigurować wzbogacenie w czterech krokach.

### <a name="step-1-create-a-data-source"></a>Krok 1: Tworzenie źródła danych

W obszarze **Nawiązywanie połączenia z danymi** > **Azure Blob Storage** wybierz konto i utworzony kontener. Podaj nazwę źródła danych i użyj wartości domyślnych dla pozostałych ustawień. 

   ![Konfiguracja obiektu blob platformy Azure](./media/cognitive-search-quickstart-blob/blob-datasource2.png)


Kliknij przycisk **OK**, aby utworzyć źródło danych.

Jedną z zalet korzystania z kreatora **Import danych** jest to, że może on również utworzyć indeks. Podczas tworzenia źródła danych kreator tworzy jednocześnie schemat indeksu. Utworzenie indeksu może potrwać kilka sekund.

### <a name="step-2-add-cognitive-skills"></a>Krok 2: Dodawanie umiejętności poznawczych

Następnie dodaj kroki wzbogacenia do potoku indeksowania. Portal udostępnia wstępnie zdefiniowane umiejętności poznawcze na potrzeby analizy obrazu i analizy tekstu. W portalu zestaw umiejętności pracuje na jednym polu źródła. Może się to wydawać małym celem, ale w przypadku obiektów blob platformy Azure pole `content` zawiera większość dokumentu obiektu blob (na przykład dokument programu Word lub pokaz slajdów programu PowerPoint). Z tego względu to pole jest idealnym polem wejściowym, ponieważ obejmuje całą zawartość obiektu blob.

Czasami może być potrzebne wyodrębnienie tekstowej reprezentacji z plików, które składają się w większości z zeskanowanych obrazów, np. z pliku PDF wygenerowanego przez skaner. Usługa Azure Search może automatycznie wyodrębniać zawartość z obrazów osadzonych w dokumencie. W tym celu wybierz opcję **Włącz optyczne rozpoznawanie znaków (OCR) i scal cały tekst w polu merged_content**. W ten sposób zostanie automatycznie utworzone pole `merged_content` zawierające tekst wyodrębniony z dokumentu oraz tekstową reprezentację obrazów osadzonych w dokumencie. Po wybraniu tej opcji wartość `Source data field` zostanie ustawiona na `merged_content`.

W kroku **Dodawanie umiejętności poznawczych** wybierz umiejętności, które wykonują przetwarzanie języka naturalnego. W ramach tego przewodnika Szybki start wybierz rozpoznawanie jednostek dla osób, organizacji i lokalizacji.

Kliknij przycisk **OK**, aby zaakceptować definicję.
   
  ![Definicja zestawu umiejętności](./media/cognitive-search-quickstart-blob/skillset.png)

Umiejętności przetwarzania języka naturalnego pracują na zawartości tekstowej w przykładowym zestawie danych. Ponieważ nie wybrano żadnych opcji przetwarzania obrazów, pliki JPEG znalezione w przykładowym zestawie danych nie będą przetwarzane w tym przewodniu Szybki start. 

### <a name="step-3-configure-the-index"></a>Krok 3: Konfigurowanie indeksu

Pamiętasz indeks utworzony ze źródłem danych? W tym kroku możesz wyświetlić jego schemat i potencjalnie poprawić dowolne ustawienia. 

W przypadku tego przewodnika Szybki start kreator wykonuje dobrą pracę, ustawiając rozsądne wartości domyślne: 

+ Każdy indeks musi mieć nazwę. W przypadku tego typu źródła danych nazwa domyślna to *azureblob-index*.

+ Każdy dokument musi mieć klucz. Kreator wybiera pola o unikatowych wartościach. W tym przewodniku Szybki start klucz to *metadata_storage_path*.

+ Każda kolekcja pól musi zawierać pola z typem danych opisującym jego wartości, a każde pole powinno mieć atrybuty indeksu, które opisują sposób ich użycia w scenariuszu wyszukiwania. 

Ponieważ zdefiniowano zestaw umiejętności, kreator zakłada, że użytkownik chce mieć pole danych źródłowych oraz dodatkowo pola danych wyjściowych utworzone przez umiejętności. Z tego powodu portal dodaje pola indeksu dla pól `content`, `people`, `organizations` i `locations`. Zwróć uwagę, że kreator automatycznie włącza atrybuty Pobieranie i Wyszukiwanie dla tych pól.

Na stronie **Dostosowywanie indeksu** przejrzyj atrybuty pól, aby sprawdzić, jak są używane w indeksie. Atrybut Wyszukiwanie wskazuje, że pole może być wyszukiwane. Atrybut Pobieranie oznacza, że może ono być zwracane w wynikach. 

Rozważ wyczyszczenie atrybutu Wyszukiwanie dla pola `content`. W obiektach blob w tym polu mogą występować nawet tysiące wierszy, trudnych do odczytu w narzędziu takim jak **Eksplorator wyszukiwania**.

Kliknij przycisk **OK**, aby zaakceptować definicję indeksu.

  ![Pola indeksu](./media/cognitive-search-quickstart-blob/index-fields.png)

> [!NOTE]
> Nieużywane pola zostały obcięte na zrzucie ekranu w celu zachowania zwięzłości. Jeśli pracujesz w portalu, lista zawiera dodatkowe pola.

### <a name="step-4-configure-the-indexer"></a>Krok 4: Konfigurowanie indeksatora

Indeksator jest procesem wysokiego poziomu sterującym procesem indeksowania. Określa on nazwę źródła danych, indeks oraz częstotliwość wykonywania. Wynik końcowy kreatora **Import danych** zawsze jest indeksatorem, który można wywoływać wielokrotnie.

Na stronie **Indeksator** nadaj indeksatorowi nazwę i użyj opcji domyślnej „uruchom raz”, aby uruchomić go natychmiast. 

  ![Definicja indeksatora](./media/cognitive-search-quickstart-blob/indexer-def.png)

Kliknij przycisk **OK**, aby zaimportować, wzbogacić i indeksować dane.

  ![Powiadomienie dotyczące usługi Azure Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Indeksowanie i wzbogacenie może potrwać, dlatego na potrzeby wcześniejszej eksploracji zaleca się użycie mniejszych zestawów danych. Indeksowanie można monitorować na stronie Powiadomienia w witrynie Azure Portal. 

## <a name="query-in-search-explorer"></a>Zapytanie w Eksploratorze wyszukiwania

Po utworzeniu indeksu możesz przesyłać zapytania, aby zwrócić dokumenty z indeksu. W portalu użyj **Eksploratora wyszukiwania**, aby uruchomić zapytania i wyświetlić wyniki. 

1. Na stronie pulpitu nawigacyjnego usługi wyszukiwania kliknij pozycję **Eksplorator wyszukiwania** na pasku poleceń.

1. Kliknij pozycję **Zmień indeks** u góry, aby wybrać utworzony indeks.

1. Wprowadź ciąg wyszukiwania, aby wykonać zapytanie względem indeksu —np. „John F. Kennedy”.

Wyniki są zwracane w formacie JSON, który może zawierać wiele danych i być trudny do odczytania, szczególnie w przypadku dużych dokumentów pochodzących z obiektów blob platformy Azure. 

Jeśli nie można łatwo skanować wyników, wyszukaj dane w dokumentach za pomocą klawiszy CTRL+F. W przypadku tego zapytania wyszukaj frazę „John F. Kennedy” w danych JSON, aby wyświetlić wystąpienia tego terminu wyszukiwania. 

Użycie klawiszy CTRL+F może również ułatwić określenie liczby dokumentów znajdujących się w danym zestawie wyników. Dla obiektów blob platformy Azure portal wybiera klucz „metadata_storage_path”, ponieważ każda wartość jest unikatowa dla dokumentu. Przy użyciu klawiszy CTRL+F wyszukaj ciąg „metadata_storage_path”, aby uzyskać liczbę dokumentów. Dla tego zapytania dwa dokumenty w zestawie wyników zawierają termin „John F. Kennedy”.

  ![Przykład Eksploratora wyszukiwania](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Wnioski

Pierwsze ćwiczenie dotyczące indeksowania wzbogaconego zostało ukończone. Celem tego przewodnika Szybki start było przedstawienie ważnych pojęć i przeprowadzenie użytkownika przez proces kreatora, aby umożliwić mu szybkie prototypowanie rozwiązania do wyszukiwania poznawczego przy użyciu własnych danych.

Niektóre kluczowe założenia, które, mamy nadzieję, zostały wychwycone, obejmują zależności od źródeł danych platformy Azure. Wzbogacenie wyszukiwania poznawczego jest powiązane z indeksatorami, a indeksatory są specyficzne dla źródła i platformy Azure. Mimo że w tym przewodniku Szybki start jest używana usługa Azure Blob Storage, możliwe są inne źródła danych platformy Azure. Aby uzyskać więcej informacji, zobacz [Indeksatory w usłudze Azure Search](search-indexer-overview.md).

Innym ważnym pojęciem jest to, że umiejętności działają na polach danych wejściowych. W portalu należy wybrać pojedyncze pole źródłowe dla wszystkich umiejętności. W kodzie danymi wejściowymi mogą być inne pola lub dane wyjściowe nadrzędnej umiejętności.

 Dane wejściowe umiejętności są mapowane na pola danych wyjściowych w indeksie. Wewnętrznie portal konfiguruje [adnotacje](cognitive-search-concept-annotations-syntax.md) i definiuje [zestaw umiejętności](cognitive-search-defining-skillset.md), ustanawiając kolejność operacji i ogólny przepływ. Te kroki są ukryte w portalu, ale po rozpoczęciu pisania kodu te koncepcje stają się istotne.

Wiesz już także, że wyświetlanie wyników jest osiągane przez wykonywanie zapytań względem indeksu. W rezultacie usługa Azure Search zapewnia indeks wyszukiwania, w którym można wykonywać zapytania przy użyciu [prostej](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) lub [w pełni rozszerzonej składni zapytania](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Indeks zawierający wzbogacone pola jest taki sam, jak każdy inny. Jeśli chcesz uwzględnić standardowe lub [niestandardowe analizatory](search-analyzers.md), [ocenianie profili](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [synonimy](search-synonyms.md), [filtry aspektowe](search-filters-facets.md), wyszukiwanie geograficzne lub dowolną inną funkcję usługi Azure Search, na pewno możesz to zrobić.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po przeprowadzeniu eksploracji najszybszym sposobem wyczyszczenia środowiska jest usunięcie grupy zasobów zawierającej usługę Azure Search i usługę Azure Blob Service.  

Zakładając, że obie te usługi są umieszczone w tej samej grupie, usuń teraz grupę zasobów, aby trwale usunąć wszystkie jej elementy, w tym usługi i przechowywaną zawartość utworzoną na potrzeby tego ćwiczenia. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Następne kroki

Z indeksowaniem i wzbogacaniem możesz eksperymentować przez ponowne uruchomienie kreatora z różnymi umiejętnościami i polami danych źródłowych. Aby ponownie wykonać kroki, usuń indeks i indeksator, a następnie ponownie utwórz indeksator przy użyciu nowej kombinacji opcji.

+ W obszarze **Przegląd** > **Indeksy** wybierz utworzony indeks, a następnie kliknij pozycję **Usuń**.

+ Na stronie **Przegląd** kliknij dwukrotnie kafelek **Indeksatory**. Znajdź utworzony indeksator i usuń go.

Możesz również ponownie wykorzystać przykładowe dane i utworzone usługi, aby dowiedzieć się, jak programowo wykonywać te same zadania w następnym samouczku. 

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z interfejsów API REST wyszukiwania poznawczego](cognitive-search-tutorial-blob.md)
