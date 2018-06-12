---
title: Co to są powiadomienia o kondycji usługi platformy Azure?
description: Powiadomienia o kondycji usługi umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowana przez Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264446"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień o kondycji usługi za pomocą portalu Azure

Powiadomienia o kondycji usługi są publikowane przez usługę Azure i zawiera informacje dotyczące zasobów w ramach Twojej subskrypcji. Te powiadomienia są zdarzenia dziennika podklasą klasy działania i można znaleźć w dzienniku aktywności. Powiadomienia o kondycji usługi może być informacyjne lub przydatnych wyników, w zależności od tej klasy.

Istnieją różne rodzaje powiadomień o kondycji usługi:  

- **Wymagana akcja:** Azure zauważyć coś nietypowego w stanie Twojego konta i współpraca z Tobą, aby rozwiązać ten problem. Azure wysyła powiadomienie, albo wyszczególnieniem akcje, które należy wykonać lub sposób kontaktu z Azure engineering lub pomocy technicznej.  
- **Asystowane odzyskiwania:** wystąpiło zdarzenie i inżynierów potwierdzeniu, że nadal występują wpływu. Azure engineering musi pracować bezpośrednio w celu przywrócenia usługi pełną kondycję.  
- **Zdarzenie:** zdarzenie, które ma wpływ na usługi jest obecnie mające wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Konserwacja:** działanie zaplanowanej konserwacji, które mogą mieć wpływ na co najmniej jeden z zasobów w ramach Twojej subskrypcji.  
- **Informacje o:** potencjalne funkcje optymalizacji, które mogą pomóc zwiększyć użytkowania zasobów. 
- **Zabezpieczenia:** pilnych informacji związanych z zabezpieczeniami dotyczące rozwiązań uruchamianych na platformie Azure.

Każde powiadomienie o kondycji usługi zawiera szczegółowe informacje o zakres i znaczenie dla zasobów. Szczegółowe informacje obejmują:

Nazwa właściwości | Opis
-------- | -----------
kanały | Jedną z następujących wartości: **Admin** lub **operacji**.
correlationId | Zazwyczaj identyfikator GUID w postaci ciągu. Zdarzenia, które należą do tego samego działania zwykle udostępnianie tego samego correlationId.
eventDataId | Unikatowy identyfikator zdarzenia.
EventName | Tytuł zdarzenia.
poziom | Poziom zdarzenia
resourceProviderName | Nazwa dostawcy zasobów dla zasobu objęte wpływem.
Typ zasobu| Typ zasobu ryzyko zasobu.
subStatus | Zazwyczaj REST odpowiedni kod stanu HTTP wywołań, ale można również uwzględnić inne parametry opisujące podstanu. Na przykład: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), akceptowane (kod stanu HTTP: 202), nie zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409), serwer wewnętrzny Błąd (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504).
eventTimestamp | Sygnatura czasowa po zdarzeniu został wygenerowany przez usługę Azure przetwarzania żądania odpowiadający zdarzenia.
submissionTimestamp | Znacznik czasu w momencie zdarzenia stały się dostępne na potrzeby zapytań.
subscriptionId | Subskrypcja platformy Azure, w którym zarejestrowano to zdarzenie.
status | Ciąg opisujący stan operacji. Niektóre typowe wartości to: **uruchomiono**, **w toku**, **zakończyło się pomyślnie**, ****, **Active**, i **Rozpoznać**.
operationName | Nazwa operacji.
category | Ta właściwość jest zawsze **ServiceHealth**.
resourceId | Identyfikator zasobu ryzyko zasobu.
Properties.Title | Tytuł zlokalizowane dla tej komunikacji. Domyślnym będzie angielski.
Properties.Communication | Szczegóły zlokalizowanych komunikacji z kodu znaczników HTML. Domyślnym będzie angielski.
Properties.incidentType | Jedną z następujących wartości: **ActionRequired**, **informacji**, **zdarzenia**, **konserwacji**, lub **zabezpieczeń**.
Properties.trackingId | Zdarzenia, z którą jest skojarzone to zdarzenie. Umożliwia korelowanie zdarzeń związanych ze zdarzeniem.
Properties.impactedServices | Zmienionym obiektu blob JSON opisujący usługi i regiony wpływa zdarzenie. Właściwość zawiera listę usług, z których każda ma **ServiceName**oraz listę ryzyko regionów, z których każda ma **RegionName**.
Properties.defaultLanguageTitle | Komunikacja w języku angielskim.
Properties.defaultLanguageContent | Komunikacja w języku angielskim jako kod znaczników HTML i zwykły tekst.
Properties.Stage | Możliwe wartości **zdarzenia**, i **zabezpieczeń** są **aktywny,** **rozwiązane** lub **RCA**. Dla **ActionRequired** lub **informacji** jest to jedyna wartość **aktywne.** Dla **konserwacji** są: **Active**, **planowane**, **w toku**, **anulowane**, **Zmienił**, **rozpoznać**, lub **pełną**.
Properties.communicationId | Komunikacja, z którą jest skojarzone to zdarzenie.

