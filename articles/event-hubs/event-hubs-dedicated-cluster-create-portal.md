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
ms.openlocfilehash: ffdcd3e6314e659443630f74f8f17261659f1246
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158511"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-preview-using-azure-portal"></a>Szybki start: Tworzenie dedykowanego klastra usługi Event Hubs (wersja zapoznawcza) przy użyciu witryny Azure portal 
Zdarzenie Hubs klastry oferują wdrożenia pojedynczej dzierżawy dla klientów korzystających z najbardziej wymagające potrzeby przesyłania strumieniowego. Ta oferta pojedynczej dzierżawy ma gwarantowanej umowy SLA dostępność przez 99,99% i jest dostępna tylko w warstwie cenowej dedykowanych. Klaster usługi Event Hubs można ruch przychodzący miliony zdarzeń na sekundę przy gwarancji wydajności i subsecond opóźnienia. Przestrzenie nazw i centrów zdarzeń utworzonych w dedykowanym klastrze obejmują wszystkie funkcje standardowe oferty i inne, ale bez żadnych ograniczeń transferu danych przychodzących. Obejmuje również popularnej [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) funkcji bez dodatkowych kosztów, co pozwala na automatycznie dane wsadowe i dziennika przesyła strumieniowo do [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) lub [usługi Azure Data Lake Storage Velikost haldy 1](../data-lake-store/data-lake-store-overview.md). Aby uzyskać więcej informacji na temat usługi Event Hubs dedykowanych klastrów, zobacz [Przegląd dedykowanej usługi Event Hubs](event-hubs-dedicated-overview.md).

Dedykowane klastry są aprowizowane i opłata jest naliczana według **jednostek pojemności (CUs)**, wstępnie przydzielić ilości zasobów Procesora i pamięci. Możesz kupić 1, 2, 4, 8, 12, 16 lub 20 jednostek pojemności dla każdego klastra. Skalowanie w górę klaster, najpierw Utwórz klaster z jednego miejsca przechowywania Opartego na, a następnie prześlij [bilet pomocy technicznej](#submit-a-support-request-for-your-dedicated-cluster). 

W tym przewodniku Szybki Start utworzysz dedykowany klaster usługi Event Hubs przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Konto platformy Azure. Jeśli nie masz, [zakupieniu konta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) przed przystąpieniem do wykonywania. Ta funkcja nie jest obsługiwana przy użyciu bezpłatnego konta platformy Azure. 
- [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (wersja 15.3, 26730.01) lub nowszej.
- [Zestaw .NET Standard SDK](https://dotnet.microsoft.com/download) w wersji 2.0 lub nowszej.
- [Utworzono grupę zasobów](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Utwórz klaster dedykowanych centrów zdarzeń
Dedykowany klaster usługi Event Hubs zapewnia kontener określania zakresu unikatowy, odwołuje się jego pełni kwalifikowaną nazwę domeny, w którym można utworzyć jeden lub kilka przestrzeni nazw. Na etapie wersji zapoznawczej portalu samoobsługowego środowiska, można utworzyć jeden klaster CU w obsługiwanych regionach. Klaster większa niż jeden pojemności, należy najpierw utworzyć klaster z jednej pojemności i przesłać żądanie, aby skalować w klastrze. 

Aby utworzyć klaster w grupie zasobów przy użyciu witryny Azure portal, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **+ Utwórz zasób** menu nawigacji po lewej stronie.
2. Typ **zdarzenie Hubs klastry** na pasku wyszukiwania, naciśnij klawisz ENTER.
3. Na **tworzenia klastrów** strony, wykonaj następujące czynności:
    1. Wprowadź **nazwa klastra**. System od razu sprawdza, czy nazwa jest dostępna.
    2. Wybierz **subskrypcji** , w której chcesz utworzyć klaster.
    3. Wybierz **grupy zasobów** , w której chcesz utworzyć klaster.
    4. Wybierz **lokalizacji** klastra. Jeśli z preferowanym regionem jest wyszarzona, Prześlij [żądania pomocy technicznej](#submit-a-support-request-for-your-dedicated-cluster).
    5. Wybierz **dalej: Tagi** znajdujący się u dołu strony. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

        ![Utwórz zdarzenie Hubs klastra — podstawowe informacje o stronie](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
4. Na **tagi** strony, wykonaj następujące czynności:
    1. Wprowadź **nazwa** i **wartość** dla tagu, które chcesz dodać. Ten krok jest **opcjonalny**.  
    2. Wybierz **przeglądu + Utwórz** przycisku.

        ![Utwórz zdarzenie Hubs klastra — strona tagów](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
5. Na **przeglądanie + tworzenie** strony, przejrzyj szczegóły, a następnie wybierz **Utwórz**. 

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


## <a name="submit-a-support-request-for-your-dedicated-cluster"></a>Prześlij żądanie pomocy technicznej dla dedykowanego klastra

Aby przesłać żądanie pomocy technicznej, wykonaj następujące kroki:

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
1. Aby usunąć klaster, wybierz **Usuń** z górnego menu.
2. Zostanie wyświetlony komunikat z potwierdzeniem chęć usunięcia klastra.
3. Typ **nazwa klastra** i wybierz **Usuń** Aby usunąć klaster.

    ![Usuń stronę klastra](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono klaster usługi Event Hubs. Aby uzyskać instrukcje krok po kroku wysyłanie i odbieranie zdarzeń z Centrum zdarzeń i Przechwytywanie zdarzeń do usługi Azure storage lub Azure Data Lake Store zobacz następujące samouczki:

- [Wysyłanie i odbieranie zdarzeń na platformie .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Włączanie usługi Event Hubs Capture za pomocą witryny Azure portal](event-hubs-capture-enable-through-portal.md)
- [Przy użyciu usługi Azure Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
