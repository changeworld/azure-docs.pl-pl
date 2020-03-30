---
title: Tworzenie dedykowanego klastra centrum zdarzeń przy użyciu portalu Azure
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster usługi Azure Event Hubs przy użyciu witryny Azure portal.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77157486"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Szybki start: tworzenie dedykowanego klastra centrów zdarzeń przy użyciu portalu Azure 
Klastry centrów zdarzeń oferują wdrożenia z jedną dzierżawą dla klientów o najbardziej wymagających potrzebach przesyłania strumieniowego. Ta oferta ma gwarantowaną 99,99% umowy SLA i jest dostępna tylko w naszej dedykowanej warstwie cenowej. [Klaster centrum zdarzeń](event-hubs-dedicated-overview.md) może przychodzących miliony zdarzeń na sekundę z gwarantowaną pojemnością i opóźnieniem podsekundowym. Przestrzenie nazw i centra zdarzeń utworzone w klastrze zawierają wszystkie funkcje standardowej oferty i więcej, ale bez żadnych limitów transferu danych przychodzących. Dedykowana oferta obejmuje również popularną funkcję [przechwytywania centrów zdarzeń](event-hubs-capture-overview.md) bez dodatkowych kosztów, co pozwala automatycznie wsadować i rejestrować strumienie danych do [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) lub usługi Azure Data Lake Storage Gen [1.](../data-lake-store/data-lake-store-overview.md)

Dedykowane klastry są aprowizowane i rozliczane przez **jednostki pojemności (CU),** wstępnie przydzieloną ilość zasobów procesora CPU i pamięci. Dla każdego klastra można kupić 1, 2, 4, 8, 12, 16 lub 20 punktów zakupu. W tym przewodniku Szybki start przejdziemy przez tworzenie klastra 1 cu event hubs za pośrednictwem witryny Azure portal.

