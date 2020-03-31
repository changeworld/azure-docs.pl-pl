---
title: Łączenie się z magazynem wiedzy (wersja zapoznawcza) za pomocą usługi Power BI
titleSuffix: Azure Cognitive Search
description: Połącz magazyn wiedzy usługi Azure Cognitive Search (wersja zapoznawcza) z usługą Power BI w celu analizy i eksploracji.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270025"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Łączenie magazynu wiedzy za pomocą usługi Power BI

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

W tym artykule dowiesz się, jak połączyć się z magazynem wiedzy i eksplorować go przy użyciu dodatku Power Query w aplikacji Power BI Desktop. Możesz szybciej rozpocząć pracę z szablonami lub utworzyć niestandardowy pulpit nawigacyjny od podstaw.

+ Wykonaj kroki opisane w [temat Tworzenie magazynu wiedzy w witrynie Azure portal](knowledge-store-create-portal.md) lub Tworzenie magazynu wiedzy usługi Azure Cognitive Search przy użyciu [rest](knowledge-store-create-rest.md) do utworzenia przykładowego magazynu wiedzy używanego w tym instruktażu. Będzie również potrzebna nazwa konta usługi Azure Storage, który został użyty do utworzenia magazynu wiedzy, wraz z jego klucz dostępu z witryny Azure portal.

+ [Zainstalowanie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Przykładowy szablon usługi Power BI — tylko portal Azure

Podczas tworzenia [magazynu wiedzy przy użyciu witryny Azure portal](knowledge-store-create-portal.md)można pobrać [szablon usługi Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na drugiej stronie **kreatora importu danych.** Ten szablon zawiera kilka wizualizacji, takich jak WordCloud i Network Navigator, dla zawartości tekstowej. 

Kliknij **pozycję Pobierz szablon usługi Power BI** na stronie Dodawanie umiejętności **poznawczych,** aby pobrać i pobrać szablon z publicznej lokalizacji usługi GitHub. Kreator modyfikuje szablon, aby uwzględnić kształt danych, jak przechwycone w projektach magazynu wiedzy określonych w kreatorze. Z tego powodu pobierany szablon będzie się różnić przy każdym uruchomieniu kreatora, przy założeniu, że różne dane wejściowe i kwalifikacje.

![Przykładowy szablon usługi Power BI usługi Azure Cognitive Search](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Przykładowy szablon usługi Power BI")

> [!NOTE]
> Mimo że szablon jest pobierany, gdy kreator jest w trakcie lotu, musisz poczekać, aż magazyn wiedzy zostanie faktycznie utworzony w usłudze Azure Table Storage, zanim będzie można go używać.

## <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

1. Uruchom program Power BI Desktop i kliknij pozycję **Pobierz dane**.

1. W oknie **Pobierz dane** wybierz pozycję **Azure**, a następnie wybierz pozycję Usługa Azure **Table Storage**.

1. Kliknij pozycję **Połącz**.

1. W **przypadku nazwy konta lub adresu URL**wprowadź nazwę konta usługi Azure Storage (zostanie utworzony pełny adres URL).

1. Jeśli zostanie wyświetlony monit, wprowadź klucz konta magazynu.

1. Wybierz tabele zawierające dane hoteli, które zostały utworzone w poprzednich instruktażach. 

   + W przewodniku po portalu nazwy tabel to *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSSKeyPhrases*i *hotelReviewsSsPages*. 
   
   + W instruktażu REST nazwy tabel są *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases*i *hotelReviewsSentiment*.

1. Kliknij **przycisk Wczytaj**.

1. Na górnej wstążce kliknij pozycję **Edytuj kwerendy,** aby otworzyć **Edytor dodatków Power Query**.

   ![Otwórz dodatek Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otwórz dodatek Power Query")

1. Wybierz *polecenie hotelReviewsSsDocument*, a następnie usuń kolumny *PartitionKey*, *RowKey*i *Timestamp* . 
   ![Edytowanie tabel](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Edytowanie tabel")

1. Kliknij ikonę ze strzałkami przeciwstawnymi w prawym górnym rogu tabeli, aby rozwinąć *zawartość*. Po wyświetleniu listy kolumn zaznacz wszystkie kolumny, a następnie usuń zaznaczenie kolumn rozpoczynających się od "metadanych". Kliknij **przycisk OK,** aby wyświetlić wybrane kolumny.

   ![Edytowanie tabel](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozwiń zawartość")

1. Zmień typ danych dla następujących kolumn, klikając ikonę ABC-123 w lewym górnym rogu kolumny.

   + W przypadku *pliku content.latitude* i *content.longitude*wybierz opcję **Liczba dziesiętna**.
   + W przypadku *reviews_date content* i *content.reviews_dateAdded*wybierz **datę/godzinę**.

   ![Zmienianie typów danych](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Zmienianie typów danych")

1. Wybierz *hotelReviewsSsPages*, a następnie powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*.
1. Zmień typ danych dla *content.sentimentscore* na **liczbę dziesiętną**.
1. Wybierz *hotelReviewsSsKeyPhrases* i powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*. Nie ma żadnych modyfikacji typu danych dla tej tabeli.

1. Na pasku poleceń kliknij pozycję **Zamknij i zastosuj**.

1. Kliknij kafelek Model w lewym okienku nawigacji i sprawdź, czy usługa Power BI pokazuje relacje między wszystkimi trzema tabelami.

   ![Sprawdzanie poprawności relacji](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Sprawdzanie poprawności relacji")

1. Kliknij dwukrotnie każdą relację i upewnij się, że **kierunek filtru krzyżowego** jest ustawiony na **Oba**.  Dzięki temu wizualizacje mogą odświeżać się po zastosowaniu filtru.

1. Kliknij kafelek Raport w lewym okienku nawigacji, aby eksplorować dane za pomocą wizualizacji. W przypadku pól tekstowych tabele i karty są przydatnymi wizualizacjami. Można wybrać pola z każdej z trzech tabel, aby wypełnić tabelę lub kartę. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy za pomocą Eksploratora magazynu, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Wyświetlanie przy użyciu Eksploratora usługi Storage](knowledge-store-view-storage-explorer.md)