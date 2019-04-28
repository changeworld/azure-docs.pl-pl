---
title: Co to są powiadomienia o kondycji usług platformy Azure?
description: Powiadomienia o kondycji usług umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowana przez Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883646"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usług są publikowane przez platformę Azure i zawierają informacje dotyczące zasobów w ramach Twojej subskrypcji. Te powiadomienia są podklasą klasy działania z dziennika zdarzeń i można także znaleźć w dzienniku aktywności. Powiadomienia o kondycji usług może być informacyjne lub informacje z możliwością działania, w zależności od klasy.

Istnieją różne rodzaje powiadomień dotyczących kondycji usługi:  

- **Wymagana akcja:** Azure zauważyć coś nietypowego powstawania na Twoim koncie i współpraca z Tobą, aby rozwiązać ten problem. Platforma Azure wysyła powiadomienie, albo szczegółowych informacji na temat akcji, które należy wykonać lub jak skontaktuj się z działem pomocy technicznej lub inżynierów platformy Azure.  
- **Asystowana odzyskiwania:** Wystąpiło zdarzenie i inżynierowie sprawdzeniu, czy wciąż występują wpływ. Inżynierów platformy Azure wymaga współpracę z Tobą bezpośrednio, aby przywrócić usługi kondycji pełne.  
- **Zdarzenia:** Zdarzenie, które ma wpływ na usługi obecnie ma wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Konserwacji:** Działanie planowanej konserwacji, które mogą mieć wpływ na co najmniej jeden z zasobów w ramach Twojej subskrypcji.  
- **Informacje:** Użyj potencjalnych optymalizacje, które mogą pomóc poprawić zasobu. 
- **Zabezpieczenia:** Pilne informacje związane z zabezpieczeniami dotyczące rozwiązania, które są uruchamiane na platformie Azure.

Każde powiadomienie kondycji usługi zawiera szczegółowe informacje na temat zakresu i wpływu na Twoje zasoby. Szczegółowe informacje obejmują:

Nazwa właściwości | Opis
-------- | -----------
kanały | Jeden z następujących wartości: **Administrator** lub **operacji**.
correlationId | Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które należą do tego samego działania zwykle współużytkować ten sam identyfikator korelacji.
eventDataId | Unikatowy identyfikator zdarzenia.
eventName | Tytuł zdarzenia.
poziom | Poziom zdarzenia
resourceProviderName | Nazwa dostawcy zasobów zasób objęty wpływem.
Typ zasobu| Typ zasobu zasób objęty wpływem.
subStatus | Zwykle kod stanu HTTP REST odpowiednich wywołań, ale mogą również obejmować inne parametry opisujące podstanu. Na przykład: OK (kod stanu HTTP: 200), utworzone (kod stanu HTTP: 201) zaakceptowane (kod stanu HTTP: 202), żadnej zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404) konflikt (kod stanu HTTP: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504).
eventTimestamp | Sygnatura czasowa podczas generowania zdarzenia według usługi platformy Azure, przetwarzanie żądania odpowiadający zdarzenia.
submissionTimestamp | Sygnatura czasowa, gdy zdarzenie stały się dostępne dla zapytań.
subscriptionId | Subskrypcja platformy Azure, w którym zarejestrowano to zdarzenie.
status | Ciąg opisujący stan operacji. Niektóre typowe wartości to: **Pracę**, **w toku**, **zakończyło się pomyślnie**, **nie powiodło się**, **Active**, i **rozwiązane**.
operationName | Nazwa operacji.
category | Ta właściwość jest zawsze **ServiceHealth**.
resourceId | Identyfikator zasobu zasób objęty wpływem.
Properties.Title | Zlokalizowane tytuł dla tej komunikacji. Domyślnym będzie angielski.
Properties.Communication | Zlokalizowane szczegóły dotyczące komunikacji z kod znaczników HTML. Domyślnym będzie angielski.
Properties.incidentType | Jeden z następujących wartości: **Wymagana akcja**, **informacyjny**, **zdarzenia**, **konserwacji**, lub **zabezpieczeń**.
Properties.trackingId | Zdarzenie, z którą jest skojarzone to zdarzenie. Umożliwia korelowanie zdarzeń powiązanych ze zdarzeniem.
Properties.impactedServices | O zmienionym znaczeniu obiektu blob JSON opisujące usługi i regiony wpływ zdarzenie. Właściwość zawiera listę usług, z których każdy ma **ServiceName**i listę regionów, których to dotyczy, z których każdy ma **RegionName**.
Properties.defaultLanguageTitle | Komunikacja w języku angielskim.
Properties.defaultLanguageContent | Komunikacja w języku angielskim, jako kod znaczników HTML czy zwykły tekst.
Properties.Stage | Możliwe wartości parametru **zdarzenia**, i **zabezpieczeń** są **aktywny,** **rozwiązane** lub **RCA**. Aby uzyskać **wymagana akcja** lub **komunikat o charakterze informacyjnym** jest to jedyna wartość **Active.** Aby uzyskać **konserwacji** są: **Aktywne**, **planowane**, **InProgress**, **anulowane**, **zmienił czas**, **rozwiązane**, lub **pełną**.
Properties.communicationId | Komunikacja, z którą jest skojarzone to zdarzenie.

