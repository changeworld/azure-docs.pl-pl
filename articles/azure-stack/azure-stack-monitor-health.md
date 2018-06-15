---
title: Monitorowanie kondycji i alertów w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie monitorowania kondycji i alertów w stosie Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: 446df7922422ccfcf3fbb92ecf153c6dec2f6197
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26640382"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorowanie kondycji i alertów w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Stos Azure obejmuje monitorowanie możliwości, które umożliwiają wyświetlanie kondycji i alerty dla regionu Azure stosu infrastruktury. **Zarządzania Region** kafelka, przypięty domyślnie w portalu administratora subskrypcji dostawcy domyślnego, zawiera listę wszystkich wdrożonych regionów stosu Azure. Kafelek pokazuje liczbę aktywnych alertów krytycznego i ostrzeżenia dla każdego regionu i jest punktem wejścia do kondycji i alertów funkcji Azure stosu.

 ![Na kafelku zarządzania regionu](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Zrozumienie kondycji w stosie Azure

 Kondycja i alerty są zarządzane przez dostawcę zasobów kondycji. Składniki infrastruktury w usłudze Azure stosu zarejestrować u dostawcy zasobów kondycji podczas wdrażania stosu Azure i konfiguracji. Rejestracja Umożliwia wyświetlanie kondycji i alertów dla poszczególnych składników. Kondycji w stosie Azure to pojęcie proste. Jeśli istnieją alerty dotyczące zarejestrowanych wystąpienia składnika, kondycja tego składnika odzwierciedla najgorszy active ważność alertu; Ostrzeżenie, lub krytyczne.
 
 ## <a name="view-and-manage-component-health-state"></a>Wyświetl i zarządzaj nimi stan kondycji składników
 
 Jako operator stosu Azure można wyświetlić stan kondycji składników, w portalu administratora i za pośrednictwem interfejsu API REST i programu PowerShell.
 
Aby wyświetlić stan kondycji w portalu, kliknij obszar, który chcesz wyświetlić w **zarządzania Region** kafelka. Można wyświetlić stan kondycji ról infrastruktury i dostawców zasobów.

![Lista ról infrastruktury](media/azure-stack-monitor-health/image2.png)

Możesz kliknąć dostawcy zasobów lub rolę infrastruktury, aby wyświetlić bardziej szczegółowe informacje.

> [!WARNING]
>Jeśli kliknij rolę infrastruktury, a następnie kliknij wystąpienia roli, istnieją opcje, aby rozpocząć, ponowne uruchomienie lub zamknięcie. Nie należy używać tych działań, podczas stosowania aktualizacji do zintegrowany system. Ponadto należy **nie** korzystać z tych opcji w środowisku Azure stosu Development Kit. Te opcje są przeznaczone tylko dla systemów zintegrowanego środowiska, gdy istnieje więcej niż jedno wystąpienie roli dla każdej roli infrastruktury. Ponowne uruchomienie wystąpienia roli (szczególnie Xrp01 AzS) w zestawie powoduje niestabilność systemu. Do rozwiązywania problemów pomocy, post problem do [forum Azure Stack](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Wyświetlanie alertów

Lista aktywnych alertów dla każdego regionu Azure stosu jest dostępny bezpośrednio z **zarządzania Region** bloku. Pierwszy Kafelek w domyślnej konfiguracji jest **alerty** kafelka, który zawiera podsumowanie krytyczne i ostrzeżenia alerty dla tego obszaru. Można przypiąć Kafelek alerty, podobnie jak inne kafelka na ten blok do pulpitu nawigacyjnego, aby uzyskać szybki dostęp.   

![Alerty kafelka, który wyświetla ostrzeżenie](media/azure-stack-monitor-health/image3.png)

Po wybraniu w górnej części **alerty** kafelka, przejdź do listy wszystkich aktywnych alertów dla tego obszaru. Jeśli zostanie wybrana **krytyczny** lub **ostrzeżenie** wiersza elementu w obrębie fragmentu, przejdź do listy filtrowane alertów (krytyczny lub ostrzeżenie). 

![Filtrowane alerty ostrzegawcze](media/azure-stack-monitor-health/image4.png)
  
**Alerty** bloku obsługuje możliwość filtrowania zarówno na stan (aktywny lub zamknięte) i ważności (krytyczna lub ostrzeżenie). Domyślny widok przedstawia wszystkie aktywne alerty. Wszystkie zamknięte alerty zostaną usunięte z systemu po upływie siedmiu dni.

![Okienko filtru do filtrowania według krytyczny lub stan ostrzeżenia](media/azure-stack-monitor-health/image5.png)

**API widoku** akcji wyświetla interfejsu API REST, który został użyty do generowania widoku listy. Ta akcja umożliwia szybkie zapoznać się z składnię interfejsu API REST, która służy do alertów zapytania. Można użyć tego interfejsu API w usłudze Automatyzacja lub w celu integracji z istniejących centrum danych monitorowania, raportowania i tworzenia biletów rozwiązania. 

![Opcja interfejsu API widok pokazujący interfejsu API REST](media/azure-stack-monitor-health/image6.png)

Możesz kliknąć określony alert, aby wyświetlić szczegóły alertu. Szczegóły alertu zawierają wszystkie pola, które są skojarzone z alertem i Włącz szybkie nawigacji składnik i źródła alertu. Na przykład następujący alert występuje, jeśli jedno wystąpienie roli infrastruktury przejdzie do trybu offline lub jest niedostępny.  

![Blok szczegółów alertu](media/azure-stack-monitor-health/image7.png)

Po powrocie do trybu online wystąpienia roli infrastruktury ten alert jest automatycznie zamykany. Wiele, ale nie każdy alert zamyka automatycznie, gdy problem zostanie rozwiązany. Zaleca się, że wybrano **Zamknij Alert** po wykonaniu czynności korygujące. Jeśli problem będzie się powtarzać, stos Azure generuje nowy alert. Jeśli ten problem można rozwiązać, alert pozostanie zamknięty i nie wymaga dodatkowych czynności.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie aktualizacjami w stosie Azure](azure-stack-updates.md)

[Zarządzanie regionu Azure stosu](azure-stack-region-management.md)
