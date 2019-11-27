---
title: Utwórz magazyn wiedzy (wersja zapoznawcza) w Azure Portal
titleSuffix: Azure Cognitive Search
description: Za pomocą Kreatora importu danych można utworzyć magazyn wiedzy używany do utrwalania wzbogaconej zawartości. Nawiąż połączenie z magazynem wiedzy na potrzeby analizy z innych aplikacji lub Wyślij ulepszoną zawartość do procesów podrzędnych. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: a8cc368b2949d9a65034ee4f989b8603dfa01027
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533959"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Szybki Start: Tworzenie sklepu z bazami danych Azure Wyszukiwanie poznawcze w Azure Portal

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Sklep z bazami informacji to funkcja platformy Azure Wyszukiwanie poznawcze, która utrzymuje dane wyjściowe potoku umiejętności poznawcze dla kolejnych analiz lub przetwarzania podrzędnego. 

Potok akceptuje obrazy i niestrukturalny tekst jako zawartość nieprzetworzona, stosuje AI do Cognitive Services (takich jak przetwarzanie obrazów i języka naturalnego) i tworzy wzbogaconą zawartość (nowe struktury i informacje) jako dane wyjściowe. Jednym z artefaktów fizycznych utworzonych w potoku jest [Magazyn wiedzy](knowledge-store-concept-intro.md), do którego można uzyskać dostęp za pomocą narzędzi, aby analizować i eksplorować zawartość.

W tym przewodniku szybki start utworzysz usługi i dane w chmurze platformy Azure w celu utworzenia sklepu z bazami informacji. Gdy wszystko będzie na miejscu, uruchom kreatora **importowania danych** w portalu, aby ściągnąć wszystkie te elementy. Wynik końcowy jest oryginalny oraz zawartość wygenerowana przez AI, którą można wyświetlić w portalu ([Eksplorator magazynu](knowledge-store-view-storage-explorer.md)).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

