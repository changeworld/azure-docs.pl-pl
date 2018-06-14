---
title: Wyświetl publicznych zużycia adresów IP w stosie Azure | Dokumentacja firmy Microsoft
description: Administratorzy mogą wyświetlać zużycie publicznych adresów IP w regionie
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742462"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Wyświetl publicznych zużycia adresów IP w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Administrator chmury można wyświetlić:
 - Liczba publicznych adresów IP, które zostały przydzielone do dzierżawców.
 - Liczba publicznych adresów IP, które są nadal dostępne do alokacji.
 - Wartość procentowa publicznych adresów IP, które zostały przydzielone w tej lokalizacji.

**Publicznego adresu IP do puli użycia** kafelka jest wyświetlana liczba publicznych adresów IP używane przez publiczny pule adresów IP. Dla każdego adresu IP kafelka pokazuje użycie dla dzierżawy maszyn wirtualnych IaaS wystąpień usług infrastruktury sieci szkieletowej i zasoby publicznych adresów IP, które jawnie zostały utworzone przez dzierżawców.

Kafelek ma na celu zapewniają operatory Azure stosu w pewnym sensie liczba publicznych adresów IP używane w tej lokalizacji. Liczba pomaga administratorom ustalić, czy działają na niski w przypadku tego zasobu.

**Publicznego adresu IP, adresy** elementu menu pod **zasoby dzierżawcy** wyświetla tylko te publiczne adresy IP, do których zostały *jawnie utworzonego przez dzierżawców*. Element menu można znaleźć na **dostawców zasobów**, **sieci** okienka. Liczba **używane** publicznego adresu IP, adresy na **publicznego adresu IP do puli użycia** kafelka zawsze różni się od (większe niż) numer na **publiczne adresy IP** kafelka w obszarze  **Zasoby dzierżawcy**.

## <a name="view-the-public-ip-address-usage-information"></a>Widok publiczne informacje o użyciu adresu IP
Aby wyświetlić całkowita liczba publicznych adresów IP, które zostały wykorzystane w regionie:

1. W portalu administratora stosu Azure wybierz **więcej usług**w obszarze **zasobów administracyjnych**, wybierz pozycję **dostawców zasobów**.
2. Z listy **dostawców zasobów**, wybierz pozycję **sieci**.
3. **Sieci** okienko zawierające **publicznego adresu IP do puli użycia** kafelka w **omówienie** sekcji.

![Okienko dostawcy zasobów sieciowych](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**Używane** numer reprezentuje liczbę przypisany publicznych adresów IP z publicznego pule adresów IP. **Wolne** liczba reprezentuje liczba publicznych adresów IP adresy z publicznym adresem IP adresów pule, które nie zostały przypisane i są nadal dostępne. **Wykorzystania** numer reprezentuje liczbę używane lub przypisać adresy jako procent całkowitej liczby publicznych adresów IP w publicznych pule adresów IP w tej lokalizacji.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Wyświetl publiczne adresy IP, które zostały utworzone przez subskrypcji dzierżawcy
Wybierz **publicznego adresu IP, adresy** w obszarze **zasoby dzierżawcy**. Zapoznaj się z listą publiczne adresy IP jawnie utworzone przez subskrypcji dzierżawcy w określonym regionie.

![Publiczne adresy IP dzierżawy](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Można zauważyć, że niektóre publiczne adresy IP, które dynamicznie przydzielane są wyświetlane na liście. Jednak adres nie został skojarzony z nimi jeszcze. Zasób Adres został utworzony w dostawcy zasobów sieciowych, ale nie została jeszcze w kontrolerze sieci.

Kontroler sieci nie przypisuje adres do zasobu, dopóki nie jest on powiązany z interfejsem, do karty interfejsu sieciowego (NIC), usługi równoważenia obciążenia lub bramy sieci wirtualnej. Gdy publiczny adres IP jest powiązany z interfejs, Kontroler sieci przydziela adres IP. Adres pojawia się w **adres** pola.

## <a name="view-the-public-ip-address-information-summary-table"></a>Widok publiczny tabelę adresów IP informacji podsumowania
W przypadku różnych publiczne adresy IP są przypisywane określające, czy adres jest wyświetlany w jedną listę lub innej.

| **Publiczny przypadku przypisanie adresów IP** | **Zostanie wyświetlony w Podsumowanie użycia** | **Zostanie wyświetlony na liście adresów IP publiczny dzierżawcy** |
| --- | --- | --- |
| Dynamiczne publiczny adres IP nie zostały jeszcze przypisane do karty Sieciowej lub Usługa równoważenia obciążenia (tymczasowe) |Nie |Yes |
| Dynamiczne publiczny adres IP przypisane do karty Sieciowej lub Usługa równoważenia obciążenia. |Yes |Yes |
| Statycznego publicznego adresu IP przypisanego do dzierżawcy karty Sieciowej lub Usługa równoważenia obciążenia. |Yes |Yes |
| Statycznego publicznego adresu IP przypisane do punktu końcowego usługi infrastruktury sieci szkieletowej. |Yes |Nie |
| Publiczny adres IP niejawnie tworzonych dla wystąpień maszyn wirtualnych IaaS i używać dla ruchu wychodzącego translatora adresów Sieciowych w sieci wirtualnej. Są one tworzone w tle, gdy dzierżawcy tworzy wystąpienie maszyny Wirtualnej, dzięki czemu maszyny wirtualne można wysyłać informacje do Internetu. |Yes |Nie |

## <a name="next-steps"></a>Kolejne kroki
[Zarządzanie kontami magazynu Azure stosu](azure-stack-manage-storage-accounts.md)