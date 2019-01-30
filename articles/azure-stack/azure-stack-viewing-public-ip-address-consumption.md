---
title: Wyświetlanie zużycia publicznego adresu IP w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Administratorzy mogą wyświetlać użycie publicznych adresów IP w regionie
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d97674940f0af91bf50af87cfe96fda9644d469b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242056"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Wyświetlanie zużycia publicznego adresu IP w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Jako administrator chmury możesz wyświetlić:
 - Liczba publicznych adresów IP, które zostały przydzielone do dzierżawców.
 - Liczba publicznych adresów IP, które są nadal dostępne do alokacji.
 - Wartość procentowa publicznych adresów IP, które zostały przydzielone w tej lokalizacji.

**Publiczny adres IP pul użycia** Kafelek zawiera liczbę publiczne adresy IP używane przez publiczny pule adresów IP. Dla każdego adresu IP Kafelek pokazuje użycie dla dzierżawy maszyn wirtualnych IaaS, wystąpień, usług infrastruktury sieci szkieletowej oraz zasoby publicznych adresów IP, jawnie utworzonych przez dzierżawców.

Kafelek ma na celu udzielić operatorom usługi Azure Stack zorientować się, liczba publicznych adresów IP używanych w tej lokalizacji. Liczba pomaga ustalić, czy ich kończy się na tym zasobie Administratorzy.

**Publiczne adresy IP** elementu menu, w obszarze **zasobów dzierżawy** wyświetla tylko publicznych adresów IP, które zostały *jawnie utworzony przez dzierżawców*. Element menu można znaleźć na **dostawców zasobów**, **sieci** okienka. Liczba **używane** publiczne adresy IP na **publiczny adres IP pul użycia** kafelka zawsze różni się od (większe niż) liczba na **publiczne adresy IP** kafelka w obszarze  **Zasoby dzierżawcy**.

## <a name="view-the-public-ip-address-usage-information"></a>Wyświetl publiczne informacje o użyciu adresu IP

Aby wyświetlić sumę publiczne adresy IP, które zostały wykorzystane w regionie:

1. W portalu administratora usługi Azure Stack, wybierz **wszystkich usług**. Następnie w obszarze **administracji** wybierz kategorię **sieci**.
1. **Sieci** okienko wyświetla **publiczny adres IP pul użycia** Kafelek w **Przegląd** sekcji.

![Okienko dostawca zasobów sieciowych](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**Używane** numer reprezentuje liczbę przydzielanych publicznych adresów IP z publicznych pule adresów IP. **Bezpłatna** numer reprezentuje numer publiczny adres IP adresów z publicznym adresem IP adresów pule, które nie zostały przypisane i są nadal dostępne. **Wykorzystania** numer reprezentuje liczbę używane lub przypisane adresy jako procent całkowitej liczby publicznych adresów IP w publicznych pul adresów IP w tej lokalizacji.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Wyświetl publiczne adresy IP, które zostały utworzone przez subskrypcji dzierżawcy

Wybierz **publiczne adresy IP** w obszarze **zasobów dzierżawy**. Przejrzyj listę publicznych adresów IP, w sposób jawny nie utworzył według subskrypcji dzierżawcy w określonym regionie.

![Publiczne adresy IP dzierżawy](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Może się okazać, że niektóre publiczne adresy IP, które są dynamicznie przydzielane są wyświetlane na liście. Jednak adres nie został skojarzony z ich jeszcze. Dostawca zasobów sieciowych, ale nie są jeszcze w kontroler sieci został utworzony zasób adresu.

Kontroler sieci nie ma przypisanego adresu do zasobu, dopóki powiąże interfejs, karta interfejsu sieciowego (NIC), moduł równoważenia obciążenia lub bramy sieci wirtualnej. Gdy wiąże publiczny adres IP do interfejsu, Kontroler sieci przydziela adresu IP. Adres będzie widoczny w **adres** pola.

## <a name="view-the-public-ip-address-information-summary-table"></a>Widok publiczny tabelę adresów IP informacje podsumowania

W różnych przypadkach publiczne adresy IP są przypisywane, które określają, czy adres pojawia się w jednej liście lub w innym.

| **Publiczne przypadek przypisanie adresu IP** | **Zostanie wyświetlone podsumowanie użycia** | **Pojawia się w dzierżawie listy publicznych adresów IP** |
| --- | --- | --- |
| Jeszcze nie zostały przypisane do karty Sieciowej lub moduł równoważenia obciążenia (tymczasowe) dynamicznego publicznego adresu IP |Nie |Yes |
| Dynamiczny publiczny adres IP przypisany do karty Sieciowej lub moduł równoważenia obciążenia. |Yes |Yes |
| Statyczny publiczny adres IP przypisany do dzierżawy kart interfejsu Sieciowego lub Usługa równoważenia obciążenia. |Yes |Yes |
| Statyczny publiczny adres IP przypisany do punktu końcowego usługi infrastruktury sieci szkieletowej. |Yes |Nie |
| Publiczny adres IP niejawnie tworzone dla wystąpień maszyn wirtualnych IaaS i używane dla NAT dla ruchu wychodzącego w sieci wirtualnej. Są one tworzone w tle zawsze wtedy, gdy dzierżawy tworzy wystąpienie maszyny Wirtualnej, tak aby maszyny wirtualne można wysyłać informacje do Internetu. |Yes |Nie |

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie kontami magazynu w usłudze Azure Stack](azure-stack-manage-storage-accounts.md)