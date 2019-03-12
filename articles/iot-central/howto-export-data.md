---
title: Eksportowanie danych z usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Sposób eksportowania danych z aplikacji usługi Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 98e19cccff1c02f653022f2061854697ee11d1a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759903"
---
# <a name="export-your-data-from-azure-iot-central"></a>Eksportowanie danych z usługi Azure IoT Central

*W tym temacie mają zastosowanie do administratorów.*

W tym artykule opisano sposób używania funkcji eksportu ciągłego dane w usłudze Azure IoT Central Eksport danych w ramach swojej własnej **usługi Azure Blob Storage**, **usługi Azure Event Hubs**, i **usługi Azure Service Bus** wystąpień. Możesz wyeksportować **pomiarów**, **urządzeń**, i **szablonów urządzeń** do docelowej ścieżki dostępu do ciepłych i analizy ścieżki nieaktywnej. Możesz wyeksportować dane do magazynu obiektów Blob, przeprowadzać analizę długoterminowych trendów w usłudze Microsoft Power BI lub eksportowanie danych do usługi Event Hubs i Service Bus, aby przekształcić i rozszerzyć swoje dane w czasie niemal rzeczywistym za pomocą usługi Azure Logic Apps lub Azure Functions.

> [!Note]
> Po włączeniu ciągły Eksport danych otrzymasz tylko dane od tej pory wartości. Obecnie nie można pobrać danych w czasie, gdy ciągły Eksport danych zostało wyłączone. Do przechowania większej ilości danych historycznych, należy włączyć funkcję ciągły Eksport danych z wcześniej.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz być administratorem w Twojej aplikacji IoT Central

## <a name="export-to-blob-storage"></a>Eksportowanie do usługi Blob Storage

