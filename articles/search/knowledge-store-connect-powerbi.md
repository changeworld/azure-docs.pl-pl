---
title: Nawiązywanie połączenia z usługą Knowledge Store za pomocą Power BI-Azure Search
description: Utwórz magazyn wiedzy przy użyciu Kreatora importowania danych w Azure Portal, a następnie połącz się z Power BI na potrzeby analizy i eksploracji.
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 66a051c430be9f59569a5843c0138c7ddc15b87c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707148"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Tworzenie Azure Search magazynu wiedzy i nawiązywanie połączenia przy użyciu Power BI

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie powinien być używany w środowisku produkcyjnym. [Interfejs API REST Azure Search wersja 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Magazyn wiedzy to funkcja w Azure Search, która utrzymuje dane wyjściowe z potoku wzbogacenia AI do późniejszej analizy lub innego przetwarzania podrzędnego. Potok wzbogacony AI akceptuje pliki obrazów lub pliki tekstowe bez struktury z obsługiwanego źródła danych, wysyła je do Azure Search indeksowania, stosuje wzbogacenia AI z Cognitive Services (takich jak analiza obrazu i przetwarzanie języka naturalnego), a następnie zapisuje wyniki z magazynem wiedzy w usłudze Azure Storage. W sklepie z bazami danych można dołączać narzędzia takie jak Power BI lub Eksplorator usługi Storage, aby eksplorować wyniki.

W tym artykule opisano tworzenie sklepu z bazami informacji w portalu, a następnie nawiązywanie połączenia i eksplorowanie przy użyciu Power Query w Power BI Desktop. 

W tym instruktażu jest używany zestaw danych obejmujący przeglądy i oceny klienta, tonacji ocenianie z Cognitive Services, a następnie Power Query do wysyłania zapytań do dokumentów. Dane pochodzą z danych dotyczących usługi hotelowej w witrynie Kaggle.com. 

## <a name="prerequisites"></a>Wymagania wstępne

+ [Pobierz plik CSV przeglądów hotelu (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ten zestaw danych zawiera rekordy opinii klientów na temat hoteli.

+ [Program Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Usługa Azure Search](search-create-service-portal.md). W tym przewodniku możesz użyć bezpłatnej usługi. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Upewnij się, że konto to "ogólnego przeznaczenia", czyli *StorageV2 (ogólnego przeznaczenia w wersji 2)* , czyli magazynu domyślnego *(ogólnego przeznaczenia w wersji 1)* . Wybierz ten sam region co Azure Search.
 
## <a name="prepare-data"></a>Przygotowywanie danych 

Załaduj plik CSV do usługi Azure Blob Storage, aby można było uzyskać do niego dostęp za pomocą indeksatora Azure Search.

1. [Zaloguj się do Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , aby zawierał przykładowe dane: 

   1. Nazwij kontener `hotel-reviews`. 
   
   1. Ustaw poziom dostępu publicznego na dowolną z jego prawidłowych wartości. Użyto domyślnej.

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń.

1. Przejdź do folderu zawierającego **HotelReviews-Free. csv**, wybierz plik, a następnie kliknij przycisk **Przekaż**.

   ![Przekaż plik CSV](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Przekaż plik CSV")

1. Podczas pracy w usłudze Azure Storage Pobierz parametry połączenia i nazwę kontenera.  Oba te ciągi będą potrzebne podczas tworzenia obiektu źródła danych:

   1. Na stronie Przegląd kliknij pozycję **klucze dostępu** i skopiuj *Parametry połączenia*. Zaczyna `DefaultEndpointsProtocol=https;` się od i zawiera `EndpointSuffix=core.windows.net`. Nazwa konta i klucz znajdują się między. 

   1. Nazwa kontenera musi być lub `hotel-reviews` być dowolną nazwą, którą chcesz przypisać. 

## <a name="create-and-run-ai-enrichments"></a>Utwórz i uruchom wzbogacania AI

Użyj Kreatora importu danych, aby utworzyć magazyn wiedzy. Spowoduje to zaimportowanie zestawu danych, wybranie wzbogacania, skonfigurowanie magazynu wiedzy i indeksu, a następnie wykonanie.

1. [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na Azure Portal.

1. Kliknij pozycję **Importuj dane** na pasku poleceń.

1. Utwórz obiekt źródła danych na pierwszej stronie kreatora.

   - Wybierz pozycję **Azure Blob Storage**.

   - Nadaj źródłu danych nazwę, na przykład *Hotel-przegląda-ds*.

   - Ponieważ dane są w formacie CSV, wybierz opcję **Tekst rozdzielany** dla trybu analizy, zaznacz **pierwszy wiersz zawiera nagłówek**i upewnij się, że znak ogranicznika jest przecinkiem.

   - Parametry połączenia z kontem usługi Azure Storage można uzyskać w portalu w obszarze **klucze dostępu**.

   - Nazwę kontenera można również uzyskać z portalu na liście obiektów BLOB usługi Azure Storage.

      ![Tworzenie obiektu źródła danych](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Tworzenie obiektu źródła danych")

1. Dodaj wzbogacanie i skonfiguruj magazyn wiedzy na drugiej stronie kreatora.

   - W polu **dołącz Cognitive Services**możesz użyć bezpłatnego zasobu, który umożliwia maksymalnie 20 transakcji dziennie. Liczba rekordów w HotelReviews-Free. CSV jest mniejsza niż 20.

   - W **obszarze Dodaj**wzbogacenia Nazwij zestawu umiejętności *hotelu-Reviews-SS*, wybierz pole *Reviews_text* , wybierz poziom szczegółowości *stron (fragmenty 5000 znaków)* , a następnie wybierz te trzy umiejętności poznawcze: *Wyodrębniaj kluczowe frazy*, *wykrywaj język*, *wykrywaj tonacji*.

      ![Utwórz zestawu umiejętności](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Utwórz zestawu umiejętności")

   - W obszarze **Zapisz wzbogacanie do sklepu merytorycznego**podaj parametry połączenia z kontem usługi Azure Storage ogólnego przeznaczenia. Magazyn wiedzy jest tworzony w usłudze Azure Table Storage w przypadku wybrania opcji *projektów tabel platformy Azure* w tej sekcji.

      ![Konfigurowanie sklepu merytorycznego](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Konfigurowanie sklepu merytorycznego")
   
   Kliknij pozycję **Next: Dostosuj indeks** docelowy, aby przejść do następnego kroku.

1. Skonfiguruj indeks opcjonalnych zapytań wyszukiwania pełnotekstowego w Azure Search. Chociaż ten Instruktaż jest Power BI połączony z usługą Azure Table Storage, Kreator **importu danych** może również utworzyć indeks używany do wyszukiwania pełnotekstowego w Azure Search. 

   Kreator próbuje użyć źródła danych, aby wywnioskować pola i typy danych, więc wszystko, co trzeba zrobić, to wybranie atrybutów niezbędnych do osiągnięcia potrzebnych zachowań. Na przykład pobieranie *oznacza,* że zawartość pola może być pobierana z usługi, podczas gdy *Wyszukiwanie* umożliwia wyszukiwanie pełnotekstowe w wybranych polach.

   - Nadaj indeksowi nazwę, na przykład *Hotel-przegląda-IDX*.
   - Ustaw wszystkie pola jako do **pobierania**.
   - Ustaw *miasto*, *nazwę*, *reviews_text*, *Język*, *frazy* kluczowe jako **kryterium wyszukiwania**.
   - Ustaw *tonacji*, *Język*, *frazy* kluczowe jako **filtrowane** i **kroju**. 
   
    Indeks powinien wyglądać podobnie do poniższej ilustracji.

     ![Skonfiguruj indeks](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Skonfiguruj indeks")

   Kliknij pozycję **Next: Utwórz indeksator** , aby przejść do następnego kroku.

1. Skonfiguruj indeksator, nadając mu nazwę i erze wykonywania. W tym instruktażu należy użyć usługi *Hotel-Recenzje-idxr* jako nazwy indeksatora i **użyć domyślnego harmonogramu** , aby natychmiast uruchomić indeksator.

   Wykonanie indeksatora umieszcza wszystkie poprzednie konfiguracje w ruchu. Operacje wyodrębniania, przetwarzania i pozyskiwania są wykonywane w tym kroku.

1. Monitorowanie indeksowania w kolejce powiadomień w portalu. Wykonanie może zająć kilka minut.

## <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

1. Rozpocznij Power BI Desktop i wybierz pozycję **Pobierz dane**.

1. W oknie pobieranie danych wybierz pozycję **Azure** , a następnie opcję **Azure Table Storage**. Kliknij przycisk **Połącz**.

1. W polu Nazwa konta lub adres URL wklej nazwę konta usługi Azure Storage (pełny adres URL jest rozpoznawany).

1. Wprowadź klucz konta.

1. Wybierz pozycję dokument, frazy kluczowe i strony. Są to tabele utworzone za pomocą Kreatora importu danych po wybraniu tych samych elementów o tej samej nazwie w konfiguracji magazynu wiedzy. Kliknijprzycisk Załaduj.

1. Otwórz Power Query, klikając polecenie **Edytuj zapytania** .

   ![Otwórz Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Otwórz Power Query")

1. Dla dokumentów:

   - Usuń kolumny PartitionKey, RowKey i timestamp utworzone przez usługę Azure Table Storage. Magazyn wiedzy zawiera relacje używane w tej analizie.

   - Rozwiń kolumnę zawartość.

     ![Edytuj tabele](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Edytuj tabele")

1. Zaznacz wszystkie pola, a następnie usuń ich zaznaczenie, rozpoczynając od "Metadata".

1. Popraw typ danych dla następujących kolumn przy użyciu ikony ABC-123 w każdej kolumnie:

   - Kolumny dat powinny być **typu DateTime**
   - *Szerokość geograficzna* powinna być **liczbą dziesiętną**
   - *Długość geograficzna* powinna być **liczbą dziesiętną**

1. Powtórz poprzednie kroki dla fraz kluczowych, usuwając PartitionKey i inne kolumny, rozwijając kolumny zawartości, ustawiając *SentimentScore* na **liczbę dziesiętną**.

1. Powtórz ponownie dla stron, usuwając PartitionKey i inne kolumny, rozwijając kolumny zawartości. Brak modyfikacji typu danych dla tej tabeli.

1. Kliknij przycisk **Zamknij i Zastosuj** po lewej stronie paska poleceń Power Query.

1. Sprawdź, czy Power BI rozpoznaje relacje utworzone przez magazyn wiedzy w ramach danych. Kliknij kafelek relacje w okienku nawigacji po lewej stronie. Wszystkie trzy tabele powinny być powiązane. Edytuj relacje i upewnij się, że "kierunek filtrowania krzyżowego" jest ustawiony na oba, co zapewni, że wszystkie wizualizacje zostaną odświeżone w przypadku zastosowania filtru.

   ![Weryfikuj relacje](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Weryfikuj relacje")

## <a name="try-with-larger-data-sets"></a>Wypróbuj większe zestawy danych

W celu uniknięcia naliczania opłat za przewodnik pokazowy zachowamy niewielką konfigurację danych. W celu uzyskania bardziej realistycznego środowiska można utworzyć i dołączyć zasób Cognitive Services rozliczany, aby umożliwić większą liczbę transakcji w odniesieniu do analizatora tonacji, ekstrakcji keyphrase i umiejętności wykrywania języka.

Utwórz nowe kontenery w usłudze Azure Blob Storage i przekaż każdy plik CSV do własnego kontenera. Określ jeden z tych kontenerów w kroku tworzenia źródła danych w Kreatorze importu danych.

| Opis | Łącze |
|-------------|------|
| Warstwa Bezpłatna   | [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Małe (500 rekordów) | [HotelReviews_Small. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Średni (6000 rekordów)| [HotelReviews_Medium. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Duży (pełny zestaw danych 35000 rekordów) | [HotelReviews_Large. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Należy pamiętać, że bardzo duże zestawy danych są kosztowne do przetworzenia. Koszty te są w przybliżeniu $1000 USD.|

W kroku wzbogacania kreatora Dołącz [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rozliczanego zasobu, który został utworzony w warstwie *S0* w tym samym regionie co Azure Search, aby użyć większych zestawów danych. 

  ![Tworzenie zasobu Cognitive Services](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Tworzenie zasobu Cognitive Services")

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz powtórzyć to ćwiczenie lub wykonać inne czynności związane z wzbogacaniem AI, Usuń właśnie utworzony indeks *hotelu-przeglądający-IDX* . Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji z powrotem do zera. 

Aby uzyskać więcej wskazówek krok po kroku przedstawiających informacje o sklepie z bazami informacji, przejdź do następnego artykułu, w którym pokazano, jak utworzyć magazyn wiedzy przy użyciu interfejsów API REST i programu Poster.

> [!div class="nextstepaction"]
> [Wprowadzenie do sklepu merytorycznego](knowledge-store-howto.md)
