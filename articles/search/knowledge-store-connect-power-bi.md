---
title: Nawiązywanie połączenia z magazynem wiedzy przy użyciu Power BI-Azure Search
description: Połącz Azure Search ze sklepem z bazami danych, korzystając z Power BI do analizy i eksploracji.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: de282213535a2e49f73bc30e476bae02d470fdb2
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265664"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Łączenie ze sklepem wiedzy Power BI

> [!Note]
> Magazyn wiedzy jest w wersji zapoznawczej i nie powinien być używany w środowisku produkcyjnym. [Interfejs API REST Azure Search wersja 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>
W tym artykule dowiesz się, jak nawiązać połączenie i eksplorować magazyn wiedzy przy użyciu Power Query w aplikacji Power BI Desktop. Aby utworzyć przykład sklepu z bazami danych używany w tym przewodniku, zobacz temat [Tworzenie sklepu z bazami danych w Azure Portal](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w temacie [Tworzenie magazynu wiedzy w Azure Portal,](knowledge-store-create-portal.md) aby utworzyć przykładowy magazyn wiedzy używany w tym instruktażu. Potrzebna będzie również nazwa konta usługi Azure Storage, które zostało użyte do utworzenia magazynu wiedzy, wraz z jego kluczem dostępu z poziomu Azure Portal.

+ [Zainstaluj Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

1. Rozpocznij Power BI Desktop a następnie kliknij pozycję **Pobierz dane**.

1. W oknie **pobieranie danych** wybierz pozycję **Azure**, a następnie wybierz pozycję **Azure Table Storage**.

1. Kliknij przycisk **Połącz**.

1. W polu **nazwa konta lub adres URL**wprowadź nazwę konta usługi Azure Storage (pełny adres URL zostanie utworzony).

1. Jeśli zostanie wyświetlony monit, wprowadź klucz konta magazynu.

1. Wybierz tabele *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*i *hotelReviewsSsPages* . Te tabele to projekcje w tabeli platformy Azure z przykładowymi danymi dotyczącymi przeanaliz hotelowych i obejmują wzbogacenie usługi poznawczej, które zostały wybrane podczas tworzenia magazynu wiedzy.

1. Kliknij przycisk **Załaduj**.

1. Na górnej wstążce kliknij przycisk **Edytuj zapytania** , aby otworzyć **Edytor Power Query**.

   ![Otwórz Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otwórz Power Query")

1. Wybierz pozycję *hotelReviewsSsDocument*, a następnie usuń kolumny *PartitionKey*, *RowKey*i *timestamp* . 

   ![Edytuj tabele](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Edytuj tabele")

1. Kliknij ikonę z przeciwległymi strzałkami w prawym górnym rogu tabeli, aby rozwinąć *zawartość*. Gdy zostanie wyświetlona lista kolumn, zaznacz opcję wszystkie kolumny, a następnie usuń zaznaczenie kolumn, które zaczynają się od "Metadata". Kliknij przycisk **OK** , aby wyświetlić wybrane kolumny.

   ![Edytuj tabele](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozwiń zawartość")

1. Zmień typ danych dla następujących kolumn, klikając ikonę ABC-123 w lewym górnym rogu kolumny.

   + W obszarze *Content. Latitude* i *Content. Długość geograficzna*wybierz **liczbę dziesiętną**.
   + W przypadku *Content. reviews_date* i *Content. Reviews_dateAdded*wybierz pozycję **Data/godzina**.

   ![Zmień typy danych](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Zmień typy danych")

1. Wybierz pozycję *hotelReviewsSsPages*, a następnie powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*.
1. Zmień typ danych *Content. SentimentScore* na **liczbę dziesiętną**.
1. Wybierz pozycję *hotelReviewsSsKeyPhrases* , a następnie powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*. Brak modyfikacji typu danych dla tej tabeli.

1. Na pasku poleceń kliknij przycisk **Zamknij i Zastosuj**.

1. Kliknij kafelek model w lewym okienku nawigacji i sprawdź, czy Power BI pokazują relacje między wszystkimi trzema tabelami.

   ![Weryfikuj relacje](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Weryfikuj relacje")

1. Kliknij dwukrotnie każdą relację i upewnij się, że ustawiono **kierunek filtrowania krzyżowego** **.**  Umożliwia to odświeżanie wizualizacji w przypadku zastosowania filtru.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Czyszczenie

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy przy użyciu Eksplorator usługi Storage, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Wyświetl z Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)

Aby dowiedzieć się, jak utworzyć magazyn wiedzy przy użyciu interfejsów API REST i programu Poster, zobacz następujący artykuł.  

> [!div class="nextstepaction"]
> [Tworzenie sklepu merytorycznego w usłudze REST](knowledge-store-howto.md)
