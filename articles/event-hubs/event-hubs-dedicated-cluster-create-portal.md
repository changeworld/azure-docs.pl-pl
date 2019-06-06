---
title: Szybki Start Azure — Tworzenie klastra dedykowanej usługi Event Hubs przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak utworzyć klaster usługi Azure Event Hubs przy użyciu witryny Azure portal.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/02/2019
ms.author: xurui
ms.openlocfilehash: 269ecca8683229a56d40dfacc354abbd7ce10762
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688590"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Szybki start: Tworzenie dedykowanego klastra usługi Event Hubs przy użyciu witryny Azure portal 
Zdarzenie Hubs klastry oferują wdrożenia pojedynczej dzierżawy dla klientów korzystających z najbardziej wymagające potrzeby przesyłania strumieniowego. Ta oferta ma umowy SLA gwarantowaną dostępność przez 99,99% i jest dostępna tylko w naszych dedykowane warstwy cenowej. [Klastra usługi Event Hubs](event-hubs-dedicated-overview.md) można ruch przychodzący miliony zdarzeń na sekundę z gwarantowaną pojemności i subsecond opóźnieniami. Przestrzenie nazw i centrów zdarzeń utworzone w ramach klastra obejmują wszystkie funkcje standardowe oferty i inne, ale bez żadnych ograniczeń transferu danych przychodzących. Dedykowana oferta obejmuje również popularnej [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) funkcji bez dodatkowych kosztów, co pozwala na automatyczne usługi batch i dziennika strumieni danych w celu [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) lub [ Usługa Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Dedykowane klastry są aprowizowane i opłata jest naliczana według **jednostek pojemności (CUs)** , wstępnie przydzielić ilości zasobów Procesora i pamięci. Możesz kupić 1, 2, 4, 8, 12, 16 lub 20 jednostek pojemności dla każdego klastra. W tym przewodniku Szybki Start przeprowadzimy Cię przez proces tworzenia klastra 1 CU Event Hubs za pomocą witryny Azure portal.

> [!NOTE]
> To środowisko samoobsługowego jest obecnie dostępna w wersji zapoznawczej w [witryny Azure Portal](https://aka.ms/eventhubsclusterquickstart). Jeśli masz jakieś pytania dotyczące ofertą dedykowaną, skontaktuj się z [zespołu usługi Event Hubs](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Konto platformy Azure. Jeśli nie masz, [zakupieniu konta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) przed przystąpieniem do wykonywania. Ta funkcja nie jest obsługiwana przy użyciu bezpłatnego konta platformy Azure. 
- [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (wersja 15.3, 26730.01) lub nowszej.
- [Zestaw .NET Standard SDK](https://dotnet.microsoft.com/download) w wersji 2.0 lub nowszej.
- [Utworzono grupę zasobów](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Utwórz klaster dedykowanych centrów zdarzeń
Klaster usługi Event Hubs zapewnia kontener określania zakresu unikatowy w którym można utworzyć jeden lub kilka przestrzeni nazw. Na tym etapie wersji zapoznawczej środowisko portalu samoobsługowego, można utworzyć 1 CU klastrów w wybranych regionach. Jeśli potrzebujesz klastra większy niż 1 CU, możesz przesłać żądanie pomocy technicznej platformy Azure, aby skalować w górę klastra po jego utworzeniu.

Aby utworzyć klaster w grupie zasobów przy użyciu witryny Azure portal, wykonaj następujące czynności:

1. Postępuj zgodnie z [ten link](https://aka.ms/eventhubsclusterquickstart) do tworzenia klastra w witrynie Azure portal. Z drugiej strony, wybierz **wszystkich usług** z okienka nawigacji po lewej stronie, wpisz "Zdarzenie Hubs klastry" na pasku wyszukiwania, a następnie wybierz pozycję "Zdarzenie Hubs klastry" z listy wyników.
2. Na **tworzenia klastrów** strony, skonfiguruj następujące opcje:
    1. Wprowadź **nazwa klastra**. System od razu sprawdza, czy nazwa jest dostępna.
    2. Wybierz **subskrypcji** , w której chcesz utworzyć klaster.
    3. Wybierz **grupy zasobów** , w której chcesz utworzyć klaster.
    4. Wybierz **lokalizacji** klastra. Jeśli z preferowanym regionem jest wyszarzona, jest tymczasowo pojemności i możesz przesłać [żądania pomocy technicznej](#submit-a-support-request) do zespołu usługi Event Hubs.
    5. Wybierz **dalej: Tagi** znajdujący się u dołu strony. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

        ![Utwórz zdarzenie Hubs klastra — podstawowe informacje o stronie](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na **tagi** strony, skonfiguruj następujące opcje:
    1. Wprowadź **nazwa** i **wartość** dla tagu, które chcesz dodać. Ten krok jest **opcjonalny**.  
    2. Wybierz **przeglądu + Utwórz** przycisku.

        ![Utwórz zdarzenie Hubs klastra — strona tagów](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na **przeglądanie + tworzenie** strony, przejrzyj szczegóły, a następnie wybierz **Utwórz**. 

    ![Utwórz stronę zdarzenie Hubs klastra — przeglądanie + Tworzenie strony](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Tworzenie przestrzeni nazw i Centrum zdarzeń w ramach klastra

1. Aby utworzyć przestrzeń nazw w klastrze, na **zdarzenie Hubs klastra** strony dla klastra, wybierz opcję **+ Namespace** z górnego menu.

    ![Klaster strony Zarządzanie — Dodawanie przycisku przestrzeni nazw](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Twórz strony przestrzeni nazw wykonaj następujące czynności:
    1. Wprowadź **nazwę przestrzeni nazw**.  System sprawdza, czy nazwa jest dostępna.
    2. Przestrzeń nazw dziedziczy następujące właściwości:
        1. Identyfikator subskrypcji
        2. Grupa zasobów
        3. Lokalizacja
        4. Nazwa klastra
    3. Wybierz **Utwórz** można utworzyć przestrzeni nazw. Możesz teraz zarządzać klastrem.  

        ![Tworzenie przestrzeni nazw na stronie klastra](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po utworzeniu przestrzeni nazw można [Tworzenie Centrum zdarzeń](event-hubs-create.md#create-an-event-hub) jak zwykle tworzone w przestrzeni nazw. 


## <a name="submit-a-support-request"></a>Prześlij żądanie pomocy technicznej

Jeśli chcesz zmienić rozmiar klastra po zakończeniu tworzenia lub jeśli nie jest dostępna z preferowanym regionem, Prześlij żądanie pomocy technicznej wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Pomoc i obsługa techniczna** menu po lewej stronie.
2. Wybierz **+ nowe żądanie pomocy technicznej** menu pomocy technicznej.
3. Na stronie pomocy technicznej wykonaj następujące kroki:
    1. Aby uzyskać **typ problemu**, wybierz opcję **techniczne** z listy rozwijanej.
    2. W polu **Subskrypcja** wybierz subskrypcję.
    3. Dla **usługi**, wybierz opcję **wykaz usług**, a następnie wybierz pozycję **usługi Event Hubs**.
    4. Aby uzyskać **zasobów**, wybierz klaster, jeśli już istnieje, w przeciwnym razie wybierz **ogólne pytania lub zasobu nie jest dostępna**.
    5. Aby uzyskać **typ problemu**, wybierz opcję **przydziału**.
    6. Aby uzyskać **podtypu Problem**, wybierz jedną z następujących wartości z listy rozwijanej:
        1. Wybierz **żądania w ramach dedykowanej jednostki SKU** żądania dla funkcji, które są obsługiwane w Twoim regionie.
        2. Wybierz **limit czasu żądania Skaluj w górę lub Skaluj w dół dedykowany klaster** Aby skalować w górę lub w dół dedykowany klaster. 
    7. Aby uzyskać **podmiotu**, opisz problem.

        ![Strona bilet pomocy technicznej](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Usuń dedykowany klaster
 
1. Aby usunąć klaster, wybierz **Usuń** z górnego menu. Należy pamiętać, rozliczana za okres co najmniej 4 godziny użycia po utworzeniu klastra. 
2. Zostanie wyświetlony komunikat z potwierdzeniem chęć usunięcia klastra.
3. Typ **nazwa klastra** i wybierz **Usuń** Aby usunąć klaster.

    ![Usuń stronę klastra](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono klaster usługi Event Hubs. Aby uzyskać instrukcje krok po kroku wysyłanie i odbieranie zdarzeń z Centrum zdarzeń i Przechwytywanie zdarzeń do usługi Azure storage lub Azure Data Lake Store zobacz następujące samouczki:

- [Wysyłanie i odbieranie zdarzeń na platformie .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Włączanie usługi Event Hubs Capture za pomocą witryny Azure portal](event-hubs-capture-enable-through-portal.md)
- [Przy użyciu usługi Azure Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
