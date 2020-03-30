---
title: Co to są powiadomienia o kondycji usługi platformy Azure?
description: Powiadomienia o kondycji usługi umożliwiają wyświetlanie komunikatów o kondycji usługi opublikowanych przez platformę Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653972"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usługi są publikowane przez platformę Azure i zawierają informacje o zasobach w ramach subskrypcji. Te powiadomienia są podklasą zdarzeń dziennika aktywności i można również znaleźć w dzienniku działań. Powiadomienia o kondycji usługi mogą być informacyjne lub zasłużenia, w zależności od klasy.

Istnieją różne klasy powiadomień o kondycji usługi:  

- **Wymagane działanie:** Platforma Azure może zauważyć coś niezwykłego na twoim koncie i współpracować z Tobą, aby temu zaradzić. Platforma Azure wysyła powiadomienie, zawierające szczegółowe informacje o działaniach, które należy wykonać, lub jak skontaktować się z inżynierami lub pomocą techniczną platformy Azure.  
- **Incydent:** Zdarzenie, które ma wpływ na usługę, ma obecnie wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Konserwacja:** Planowane działanie konserwacyjne, które może mieć wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Informacje:** Potencjalne optymalizacje, które mogą pomóc w zwiększce wykorzystania zasobów. 
- **Bezpieczeństwo:** Pilne informacje związane z zabezpieczeniami dotyczące rozwiązań uruchamianych na platformie Azure.

Każde powiadomienie o kondycji usługi zawiera szczegółowe informacje na temat zakresu i wpływu na zasoby. Szczegóły obejmują:

Nazwa właściwości | Opis
-------- | -----------
Kanały | Jedna z następujących wartości: **Admin** lub **Operation**.
correlationId | Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia, które należą do tej samej akcji zwykle współużytkują ten sam correlationId.
identyfikator danych zdarzeń | Unikatowy identyfikator zdarzenia.
Eventname | Tytuł wydarzenia.
poziom | Poziom zdarzenia
nazwa zasobu | Nazwa dostawcy zasobów dla zasobu, do który ma wpływ.
resourceType| Typ zasobu, do który ma wpływ.
Podstanu | Zazwyczaj kod stanu HTTP odpowiedniego wywołania REST, ale może również zawierać inne ciągi opisujące podstatus. Na przykład: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowany (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), nieprawidłowe żądanie (kod stanu HTTP: 400), nieodebrania (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409), serwer wewnętrzny Błąd (kod stanu HTTP: 500), usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504).
eventTimestamp | Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzania żądania odpowiadającego zdarzenia.
submissionTimestamp | Sygnatura czasowa, gdy zdarzenie stało się dostępne do wykonywania zapytań.
subscriptionId | Subskrypcja platformy Azure, w której zarejestrowano to zdarzenie.
status | Ciąg opisujący stan operacji. Niektóre typowe wartości to: **Rozpoczęty**, **W toku**, **Powiódł się,** **Nie powiodło się,** **Aktywny**i **Rozwiązany**.
operationName | Nazwa operacji.
category | Ta właściwość jest zawsze **ServiceHealth**.
resourceId | Identyfikator zasobu, na który ma wpływ.
Właściwości.title | Zlokalizowany tytuł dla tej komunikacji. Domyślny jest angielski.
Właściwości.komunikacja | Zlokalizowane szczegóły komunikacji za pomocą znaczników HTML. Domyślny jest angielski.
Właściwości.incidentType | Jedna z następujących wartości: **Wymagana akcja,** **Informacja,** **Incydent,** **Konserwacja**lub **Bezpieczeństwo.**
Identyfikator właściwości.tracking | Zdarzenie, z którym jest skojarzone to zdarzenie. Użyj tego, aby skorelować zdarzenia związane ze zdarzeniem.
Właściwości.impactedServices | Obiekt blob usługi JSON, który opisuje usługi i regiony, których dotyczy zdarzenie. Właściwość zawiera listę usług, z których każda ma **ServiceName**, i listę regionów, których dotyczy problem, z których każdy ma **RegionName**.
Właściwości.defaultLanguageTitle | Komunikat w języku angielskim.
Właściwości.defaultLanguageContent | Komunikacja w języku angielskim jako znaczniki HTML lub zwykły tekst.
Właściwości.etap | Możliwe wartości **dla incident**i **security** są **aktywne,** **rozwiązane** lub **RCA**. W przypadku **akcji wymaganych** lub **informacyjnych** jedyną wartością jest **Active.** W przypadku **konserwacji** są to: **Aktywne,** **Planowane,** **InProgress,** **Anulowane,** **Przełożone,** **Rozwiązane**lub **Zakończone**.
Obiekt Właściwości.communicationId | Komunikacja, z którą jest skojarzone to zdarzenie.

### <a name="details-on-service-health-level-information"></a>Szczegółowe informacje na temat poziomu kondycji usług

**Wymagana akcja** (properties.incidentType == Wymagana akcja)
- Informacje — akcja administratora jest wymagana, aby zapobiec wpływowi na istniejące usługi.
    
**Konserwacja** (properties.incidentType == Konserwacja)
- Ostrzeżenie - Konserwacja awaryjna
- Informacje — standardowa planowana konserwacja

**Informacje** (properties.incidentType == Informacje)
- Informacje — administrator może być wymagany, aby zapobiec wpływowi na istniejące usługi.

**Zabezpieczenia** (właściwości.incidentType == Zabezpieczenia)
- Ostrzeżenie — poradnik zabezpieczeń, który wpływa na istniejące usługi i może wymagać działania administratora.
- Informacyjny — poradnik zabezpieczeń, który wpływa na istniejące usługi.

**Problemy z usługą** (properties.incidentType == Incydent)
- Błąd — powszechne problemy z dostępem do wielu usług w wielu regionach mają wpływ na szeroki zestaw klientów.
- Ostrzeżenie — problemy z dostępem do określonych usług i/lub określonych regionów mają wpływ na podzbiór klientów.
- Informacje — problemy wpływające na operacje zarządzania i/lub opóźnienia, nie wpływające na dostępność usługi.
