---
title: Tworzenie magazynu wiedzy (wersji zapoznawczej) w witrynie Azure portal
titleSuffix: Azure Cognitive Search
description: Kreator importu danych służy do tworzenia magazynu wiedzy używanego do utrwalania wzbogaconej zawartości. Połącz się z magazynem wiedzy w celu analizy z innych aplikacji lub wyślij wzbogaconą zawartość do procesów podrzędnych. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472370"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Szybki start: tworzenie magazynu wiedzy usługi Azure Cognitive Search w witrynie Azure portal

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Magazyn wiedzy jest funkcją usługi Azure Cognitive Search, która utrzymuje dane wyjściowe z potoku umiejętności poznawczych do kolejnych analiz lub przetwarzania podrzędnego. 

Potok akceptuje nieustrukturyzowany tekst i obrazy jako zawartość nieprzetworzoną, stosuje ai za pośrednictwem usług Cognitive Services (takich jak OCR, analiza obrazu i przetwarzanie języka naturalnego), wyodrębnia informacje i wyprowadza nowe struktury i informacje. Jednym z fizycznych artefaktów utworzonych przez potok jest [magazyn wiedzy,](knowledge-store-concept-intro.md)do którego można uzyskać dostęp za pomocą narzędzi do analizowania i eksplorowania zawartości.

