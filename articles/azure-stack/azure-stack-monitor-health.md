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
ms.date: 9/10/2018
ms.author: mabrigg
ms.openlocfilehash: 69ed08e8f6c820790c432bfa25988e038fd0efbd
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379693"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorowanie kondycji i alertów w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack obejmuje funkcje, które umożliwiają wyświetlanie kondycji i alertów dla regionu Azure Stack do monitorowania infrastruktury. **Zarządzanie regionami** Kafelek przypięty domyślnie w portalu administratora subskrypcji dostawcy domyślnego, wyświetla listę wszystkich wdrożonych regionów usługi Azure Stack. Kafelek pokazuje liczbę aktywnych alertów krytycznego i ostrzeżenia dla każdego regionu, a jest punktem wejścia do kondycji i alertów funkcje usługi Azure Stack.

 ![Kafelek zarządzanie regionami](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Zrozumienie kondycji w usłudze Azure Stack

 Kondycji i alertów są zarządzane przez dostawcę zasobów kondycji. Składniki infrastruktury usługi Azure Stack zarejestrować za pomocą dostawcy zasobów kondycji podczas wdrażania usługi Azure Stack i konfiguracji. Rejestracja Umożliwia wyświetlanie kondycji i alertów dla każdego składnika. Kondycja usługi Azure Stack jest prosta koncepcja. Jeśli istnieją alerty dotyczące zarejestrowanych wystąpienia składnika, stan kondycji tego składnika odzwierciedla najgorszy active ważność alertu; Ostrzeżenie, lub krytyczne.

## <a name="alert-severity-definition"></a>Definicja ważność alertu

W usłudze Azure Stack alerty są wywoływane przy użyciu tylko dwa poziomy ważności: **ostrzeżenie** i **krytyczne**.

- **Ostrzeżenie**  
  Operator może zająć alert ostrzeżenia, w sposób, według harmonogramu. Alert zwykle nie ma wpływu na obciążenia użytkownika.

- **Krytyczne**  
  Operator powinien adresów alert krytyczny o pilność. Są to problemy, które obecnie wpływ lub wkrótce będzie miało wpływ na użytkowników usługi Azure Stack. 

 
 ## <a name="view-and-manage-component-health-state"></a>Wyświetlanie i zarządzanie nimi stan kondycji składników
 
 Jako operatorów usługi Azure Stack można wyświetlić stan kondycji składników, w portalu administratora i za pośrednictwem interfejsu API REST i PowerShell.
 
Aby wyświetlić stan kondycji w witrynie portal, kliknij go, którą chcesz wyświetlić w **zarządzanie regionami** kafelka. Można wyświetlić stan kondycji infrastruktury ról i dostawców zasobów.

![Lista ról infrastruktury](media/azure-stack-monitor-health/image2.png)

Możesz kliknąć dostawcy zasobów lub roli infrastruktury, aby wyświetlić bardziej szczegółowe informacje.

> [!WARNING]  
> Kliknij rolę infrastruktury, a następnie kliknij wystąpienie roli, czy opcje uruchamianie, ponowne uruchomienie lub zamknięcie. Nie należy używać tych akcji, po zastosowaniu aktualizacji do system zintegrowany. Ponadto wykonaj **nie** użyć tych opcji w środowisku usługi Azure Stack Development Kit. Te opcje są przeznaczone tylko dla środowiska zintegrowane systemy, w przypadku, gdy istnieje więcej niż jedno wystąpienie roli dla roli infrastruktury. Ponowne uruchamianie wystąpienia roli (szczególnie AzS-Xrp01) w zestawie powoduje niestabilność systemu. Rozwiązaniu tego problemu, Opublikuj problemu do [forum usługi Azure Stack](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Wyświetlanie alertów

Lista aktywnych alertów dla każdego regionu Azure Stack jest dostępna bezpośrednio z **zarządzanie regionami** bloku. Pierwszy Kafelek w domyślnej konfiguracji jest **alerty** Kafelek, który zawiera podsumowanie krytycznych i alerty ostrzegawcze dla regionu. Można przypiąć kafelka alerty, podobnie jak inne kafelka, w tym bloku do pulpitu nawigacyjnego, aby uzyskać szybki dostęp.   

![Alerty Kafelek, który wyświetla ostrzeżenie](media/azure-stack-monitor-health/image3.png)

Wybierając w górnej części **alerty** kafelka, przejdź do listy wszystkich aktywnych alertów dla regionu. Jeśli wybierzesz opcję **krytyczny** lub **ostrzeżenie** element wiersza we fragmencie, przejdź do filtrowana lista alertów (krytyczny lub ostrzegawczy). 

![Filtrowane alerty ostrzegawcze](media/azure-stack-monitor-health/image4.png)
  
**Alerty** bloku obsługuje możliwość filtrowania zarówno na stan (aktywne lub zamknięte) i ważności (krytyczny lub ostrzegawczy). Domyślny widok przedstawia wszystkie aktywne alerty. Wszystkie zamknięte alerty zostaną usunięte z systemu po upływie siedmiu dni.

![Okienko filtru, aby filtrować według krytyczny lub stan ostrzeżenia](media/azure-stack-monitor-health/image5.png)

**API widoku** akcja wyświetla interfejsu API REST, który został użyty do wygenerowania widoku listy. Ta akcja umożliwia szybkie zapoznanie się ze składnią interfejsu API REST używanego do kwerendy alertów. Można użyć tego interfejsu API w usłudze automation lub integracji, za pomocą swojego istniejącego centrum danych monitorowania, raportowania i tworzenia biletów rozwiązania. 

![Opcję Widok interfejsu API, która wyświetla interfejsu API REST](media/azure-stack-monitor-health/image6.png)

Możesz kliknąć określony alert, aby wyświetlić szczegóły alertu. Szczegóły alertu pokazują wszystkie pola, które są skojarzone z alertem i umożliwiają szybkie nawigowanie do źródła alertu i składnik. Na przykład następujący alert występuje, jeśli jedno z wystąpień roli infrastruktury przejdzie do trybu offline lub jest niedostępny.  

![W bloku szczegóły alertu](media/azure-stack-monitor-health/image7.png)

Po powrocie do trybu online wystąpienia roli infrastruktury ten alert jest automatycznie zamyka. Wiele, ale nie każdy alert automatycznie zamyka się, gdy problem zostanie rozwiązany. Zaleca się, że wybrano **Zamknij Alert** po wykonaniu czynności zaradczych. Jeśli problem będzie się powtarzać, usługi Azure Stack generuje nowy alert. Jeśli możesz rozwiązać ten problem, alert pozostanie zamknięte i nie wymaga dodatkowych czynności.

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)

[Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)