Ten przewodnik Szybki Start używa platformy Azure Wyszukiwanie poznawcze, usługi Azure Blob Storage i [usługi azure Cognitive Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. 

Ze względu na to, że obciążenie jest małe, Cognitive Services jest wybierana w tle, aby zapewnić bezpłatne przetwarzanie do 20 transakcji codziennie w przypadku wywołania z usługi Azure Wyszukiwanie poznawcze. Jeśli używasz przykładowych danych, które udostępniamy, możesz pominąć tworzenie i dołączanie zasobów Cognitive Services.

1. [Pobierz plik HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Te dane to dane z przeglądu hotelu zapisane w pliku CSV (pochodzące z Kaggle.com) i zawierają 19 opinii klientów na temat pojedynczego hotelu. 

1. [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) w ramach bieżącej subskrypcji. Będziesz używać usługi Azure Storage do importowania nieprzetworzonej zawartości oraz magazynu wiedzy, który jest wynikiem końcowym.

   Dla tego konta istnieją dwa wymagania:

   + Wybierz ten sam region co usługa Azure Wyszukiwanie poznawcze. 
   
   + Wybierz typ konta StorageV2 (ogólnego przeznaczenia w wersji 2). 

1. Otwórz strony usługi BLOB Services i Utwórz kontener.  

1. Kliknij pozycję **Przekaż**.

    ![Przekaż dane](media/knowledge-store-create-portal/upload-command-bar.png "Przekaż przeglądy hotelu")

1. Wybierz plik **HotelReviews-Free. csv** , który został pobrany w pierwszym kroku.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start.

Teraz można przystąpić do przenoszenia Kreatora importu danych.

## <a name="run-the-import-data-wizard"></a>Uruchom Kreatora importowania danych

Na stronie Przegląd usługi wyszukiwania kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć magazyn wiedzy w czterech krokach.

  ![Polecenie importu danych](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

1. W obszarze **Nawiązywanie połączenia z danymi** wybierz opcję **Magazyn obiektów blob Azure**, a następnie wybierz konto i utworzony kontener. 
1. W polu **Nazwa**wprowadź `hotel-reviews-ds`.
1. W obszarze **tryb analizy**wybierz pozycję **Tekst rozdzielany**, a następnie zaznacz pole wyboru **pierwszy wiersz zawiera nagłówek** . Upewnij się, że **znak ogranicznika** jest przecinkiem (,).
1. Wprowadź **Parametry połączenia** usługi magazynu zapisane w poprzednim kroku.
1. W obszarze **nazwa kontenera**wprowadź `hotel-reviews`.
1. Kliknij przycisk **Dalej: Dodaj wzbogacanie AI (opcjonalnie)** .

      ![Tworzenie obiektu źródła danych](media/knowledge-store-create-portal/hotel-reviews-ds.png "Tworzenie obiektu źródła danych")

1. Przejdź do następnej strony.

### <a name="step-2-add-cognitive-skills"></a>Krok 2. Dodawanie umiejętności poznawczych

W tym kroku kreatora utworzysz zestawu umiejętności z wzbogacaniem umiejętności poznawczych. Umiejętności, których używamy w tym przykładzie, będą wyodrębniać kluczowe frazy i wykrywać język i tonacji. W późniejszym kroku te wzbogacania będą "zorganizowane" w sklepie z bazami danych jako tabele platformy Azure.

1. Rozwiń węzeł **Attach Cognitive Services**. Wartość **bezpłatna (ograniczone wzbogacania)** jest domyślnie zaznaczona. Możesz użyć tego zasobu, ponieważ liczba rekordów w HotelReviews-Free. CSV to 19, a ten bezpłatny zasób umożliwia maksymalnie 20 transakcji dziennie.
1. Rozwiń węzeł **Dodawanie umiejętności poznawczych**.
1. W obszarze **Nazwa zestawu umiejętności**wprowadź `hotel-reviews-ss`.
1. W **polu Źródło danych**wybierz pozycję **reviews_text**.
1. Aby uzyskać **poziom szczegółowości wzbogacenia**, wybierz pozycję **strony (fragmenty: 5000 znaków).**
1. Wybierz następujące umiejętności poznawcze:
    + **Wyodrębnianie kluczowych fraz**
    + **Wykryj język**
    + **Wykrywanie tonacji**

      ![Utwórz zestawu umiejętności](media/knowledge-store-create-portal/hotel-reviews-ss.png "Tworzenie zestawu umiejętności")

1. Rozwiń pozycję **Zapisz wzbogacanie do sklepu merytorycznego**.
1. Wprowadź **Parametry połączenia konta magazynu** zapisane w poprzednim kroku.
1. Wybierz następujące **projekcje tabeli platformy Azure**:
    + **Secret**
    + **Strony**
    + **Kluczowe frazy**

    ![Konfigurowanie sklepu merytorycznego](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurowanie sklepu merytorycznego")

1. Przejdź do następnej strony.

### <a name="step-3-configure-the-index"></a>Krok 3. Konfigurowanie indeksu

W tym kroku kreatora skonfigurujesz indeks opcjonalnych zapytań wyszukiwania pełnotekstowego. Kreator posłuży do próbkowania źródła danych w celu wywnioskowania pól i typów danych. Musisz tylko wybrać atrybuty żądanego zachowania. Na przykład atrybut możliwy do **pobierania** zezwoli usłudze wyszukiwania na zwrócenie wartości pola, podczas gdy **przeszukiwanie** spowoduje włączenie wyszukiwania pełnotekstowego w polu.

1. W obszarze **Nazwa indeksu**wprowadź `hotel-reviews-idx`.
1. W przypadku atrybutów należy wybrać następujące opcje:
    + Wybierz opcję **pobierania** dla wszystkich pól.
    + Wybierz opcję z możliwością **filtrowania** i **kroju** dla tych pól: *tonacji*, *Język*, *frazy* kluczowe
    + Wybierz opcję **wyszukiwania** dla tych pól: *miasto*, *Nazwa*, *reviews_text*, *Język*, *frazy* kluczowe

    Indeks powinien wyglądać podobnie do poniższej ilustracji. Ponieważ lista jest długa, nie wszystkie pola są widoczne na obrazie.

    ![Skonfiguruj indeks](media/knowledge-store-create-portal/hotel-reviews-idx.png "Skonfiguruj indeks")

1. Przejdź do następnej strony.

### <a name="step-4-configure-the-indexer"></a>Krok 4. Konfigurowanie indeksatora

W tym kroku kreatora skonfigurujesz indeksator, który będzie ściągał źródło danych, zestawu umiejętności i indeks zdefiniowany w poprzednich krokach kreatora.

1. W obszarze **Nazwa**wprowadź `hotel-reviews-idxr`.
1. W polu **harmonogram**Zachowaj wartość domyślną **jeden raz**.
1. Kliknij przycisk **Prześlij** , aby uruchomić indeksator. W tym kroku jest wyodrębnianie danych, indeksowanie i stosowanie umiejętności poznawczych.

## <a name="monitor-status"></a>Monitorowanie stanu

Indeksowanie umiejętności poznawcze trwa dłużej niż typowe indeksowanie tekstowe. Kreator powinien otworzyć listę Indeksator na stronie przeglądu, co pozwala na śledzenie postępu. W przypadku samodzielnej nawigacji przejdź do strony Przegląd i kliknij opcję **Indeksatory**.

W Azure Portal można także monitorować dziennik aktywności powiadomień dla łącza stanu **powiadomień wyszukiwanie poznawcze platformy Azure** . Wykonanie może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane zostały wzbogacone przy użyciu Cognitive Services i zostały przedstawione wyniki w sklepie z bazami danych, można użyć Eksplorator usługi Storage lub Power BI do eksplorowania wzbogaconego zestawu.

Możesz wyświetlić zawartość w Eksplorator usługi Storage lub zawęzić krok z Power BI, aby uzyskać wgląd w dane za pomocą wizualizacji.

> [!div class="nextstepaction"]
> [Widok z Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)
> [Connect with Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub spróbować użyć innego instruktażu do wzbogacania, Usuń usługę *Hotel-Recenzje-idxr* indeksator. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji do zera na potrzeby przetwarzania Cognitive Services.