Pomiary, urządzeń i danych szablonów urządzeń zostaną wyeksportowane do swojego konta magazynu, raz na minutę, z każdego pliku, zawierającego partii zmiany od ostatniego wyeksportowany plik. Wyeksportowane dane znajdują się w [Apache AVRO](https://avro.apache.org/docs/current/index.html) formatu.

Dowiedz się więcej o [eksportowanie do usługi Blob storage](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Eksportowanie do usług Event Hubs i Service Bus

Pomiary, urządzeń i danych szablonów urządzeń zostaną wyeksportowane do usługi Centrum zdarzeń lub kolejki usługi Service Bus lub temat. Pomiary wyeksportowane dane umieszczane są w czasie niemal rzeczywistym i zawiera materiałami komunikat urządzenia wysyłane do IoT Central, nie tylko wartości pomiarów samodzielnie. Dane wyeksportowane urządzenia nadejściu w partiach, co minutę i zawiera zmiany właściwości i ustawienia wszystkie urządzenia i szablony wyeksportowane urządzenia zawiera zmiany do wszystkich szablonów urządzenia.

Dowiedz się więcej o [eksportowanie do usługi Event Hubs i Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Jeśli nie masz istniejącego magazynu lub zdarzenia koncentratory/usługi Service Bus można wyeksportować do, wykonaj następujące kroki:

### <a name="create-storage-account"></a>Tworzenie konta magazynu

1. Tworzenie [nowe konto magazynu w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Dowiedz się więcej w [dokumentacja usługi Azure Storage](https://aka.ms/blobdocscreatestorageaccount).

2. Dla typu konta, wybierz **ogólnego przeznaczenia** lub **magazynu obiektów Blob**.

3. wybierz subskrypcję.

    > [!Note]
    > Teraz możesz wyeksportować dane do innych subskrypcji, które są **nie sam** co dla twojej aplikacji płatność za rzeczywiste użycie IoT Central. Łączysz się przy użyciu parametrów połączenia, w tym przypadku.

4. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **usługę Blob Service**, wybierz opcję **Przeglądaj obiekty BLOB**. Wybierz **+ kontener** u góry, aby utworzyć nowy kontener.

### <a name="create-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

1. Tworzenie [nowych nazw usługi Event Hubs w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Dowiedz się więcej w [dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

2. wybierz subskrypcję.

    > [!Note]
    > Teraz możesz wyeksportować dane do innych subskrypcji, które są **nie sam** co dla twojej aplikacji płatność za rzeczywiste użycie IoT Central. Łączysz się przy użyciu parametrów połączenia, w tym przypadku.

3. Tworzenie Centrum zdarzeń w przestrzeni nazw usługi Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ Centrum zdarzeń** u góry, aby utworzyć wystąpienie Centrum zdarzeń.

### <a name="create-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. Tworzenie [nowej przestrzeni nazw magistrali usług w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Dowiedz się więcej w [dokumentacja usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).

2. wybierz subskrypcję.

    > [!Note]
    > Teraz możesz wyeksportować dane do innych subskrypcji, które są **nie sam** co dla twojej aplikacji płatność za rzeczywiste użycie IoT Central. Łączysz się przy użyciu parametrów połączenia, w tym przypadku.

3. Przejdź do przestrzeni nazw usługi Service Bus i wybierz **+ kolejka** lub **+ temat** u góry, aby utworzyć kolejki lub tematu, aby wyeksportować do.

## <a name="set-up-continuous-data-export"></a>Konfigurowanie ciągły Eksport danych

Teraz, gdy masz docelowym magazynu/Event Hubs/Service Bus, aby wyeksportować dane, wykonaj następujące kroki, aby skonfigurować ciągły Eksport danych.

1. Zaloguj się do aplikacji IoT Central.

2. W menu po lewej stronie wybierz **ciągły Eksport danych**.

    > [!Note]
    > Jeśli nie widzisz ciągły Eksport danych w menu po lewej stronie, nie jesteś administratorem w swojej aplikacji. Porozmawiaj z administratorem, aby skonfigurować Eksport danych.

    ![Utwórz nowe Centrum zdarzeń](media/howto-export-data/export_menu.png)

3. Wybierz **+ nowy** przycisk w prawym górnym rogu. Wybierz jedną z **usługi Azure Blob Storage**, **usługi Azure Event Hubs**, lub **usługi Azure Service Bus** jako miejsce docelowe eksportu.

    > [!NOTE]
    > Maksymalna liczba eksportów aplikacji wynosi pięć.

    ![Utwórz nowy ciągły Eksport danych](media/howto-export-data/export_new.png)

4. W polu listy rozwijanej wybierz swoje **magazynu konta/Event Hubs przestrzeń nazw/przestrzeń nazw magistrali**. Można również wybrać na liście, która jest ostatnia opcja **wprowadź parametry połączenia**. 

    > [!NOTE]
    > Wyświetlane są tylko magazyn kont/Event Hubs przestrzenie nazw/usługi Service Bus przestrzeniach nazw **tej samej subskrypcji co aplikacja IoT Central**. Jeśli chcesz wyeksportować do miejsca docelowego spoza tej subskrypcji, wybierz opcję **wprowadź parametry połączenia** i zobacz krok 5.

    > [!NOTE]
    > 7-dniowy, który eksportowania aplikacji w wersji próbnej, jedynym sposobem, aby skonfigurować ciągłe danych jest za pomocą parametrów połączenia. Jest to spowodowane 7-dniowy aplikacji w wersji próbnej nie mają skojarzonej subskrypcji platformy Azure.

    ![Utwórz nowy CRP Centrum zdarzeń](media/howto-export-data/export_create.png)

5. (Opcjonalnie) Jeśli została wybrana opcja **wprowadź parametry połączenia**, nowe pole pojawia się należy wkleić parametry połączenia. Aby uzyskać parametry połączenia dla usługi:
    - Konto magazynu, przejdź do konta magazynu w witrynie Azure Portal.
        - W obszarze **ustawienia**, wybierz opcję **klucze dostępu**
        - Skopiuj parametry połączenia klucz1 lub klucz2 parametry połączenia
    - Usługa Event Hubs lub usługi Service Bus, przejdź do przestrzeni nazw w witrynie Azure Portal.
        - W obszarze **ustawienia**, wybierz opcję **współużytkowane zasady dostępu**
        - Wybierz domyślne **RootManageSharedAccessKey** lub Utwórz nową
        - Skopiuj parametry połączenia podstawowej lub dodatkowej

6. W polu listy rozwijanej, wybierz kontener/Event hub/kolejki lub tematu.

7. W obszarze **danych do wyeksportowania**, określ każdy rodzaj danych do wyeksportowania, ustawiając typ **na**.

8. Aby włączyć ciągły Eksport danych, upewnij się, **eksportu danych** jest **na**. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie ciągły Eksport danych](media/howto-export-data/export_list.png)

9. Po kilku minutach dane są wyświetlane w wybranej lokalizacji docelowej.

## <a name="next-steps"></a>Kolejne kroki

Teraz gdy wiesz, jak wyeksportować dane, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Eksportuj dane do usługi Azure Blob Storage](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Eksportuj dane do usługi Azure Event Hubs i Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Jak wizualizować dane w usłudze Power BI](howto-connect-powerbi.md)