### <a name="details-on-service-health-level-information"></a>Szczegóły na poziomie informacje o kondycji usługi
  <ul>
    <li><b>Wymagana akcja</b> (properties.incidentType == ActionRequired) <dl>
            <dt>Informacyjny</dt>
            <dd>Czynności wykonywane przez administratora wymagane w celu ograniczenia wpływu na istniejące usługi</dd>
        </dl>
    </li>
    <li><b>Konserwacja</b> (properties.incidentType == konserwacji) <dl>
            <dt>Ostrzeżenie</dt>
            <dd>pilnej konserwacji<dd>
            <dt>Informacyjny</dt>
            <dd>Standardowa zaplanowanej konserwacji</dd>
        </dl>
    </li>
    <li><b>Informacje o</b> (properties.incidentType == informacji) <dl>
            <dt>Informacyjny</dt>
            <dd>Aby uniknąć wpływu na istniejące usługi może być konieczne administratora</dd>
        </dl>
    </li>
    <li><b>Zabezpieczenia</b> (properties.incidentType == zabezpieczeń) <dl>
            <dt>Błąd</dt>
            <dd>Powszechne problemy podczas uzyskiwania dostępu do wielu usług w wielu regionach wpływają na szerokiego zakresu klientów.</dd>
            <dt>Ostrzeżenie</dt>
            <dd>Problemy dotyczące uzyskiwania dostępu do określonych usług i/lub w określonych regionach wpływają na podzestawie klientów.</dd>
            <dt>Informacyjny</dt>
            <dd>Problemów wpływających na operacje zarządzania i/lub opóźnienia, nie wpływu na dostępność usługi.</dd>
        </dl>
    </li>
    <li><b>Usługa problemów</b> (properties.incidentType == zdarzenie) <dl>
            <dt>Błąd</dt>
            <dd>Powszechne problemy podczas uzyskiwania dostępu do wielu usług w wielu regionach wpływają na szerokiego zakresu klientów.</dd>
            <dt>Ostrzeżenie</dt>
            <dd>Problemy dotyczące uzyskiwania dostępu do określonych usług i/lub w określonych regionach wpływają na podzestawie klientów.</dd>
            <dt>Informacyjny</dt>
            <dd>Problemów wpływających na operacje zarządzania i/lub opóźnienia, nie wpływu na dostępność usługi.</dd>
        </dl>
    </li>
  </ul>

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Wyświetl powiadomienia usługi kondycji w portalu Azure
1.  W [portalu Azure](https://portal.azure.com), wybierz pozycję **Monitor**.

    ![Zrzut ekranu Azure menu portalu z monitorem wybrane](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor zgromadzono wszystkich monitorowania ustawień i danych do jednego, skonsolidowanego widoku. Na początku widoczna jest sekcja **Dziennik aktywności**.

3.  Wybierz **alerty**.

    ![Zrzut ekranu działania monitorowania dziennika wybrane alerty](./media/monitoring-service-notifications/service-health-summary.png)
4. Wybierz **+ Dodaj działanie dziennika alert**i ustawić alert, aby upewnić się, zostanie wyświetlone powiadomienie powiadomień usługi w przyszłości. Aby uzyskać więcej informacji, zobacz [tworzyć alerty dziennika działania dotyczące powiadomień usługi](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Kolejne kroki
Odbieranie [alertów powiadomienia, gdy powiadomienie o kondycji usługi](monitoring-activity-log-alerts-on-service-notifications.md) zamieszczony.  
Dowiedz się więcej o [alertów dotyczących działań w dzienniku](monitoring-activity-log-alerts.md).