W tym przewodniku Szybki start połączysz usługi i dane w chmurze platformy Azure, aby utworzyć magazyn wiedzy. Gdy wszystko będzie na swoim miejscu, uruchomisz **Kreatora importu danych** w portalu, aby pociągnąć to wszystko razem. Efektem końcowym jest oryginalna zawartość tekstowa oraz zawartość generowana przez AI, którą można wyświetlić w portalu[(Eksplorator magazynu).](knowledge-store-view-storage-explorer.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki start używa usługi Azure Cognitive Search, usługi Azure Blob storage i [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) dla sztucznej inteligencji. 

Ponieważ obciążenie jest tak małe, usługi Cognitive Services są wykorzystywane za kulisami, aby zapewnić bezpłatne przetwarzanie dla maksymalnie 20 transakcji dziennie. Ponieważ zestaw danych jest tak mały, można pominąć tworzenie lub dołączanie zasobu usług Cognitive Services.

1. [Pobierz HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dane te są danymi recenzji hotelu zapisanymi w pliku CSV (pochodzącymi z Kaggle.com) i zawierają 19 opinii klientów na temat jednego hotelu. 

1. [Utwórz konto magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub znajdź istniejące [konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) w ramach bieżącej subskrypcji. Użyjesz magazynu platformy Azure zarówno dla zawartości nieprzetworzonej do zaimportowania, jak i dla magazynu wiedzy, który jest wynikiem końcowym.

   Wybierz typ konta **StorageV2 (ogólnego przeznaczenia V2).**

1. Otwórz strony usług obiektów Blob i utwórz kontener o nazwie *hotel-reviews*.

1. Kliknij pozycję **Przekaż**.

    ![Przesyłanie danych](media/knowledge-store-create-portal/upload-command-bar.png "Prześlij opinie o hotelu")

1. Wybierz plik **HotelReviews-Free.csv** pobrany w pierwszym kroku.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

1. Prawie skończyłeś pracę z tym zasobem, ale przed opuszczeniem tych stron użyj łącza w lewym okienku nawigacji, aby otworzyć stronę **Klucze dostępu.** Pobierz ciąg połączenia, aby pobrać dane z magazynu obiektów Blob. Parametry połączenia wyglądają podobnie do następującego przykładu:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Nadal w portalu przełącz się na usługę Azure Cognitive Search. [Utwórz nową usługę](search-create-service-portal.md) lub [znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

Teraz można przystąpić do pracy w Kreatorze importu danych.

## <a name="run-the-import-data-wizard"></a>Uruchamianie kreatora importu danych

Na stronie Przegląd usługi wyszukiwania kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć magazyn wiedzy w czterech krokach.

  ![Polecenie importu danych](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

1. W obszarze **Nawiązywanie połączenia z danymi** wybierz opcję **Magazyn obiektów blob Azure**, a następnie wybierz konto i utworzony kontener. 
1. W **Name**przypadku nazwy `hotel-reviews-ds`wprowadź .
1. W **trybie analizowania**wybierz pozycję **Tekst rozdzielany**, a następnie zaznacz pole wyboru **Pierwszy wiersz zawiera nagłówek.** Upewnij się, że **znak ogranicznika** jest przecinkiem (,).
1. W **polu Parametry połączenia**wklej ciąg połączenia skopiowany ze strony **Klucze dostępu** w usłudze Azure Storage.
1. W **kontenerach**wprowadź nazwę kontenera obiektów blob zawierającego dane.

    Strona powinna wyglądać podobnie do poniższego zrzutu ekranu.

    ![Tworzenie obiektu źródła danych](media/knowledge-store-create-portal/hotel-reviews-ds.png "Tworzenie obiektu źródła danych")

1. Przejdź do następnej strony.

### <a name="step-2-add-cognitive-skills"></a>Krok 2. Dodawanie umiejętności poznawczych

W tym kroku kreatora, można utworzyć skillset z umiejętności poznawczych wzbogacenia. Dane źródłowe składają się z opinii klientów w kilku językach. Umiejętności, które są istotne dla tego zestawu danych obejmują wyodrębnianie fraz kluczowych, wykrywanie tonacji i tłumaczenie tekstu. W późniejszym kroku te wzbogacenia będą "rzutowane" do magazynu wiedzy jako tabele platformy Azure.

1. Rozwiń **Dołącz usługi Cognitive Services**. **Wolne (ograniczone wzbogacenia)** jest wybierany domyślnie. Możesz użyć tego zasobu, ponieważ liczba rekordów w HotelReviews-Free.csv wynosi 19, a ten bezpłatny zasób pozwala na maksymalnie 20 transakcji dziennie.
1. Rozwiń **Dodaj wzbogacenia**.
1. W przypadku nazwy `hotel-reviews-ss` **skillset**wprowadź .
1. W polu **Dane źródłowe**wybierz **reviews_text**.
1. Aby uzyskać **poziom szczegółowości wzbogacenia,** wybierz **strony (5000 znaków fragmentów)**
1. Wybierz te umiejętności poznawcze:
    + **Wyodrębnianie kluczowych fraz**
    + **Tłumaczenie tekstu**
    + **Wykrywanie tonacji**

      ![Tworzenie zestawu umiejętności](media/knowledge-store-create-portal/hotel-reviews-ss.png "Tworzenie zestawu umiejętności")

1. Rozwiń **Zapisz wzbogacenia do magazynu wiedzy**.
1. Wybierz te **rzuty tabeli platformy Azure:**
    + **Dokumenty**
    + **Strony**
    + **Kluczowe zwroty**
1. Wprowadź parametry **połączenia konta magazynu** zapisane w poprzednim kroku.

    ![Konfigurowanie magazynu wiedzy](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurowanie magazynu wiedzy")

1. Opcjonalnie pobierz szablon usługi Power BI. Po dodaniu dostępu do szablonu z kreatora lokalny plik pbit jest dostosowywany do kształtu danych.

1. Przejdź do następnej strony.

### <a name="step-3-configure-the-index"></a>Krok 3. Konfigurowanie indeksu

W tym kroku kreatora skonfigurujesz indeks dla opcjonalnych zapytań wyszukiwania pełnotekstowego. Kreator zapróbkuje źródło danych, aby wywnioskować pola i typy danych. Wystarczy wybrać atrybuty dla żądanego zachowania. Na przykład atrybut **Retrievable** umożliwi usłudze wyszukiwania zwrócenie wartości pola, podczas gdy **przeszukiwanie włączy** wyszukiwanie pełnotekstowe w polu.

1. W przypadku nazwy `hotel-reviews-idx` **indeksu**wprowadź .
1. W przypadku atrybutów zaakceptuj domyślne wybory: **Możliwe do pobrania** i **przeszukiwanie** nowych pól, które tworzy potok.

    Indeks powinien wyglądać podobnie do poniższego obrazu. Ponieważ lista jest długa, nie wszystkie pola są widoczne na obrazie.

    ![Konfigurowanie indeksu](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurowanie indeksu")

1. Przejdź do następnej strony.

### <a name="step-4-configure-the-indexer"></a>Krok 4. Konfigurowanie indeksatora

W tym kroku kreatora skonfigurujesz indeksator, który połączy źródło danych, zestaw umiejętności i indeks zdefiniowany w poprzednich krokach kreatora.

1. W **yjmij nazwę**, wprowadź `hotel-reviews-idxr`.
1. W polu **Harmonogram**zachowaj domyślny **raz**.
1. Kliknij **przycisk Prześlij,** aby uruchomić indeksator. Ekstrakcja danych, indeksowanie, stosowanie umiejętności poznawczych zdarzają się w tym kroku.

## <a name="monitor-status"></a>Stan monitora

Indeksowanie umiejętności poznawczych trwa dłużej niż typowe indeksowanie tekstowe. Kreator powinien otworzyć listę Indeksator na stronie przeglądu, co pozwala na śledzenie postępu. W przypadku samodzielnej nawigacji przejdź do strony Przegląd, a następnie kliknij przycisk **Indeksatory**.

W witrynie Azure portal można również monitorować dziennik aktywności powiadomień dla klikalnego łącza **stanu powiadomień usługi Azure Cognitive Search.** Wykonanie może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane zostały wzbogacone za pomocą usług Cognitive Services i rzutowane na wyniki w magazynie wiedzy, można użyć Eksploratora magazynu lub usługi Power BI do eksplorowania wzbogaconego zestawu danych.

Możesz wyświetlić zawartość w Eksploratorze magazynu lub zrobić krok dalej dzięki usłudze Power BI, aby uzyskać szczegółowe informacje za pomocą wizualizacji.

> [!div class="nextstepaction"]
> [Wyświetlanie za pomocą programu](knowledge-store-view-storage-explorer.md)
> Explorer magazynu[połącz z programem Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub wypróbować inny przewodnik wzbogacania AI, usuń *indeksator hotel-reviews-idxr.* Usunięcie indeksatora resetuje licznik transakcji dziennego z powrotem do zera dla przetwarzania usług Cognitive Services.