### <a name="details-on-service-health-level-information"></a>Szczegółowe informacje na temat poziomu informacje o kondycji usług

**Wymagana akcja** (properties.incidentType == wymagana akcja)
- Komunikat o charakterze informacyjnym - czynności wykonywane przez administratora zajść konieczność uniemożliwiają wpływu na istniejące usługi
    
**Konserwacja** (properties.incidentType == konserwacji)
- Ostrzeżenie - pilnej konserwacji
- Komunikat o charakterze informacyjnym — standardowa planowanej konserwacji

**Informacje o** (properties.incidentType == informacji)
- Informacyjny — Administrator może być konieczne zapobiegną do istniejących usług

**Zabezpieczenia** (properties.incidentType == zabezpieczeń)
- Błąd — powszechne problemy, dostęp do wielu usług w wielu regionach mających wpływ na szeroką gamę klientów.
- Ostrzeżenie — problemy z dostępu do określonych usług i/lub konkretnych regionów mających wpływ na podzbiór klientów.
- Komunikat o charakterze informacyjnym — problemy wpływające na operacji zarządzania i/lub opóźnienia, negatywnego wpływu na dostępność usług.

**Usługa problemów** (properties.incidentType == zdarzenia)
- Błąd — powszechne problemy, dostęp do wielu usług w wielu regionach mających wpływ na szeroką gamę klientów.
- Ostrzeżenie — problemy z dostępu do określonych usług i/lub konkretnych regionów mających wpływ na podzbiór klientów.
- Komunikat o charakterze informacyjnym — problemy wpływające na operacji zarządzania i/lub opóźnienia, negatywnego wpływu na dostępność usług.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia usługi kondycji w witrynie Azure portal
1.  W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Monitor**.

    ![Zrzut ekranu platformy Azure z menu portalu z monitorem wybrane](./media/service-notifications/home-monitor.png)

    Usługa Azure Monitor gromadzi wszystkie ustawienia monitorowania i danych w jednym skonsolidowanym widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

3.  Wybierz **alerty**.

    ![Zrzut ekranu monitorowanie działań zdarzenia z dziennika wybrane alerty](./media/service-notifications/service-health-summary.png)
4. Wybierz **+ Dodaj alert dziennika aktywności**i ustawić alert, aby upewnić się, zostanie wyświetlone powiadomienie dla przyszłej obsługi powiadomień. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności dla powiadomień dotyczących usług](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Kolejne kroki
Odbieranie [alertu powiadomień przy każdym powiadomienia kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) zostanie opublikowany.  
Dowiedz się więcej o [alertów dziennika aktywności](../../azure-monitor/platform/activity-log-alerts.md).

