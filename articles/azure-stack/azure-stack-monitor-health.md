---
title: Monitorowanie kondycji i alertów w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie kondycji i alertów w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.openlocfilehash: 9a925a01cae75124dc56b0c2bc5cc931a6e04100
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721618"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorowanie kondycji i alertów w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack obejmuje monitorowania infrastruktury funkcji, które ułatwiają wyświetlanie kondycji i alertów dla regionu Azure Stack. **Zarządzanie regionami** Kafelek przypięty domyślnie w portalu administratora subskrypcji dostawcy domyślnego, wyświetla listę wszystkich wdrożonych regionów usługi Azure Stack. Kafelek zawiera liczbę aktywnych alertów krytycznego i ostrzeżenia dla każdego regionu. Kafelek jest punktem wejścia do kondycji i alertów funkcje usługi Azure Stack.

![Kafelek zarządzanie regionami](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Zrozumienie kondycji w usłudze Azure Stack

Dostawca zasobów zdrowia zarządza kondycji i alertów. Składniki infrastruktury usługi Azure Stack zarejestrować za pomocą dostawcy zasobów kondycji podczas wdrażania usługi Azure Stack i konfiguracji. Rejestracja Umożliwia wyświetlanie kondycji i alertów dla każdego składnika. Kondycja usługi Azure Stack jest prosta koncepcja. Jeśli istnieją alerty dotyczące zarejestrowanych wystąpienia składnika, stan kondycji tego składnika odzwierciedla najgorszy active ważność alertu: ostrzegawczy lub krytyczny.

## <a name="alert-severity-definition"></a>Definicja ważność alertu

W usłudze Azure Stack zgłasza alerty tylko dwa poziomy ważności: **ostrzeżenie** i **krytyczne**.

- **Ostrzeżenie**  
  Operator może zająć alert ostrzeżenia, w sposób, według harmonogramu. Alert zwykle nie ma wpływu na obciążenia użytkownika.

- **Krytyczne**  
  Operator powinien adresów alert krytyczny o pilność. Są to problemy, które obecnie wpływ lub wkrótce będzie miało wpływ na użytkowników usługi Azure Stack.


## <a name="view-and-manage-component-health-state"></a>Wyświetlanie i zarządzanie nimi stan kondycji składników

Stan kondycji składników można wyświetlić w portalu administratora i za pośrednictwem interfejsu API REST i PowerShell.

Aby wyświetlić stan kondycji w witrynie portal, kliknij go, którą chcesz wyświetlić w **zarządzanie regionami** kafelka. Można wyświetlić stan kondycji infrastruktury ról i dostawców zasobów.

![Lista ról infrastruktury](media/azure-stack-monitor-health/image2.png)

Możesz kliknąć dostawcy zasobów lub roli infrastruktury, aby wyświetlić bardziej szczegółowe informacje.

> [!WARNING]  
> Jeśli kliknij rolę infrastruktury, a następnie kliknij wystąpienie roli, dostępne są opcje **Start**, **ponowne uruchomienie**, lub **zamknięcia**. Nie należy używać tych akcji, po zastosowaniu aktualizacji do system zintegrowany. Ponadto wykonaj **nie** użyć tych opcji w środowisku usługi Azure Stack Development Kit. Te opcje są tylko przeznaczone dla środowiska zintegrowane systemy usługi w przypadku, gdy istnieje więcej niż jedno wystąpienie roli dla roli infrastruktury. Ponowne uruchamianie wystąpienia roli (szczególnie AzS-Xrp01) w zestawie powoduje niestabilność systemu. Rozwiązaniu tego problemu, Opublikuj problemu do [forum usługi Azure Stack](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Wyświetlanie alertów

Lista aktywnych alertów dla każdego regionu Azure Stack jest dostępna bezpośrednio z **zarządzanie regionami** bloku. Pierwszy Kafelek w domyślnej konfiguracji jest **alerty** Kafelek, który zawiera podsumowanie krytycznych i alerty ostrzegawcze dla regionu. Można przypiąć kafelka alerty, podobnie jak inne kafelka, w tym bloku do pulpitu nawigacyjnego, aby uzyskać szybki dostęp.

![Alerty Kafelek, który wyświetla ostrzeżenie](media/azure-stack-monitor-health/image3.png)

Wybierając w górnej części **alerty** kafelka, przejdź do listy wszystkich aktywnych alertów dla regionu. Jeśli wybierzesz opcję **krytyczny** lub **ostrzeżenie** element wiersza we fragmencie, przejdź do filtrowana lista alertów (krytyczny lub ostrzegawczy). 

**Alerty** bloku obsługuje możliwość filtrowania zarówno na stan (aktywne lub zamknięte) i ważności (krytyczny lub ostrzegawczy). Domyślny widok przedstawia wszystkie aktywne alerty. Wszystkie zamknięte alerty zostaną usunięte z systemu po upływie siedmiu dni.

![Okienko filtru, aby filtrować według krytyczny lub stan ostrzeżenia](media/azure-stack-monitor-health/alert-view.png)

**API widoku** akcja wyświetla interfejsu API REST, który został użyty do wygenerowania widoku listy. Ta akcja umożliwia szybkie zapoznanie się ze składnią interfejsu API REST używanego do kwerendy alertów. Można użyć tego interfejsu API w usłudze automation lub integracji, za pomocą swojego istniejącego centrum danych monitorowania, raportowania i tworzenia biletów rozwiązania.

Możesz kliknąć określony alert, aby wyświetlić szczegóły alertu. Szczegóły alertu pokazują wszystkie pola, które są skojarzone z alertem i umożliwiają szybkie nawigowanie do źródła alertu i składnik. Na przykład następujący alert występuje, jeśli jedno z wystąpień roli infrastruktury przejdzie do trybu offline lub jest niedostępny.  

![W bloku szczegóły alertu](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Napraw alertów

Możesz wybrać **naprawy** niektórych alertów.

Po wybraniu **naprawy** akcji wykonuje instrukcje określonego alertu, aby spróbować rozwiązać problem. Po wybraniu stanu **naprawy** akcja jest dostępna jako powiadomienie w portalu.

![Naprawa w toku](media/azure-stack-monitor-health/repair-in-progress.png)

**Naprawy** akcji informację o pomyślnym zakończeniu lub nie można ukończyć akcji, w tym samym bloku portalu powiadomień.  W przypadku niepowodzenia akcji naprawy dla alertu możesz ponownie uruchomić **naprawy** akcji w szczegółach alertu. Jeśli akcja naprawy zakończy się pomyślnie, **nie** ponownie **naprawy** akcji.

![Naprawy zakończy się pomyślnie](media/azure-stack-monitor-health/repair-completed.png)

Po powrocie do trybu online wystąpienia roli infrastruktury ten alert jest automatycznie zamyka. Automatycznie zamyka wiele, ale nie każdy alert, gdy problem zostanie rozwiązany. Alerty dostarczające przycisk akcji naprawy zostanie zamknięte automatycznie, jeśli usługi Azure Stack jest rozpoznawany jako ten problem.  W przypadku innych alertów, wybrać **Zamknij Alert** po wykonaniu czynności zaradczych. Jeśli problem będzie się powtarzać, usługi Azure Stack generuje nowy alert. Jeśli możesz rozwiązać ten problem, alert pozostanie zamknięte i wymaga nie dodatkowych czynności.

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)

[Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)
