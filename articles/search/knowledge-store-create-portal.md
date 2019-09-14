---
title: Utwórz magazyn wiedzy w Azure Portal-Azure Search
description: Utwórz Azure Search magazyn wiedzy na potrzeby utrwalania wzbogacania z potoku wyszukiwania poznawczego za pomocą Kreatora importu danych w Azure Portal.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 14996d0ac9ee4e086a5dccd9275ef694adca06ca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963022"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Utwórz Azure Search magazyn wiedzy w Azure Portal

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie powinien być używany w środowisku produkcyjnym. [Interfejs API REST Azure Search wersja 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Magazyn wiedzy to funkcja w Azure Search, która utrzymuje dane wyjściowe z potoku wzbogacania AI na potrzeby późniejszej analizy lub przetwarzania podrzędnego. Potok wzbogacony AI akceptuje pliki obrazów lub pliki tekstowe bez struktury, indeksuje je przy użyciu Azure Search, stosuje wzbogacenia AI z Cognitive Services (takich jak analiza obrazów i przetwarzanie języka naturalnego), a następnie zapisuje wyniki w sklepie z bazami danych na platformie Azure Chowan. Można następnie użyć narzędzi takich jak Power BI lub Eksplorator usługi Storage do eksplorowania sklepu z bazami danych.

W tym artykule opisano Użycie Kreatora importowania danych na Azure Portal w celu pozyskiwania, indeksowania i stosowania wzbogacania AI do zestawu przeglądów hotelowych. Przeglądy hotelu są importowane do magazynu w blogu platformy Azure, a wyniki są zapisywane jako magazyn wiedzy na platformie Azure Table Storage.

Po utworzeniu sklepu z bazami informacji można dowiedzieć się, jak uzyskać dostęp do tego sklepu merytorycznego za pomocą Eksplorator usługi Storage lub Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

+ [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) do przechowywania przykładowych danych i sklepu z bazami informacji. Konto magazynu musi używać tej samej lokalizacji (na przykład US-WEas swojej usługi Azure Search, a *rodzaj konta* musi mieć wartość *StorageV2 (ogólnego przeznaczenia w wersji 2)* (wartość domyślna) lub *Storage (ogólnego przeznaczenia w wersji 1)* .

## <a name="load-the-data"></a>Ładowanie danych

Załaduj plik CSV przeglądów hotelu do magazynu obiektów blob platformy Azure, aby uzyskać do niego dostęp za pomocą indeksatora Azure Search i przechodzącego przez potok wzbogacania AI.

### <a name="create-an-azure-blob-container-with-the-data"></a>Tworzenie kontenera obiektów blob platformy Azure z danymi

1. [Pobierz dane z przeglądu hotelu zapisane w pliku CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Te dane pochodzą z programu Kaggle.com i zawierają opinie klientów dotyczące hoteli.
1. [Zaloguj się do Azure Portal](https://portal.azure.com)i przejdź do konta usługi Azure Storage.
1. [Tworzenie kontenera obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) W tym celu na pasku nawigacyjnym po lewej stronie konta magazynu kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener** na pasku poleceń.
1. Dla nowej **nazwy**kontenera wprowadź `hotel-reviews`.
1. Wybierz dowolny **poziom dostępu publicznego**. Użyto domyślnej.
1. Kliknij przycisk **OK** , aby utworzyć kontener obiektów blob platformy Azure.
1. Otwórz nowy `hotels-review` kontener, kliknij przycisk **Przekaż**, a następnie wybierz plik **HotelReviews-Free. csv** , który został pobrany w pierwszym kroku.

    ![Przekaż dane](media/knowledge-store-create-portal/upload-command-bar.png "Przekaż przeglądy hotelu")

1. Kliknij przycisk **Przekaż** , aby ZAIMPORTOWAĆ plik CSV do usługi Azure Blob Storage. Zostanie wyświetlony nowy kontener.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

### <a name="get-the-azure-storage-account-connection-string"></a>Pobierz parametry połączenia z kontem usługi Azure Storage

1. W portalu przejdź do swojego konta usługi Azure Storage.
1. W lewym panelu nawigacyjnym usługi kliknij pozycję **klucze dostępu**.
1. W obszarze **klucz 1**skopiuj i Zapisz *Parametry połączenia*. Ciąg rozpoczyna się od `DefaultEndpointsProtocol=https`. Nazwa i klucz konta magazynu są osadzone w ciągu. Poinformuj ten ciąg. Będzie on potrzebny w kolejnych krokach.

## <a name="create-and-run-ai-enrichments"></a>Utwórz i uruchom wzbogacania AI

Użyj Kreatora importu danych, aby utworzyć magazyn wiedzy. Utworzysz źródło danych, wybierzesz wzbogacanie, skonfigurujesz magazyn wiedzy i indeks, a następnie wykonaj polecenie.

### <a name="start-the-import-data-wizard"></a>Uruchamianie kreatora importu danych

1. [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)na Azure Portal.

1. Na pasku poleceń kliknij pozycję **Importuj dane** , aby uruchomić Kreatora importu.

### <a name="connect-to-your-data-import-data-wizard"></a>Nawiązywanie połączenia z danymi (Kreator importowania danych)

W tym kroku kreatora utworzysz źródło danych z obiektu blob platformy Azure z danymi hoteli.

1. Na liście **Źródło danych** wybierz pozycję **Azure Blob Storage**.
1. Wpisz`hotel-reviews-ds` **nazwę**.
1. W obszarze **tryb analizy**wybierz pozycję **Tekst rozdzielany**, a następnie zaznacz pole wyboru **pierwszy wiersz zawiera nagłówek** . Upewnij się, że **znak ogranicznika** jest przecinkiem (,).
1. Wprowadź **Parametry połączenia** usługi magazynu zapisane w poprzednim kroku.
1. W obszarze **nazwa kontenera**wprowadź `hotel-reviews`.
1. Kliknij pozycję **Next: Dodaj wyszukiwanie poznawcze (opcjonalnie)** .

      ![Tworzenie obiektu źródła danych](media/knowledge-store-create-portal/hotel-reviews-ds.png "Tworzenie obiektu źródła danych")

## <a name="add-cognitive-search-import-data-wizard"></a>Dodaj wyszukiwanie poznawcze (Kreator importowania danych)

W tym kroku kreatora utworzysz zestawu umiejętności z wzbogacaniem umiejętności poznawczych. Umiejętności, których używamy w tym przykładzie, będą wyodrębniać kluczowe frazy i wykrywać język i tonacji. Te wzbogacania będą "przewidywane" w sklepie z bazami danych jako tabele platformy Azure.

1. Rozwiń węzeł **Attach Cognitive Services**. Wartość **bezpłatna (ograniczone wzbogacania)** jest domyślnie zaznaczona. Możesz użyć tego zasobu, ponieważ liczba rekordów w HotelReviews-Free. CSV to 19, a ten bezpłatny zasób umożliwia maksymalnie 20 transakcji dziennie.
1. Rozwiń pozycję **Dodaj wzbogacania**.
1. W obszarze **Nazwa zestawu umiejętności**wprowadź `hotel-reviews-ss`.
1. W **polu Źródło danych**wybierz pozycję **reviews_text*.
1. Aby uzyskać **poziom szczegółowości wzbogacenia**, wybierz pozycję **strony (fragmenty: 5000 znaków).**
1. Wybierz następujące umiejętności poznawcze:
    + **Wyodrębnianie kluczowych fraz**
    + **Wykryj język**
    + **Wykrywanie tonacji**

      ![Utwórz zestawu umiejętności](media/knowledge-store-create-portal/hotel-reviews-ss.png "Utwórz zestawu umiejętności")

1. Rozwiń pozycję **Zapisz wzbogacanie do sklepu merytorycznego**.
1. Wprowadź **Parametry połączenia konta magazynu** zapisane w poprzednim kroku.
1. Wybierz następujące **projekcje tabeli platformy Azure**:
    + **Secret**
    + **Strony**
    + **Kluczowe frazy**

    ![Konfigurowanie sklepu merytorycznego](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurowanie sklepu merytorycznego")

1. Kliknij pozycję **Next: Dostosuj indeks**docelowy.

### <a name="import-data-import-data-wizard"></a>Importuj dane (Kreator importowania danych)

W tym kroku kreatora skonfigurujesz indeks opcjonalnych zapytań wyszukiwania pełnotekstowego. Kreator posłuży do próbkowania źródła danych w celu wywnioskowania pól i typów danych. Musisz tylko wybrać atrybuty żądanego zachowania. Na przykład atrybut możliwy do **pobierania** zezwoli usłudze wyszukiwania na zwrócenie wartości pola, podczas gdy **przeszukiwanie** spowoduje włączenie wyszukiwania pełnotekstowego w polu.

1. W obszarze **Nazwa indeksu**wprowadź `hotel-reviews-idx`.
1. W przypadku atrybutów należy wybrać następujące opcje:
    + Wybierz opcję **pobierania** dla wszystkich pól.
    + **Dla tych** pól wybierz opcję z możliwością **filtrowania** i tworzenia: *Tonacji*, *Język*, *frazy* kluczowe
    + Wybierz opcję **wyszukiwania** dla tych pól: *miasto*, *Nazwa*, *reviews_text*, *Język*, *frazy* kluczowe

    Indeks powinien wyglądać podobnie do poniższej ilustracji. Ponieważ lista jest długa, nie wszystkie pola są widoczne na obrazie.

    ![Skonfiguruj indeks](media/knowledge-store-create-portal/hotel-reviews-idx.png "Skonfiguruj indeks")

1. Kliknij pozycję **Next: Utwórz indeksator**.

### <a name="create-an-indexer"></a>Utwórz indeksator

W tym kroku kreatora skonfigurujesz indeksator, który będzie ściągał źródło danych, zestawu umiejętności i indeks zdefiniowany w poprzednich krokach kreatora.

1. W obszarze **Nazwa**wprowadź `hotel-reviews-idxr`.
1. W polu **harmonogram**Zachowaj wartość domyślną **jeden raz**.
1. Kliknij przycisk **Prześlij** , aby uruchomić indeksator. W tym kroku jest wyodrębnianie danych, indeksowanie i stosowanie umiejętności poznawczych.

### <a name="monitor-the-notifications-queue-for-status"></a>Monitorowanie kolejki powiadomień pod kątem stanu

1. W Azure Portal Monitoruj dziennik aktywności powiadomień, aby uzyskać Azure Search łącze stanu **powiadomień** . Wykonanie może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane są wzbogacane za pomocą usług poznawczych, a wyniki są zorganizowane w sklepie z bazami danych, można użyć Eksplorator usługi Storage lub Power BI do eksplorowania wzbogaconego zestawu.

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy przy użyciu Eksplorator usługi Storage, zobacz następujący przewodnik.

> [!div class="nextstepaction"]
> [Wyświetl z Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)

Aby dowiedzieć się, jak połączyć ten magazyn wiedzy z Power BI, zobacz następujący przewodnik.

> [!div class="nextstepaction"]
> [Łączenie z usługą Power BI](knowledge-store-connect-power-bi.md)

Jeśli chcesz powtórzyć to ćwiczenie lub spróbować użyć innego instruktażu do wzbogacania, Usuń usługę *Hotel-Recenzje-idxr* indeksator. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji z powrotem do zera.