> [!NOTE]
> To samoobsługowe środowisko jest obecnie dostępne w wersji zapoznawczej w witrynie [Azure Portal.](https://aka.ms/eventhubsclusterquickstart) Jeśli masz jakieś pytania dotyczące dedykowanej oferty, skontaktuj się z [zespołem Event Hubs.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Konto platformy Azure. Jeśli go nie masz, [kup konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) przed rozpoczęciem. Ta funkcja nie jest obsługiwana przy bezpłatnym koncie platformy Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Aktualizacja 3 (wersja 15.3, 26730.01) lub nowsza.
- [Zestaw .NET Standard SDK](https://dotnet.microsoft.com/download) w wersji 2.0 lub nowszej.
- [Utworzono grupę zasobów](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Tworzenie dedykowanego klastra centrów zdarzeń
Klaster centrum zdarzeń udostępnia unikatowy kontener zakresu, w którym można utworzyć jeden lub więcej obszarów nazw. W tej fazie podglądu samoobsługowego portalu można utworzyć 1 klastry CU w wybranych regionach. Jeśli potrzebujesz klastra większego niż 1 CU, możesz przesłać żądanie pomocy technicznej platformy Azure, aby skalować w górę klastra po jego utworzeniu.

Aby utworzyć klaster w grupie zasobów przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. Kliknij [ten link,](https://aka.ms/eventhubsclusterquickstart) aby utworzyć klaster w witrynie Azure portal. Z drugiej strony wybierz **pozycję Wszystkie usługi** z lewego okienka nawigacji, a następnie wpisz "Klastry centrów zdarzeń" na pasku wyszukiwania i wybierz "Klastry centrów zdarzeń" z listy wyników.
2. Na stronie **Tworzenie klastra** skonfiguruj następujące elementy:
    1. Wprowadź **nazwę klastra**. System od razu sprawdza, czy nazwa jest dostępna.
    2. Wybierz **subskrypcję,** w której chcesz utworzyć klaster.
    3. Wybierz **grupę zasobów,** w której chcesz utworzyć klaster.
    4. Wybierz **lokalizację** klastra. Jeśli preferowany region jest wyszarzony, jest tymczasowo poza możliwością i można przesłać [żądanie pomocy technicznej](#submit-a-support-request) do zespołu centrum zdarzeń.
    5. Wybierz przycisk **Dalej: Znaczniki** u dołu strony. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

        ![Tworzenie klastra centrów zdarzeń — strona Podstawy](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na stronie **Znaczniki** skonfiguruj następujące elementy:
    1. Wprowadź **nazwę** i **wartość** znacznika, który chcesz dodać. Ten krok jest **opcjonalny**.  
    2. Wybierz przycisk **Recenzja + Utwórz.**

        ![Strona Tworzenie klastra centrów zdarzeń — strona Znaczniki](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na stronie **Recenzja + Utwórz** przejrzyj szczegóły i wybierz pozycję **Utwórz**. 

    ![Strona Tworzenie klastra centrów zdarzeń — recenzja + strona Utwórz](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Tworzenie obszaru nazw i centrum zdarzeń w klastrze

1. Aby utworzyć obszar nazw w klastrze, na stronie **Klaster centrów zdarzeń** dla klastra wybierz **polecenie +Obszar nazw** z górnego menu.

    ![Strona zarządzania klastrem — przycisk dodaj obszar nazw](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na stronie tworzenie obszaru nazw wykonaj następujące czynności:
    1. Wprowadź **nazwę obszaru nazw**.  System sprawdza, czy nazwa jest dostępna.
    2. Obszar nazw dziedziczy następujące właściwości:
        1. Identyfikator subskrypcji
        2. Grupa zasobów
        3. Lokalizacja
        4. Nazwa klastra
    3. Wybierz **pozycję Utwórz,** aby utworzyć obszar nazw. Teraz możesz zarządzać klastrem.  

        ![Tworzenie obszaru nazw na stronie klastra](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po utworzeniu obszaru nazw można [utworzyć centrum zdarzeń,](event-hubs-create.md#create-an-event-hub) tak jak zwykle w obszarze nazw. 


## <a name="submit-a-support-request"></a>Prześlij prośbę o pomoc techniczną

Jeśli chcesz zmienić rozmiar klastra po utworzeniu lub jeśli preferowany region nie jest dostępny, prześlij prośbę o pomoc techniczną, wykonując następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pomoc + **wsparcie** z lewego menu.
2. Wybierz **+ Nowe żądanie pomocy technicznej** z menu Pomoc techniczna.
3. Na stronie pomocy technicznej wykonaj następujące kroki:
    1. W przypadku **typu problemu**wybierz pozycję **Techniczne** z listy rozwijanej.
    2. W polu **Subskrypcja** wybierz subskrypcję.
    3. W **obszarze Usługa**wybierz pozycję Moje **usługi**, a następnie wybierz pozycję Centrum **zdarzeń**.
    4. W przypadku **zasobu**wybierz klaster, jeśli już istnieje, w przeciwnym razie wybierz opcję **Ogólne pytanie/zasób niedostępny**.
    5. W obszarze **Typ problemu**wybierz opcję **Przydział**.
    6. W polu **Problem wybierz**jedną z następujących wartości z listy rozwijanej:
        1. Wybierz **opcję Poproś o dedykowaną jednostkę SKU,** aby zażądać tej funkcji, która ma być obsługiwana w Twoim regionie.
        2. Wybierz **żądanie skalowania w górę lub skalowanie w dół dedykowanego klastra,** jeśli chcesz skalować w górę lub skalować w dół dedykowanego klastra. 
    7. W przypadku **tematu**opisz problem.

        ![Strona biletu pomocy technicznej](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Usuwanie dedykowanego klastra
 
1. Aby usunąć klaster, wybierz **polecenie Usuń** z górnego menu. Należy pamiętać, że klaster będzie rozliczany przez co najmniej 4 godziny użytkowania po utworzeniu. 
2. Pojawi się komunikat potwierdzający chęć usunięcia klastra.
3. Wpisz **nazwę klastra** i wybierz pozycję **Usuń,** aby usunąć klaster.

    ![Usuwanie strony klastra](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono klaster centrum zdarzeń. Aby uzyskać instrukcje krok po kroku do wysyłania i odbierania zdarzeń z centrum zdarzeń i przechwytywania zdarzeń do magazynu platformy Azure lub usługi Azure Data Lake Store, zobacz następujące samouczki:

- Wysyłanie i odbieranie zdarzeń 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
- [Włącz przechwytywanie usługi Event Hubs za pomocą witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Korzystanie z usługi Azure Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
