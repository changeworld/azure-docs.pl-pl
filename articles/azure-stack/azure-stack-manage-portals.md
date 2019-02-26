---
title: Korzystanie z portalu administratora w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako operatorów usługi Azure Stack Dowiedz się, jak korzystać z portalu administratora.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817936"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Szybki Start: Użyj portalu administracyjnego usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Istnieją dwa portale w usłudze Azure Stack; w portalu administratora i portalu użytkowników (czasami określane jako *dzierżawy* portalu.) Operator usługi Azure Stack użyjesz portalu administracyjnego dla bieżące zarządzanie i operacje usługi Azure Stack.

## <a name="access-the-administrator-portal"></a>Dostęp do portalu administratora

Dostęp do portalu administratora, przejdź do adresu URL portalu i zaloguj się przy użyciu poświadczeń operatorów usługi Azure Stack. Zintegrowany system, aby uzyskać portalu, który różni się adres URL na podstawie nazwy regionu i zewnętrznych w pełni kwalifikowaną nazwę domeny (FQDN) wdrożenia usługi Azure Stack. W portalu administratora adres URL jest zawsze taki sam sposób, w przypadku wdrożeń usługi Azure Stack Development Kit (ASDK). 

| Środowisko | Adres URL portalu administratora |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Systemy zintegrowane | https://adminportal.&lt; *region*&gt;.&lt; *Nazwy FQDN*&gt; | 
| | |

> [!TIP]
> Dla środowiska ASDK, należy najpierw upewnić się, że możesz [łączącego się z hostem development kit](azure-stack-connect-azure-stack.md) za pomocą połączenia pulpitu zdalnego lub za pośrednictwem wirtualnej sieci prywatnej (VPN).

 ![W portalu administratora](media/azure-stack-manage-portals/admin-portal.png)

Domyślna strefa czasowa dla wszystkich wdrożeń usługi Azure Stack jest ustawiona do uniwersalnego czasu koordynowanego (UTC). 

W portalu administratora możesz można wykonywać następujące czynności:

* [Rejestrowania usługi Azure Stack na platformie Azure](azure-stack-registration.md)
* [Wypełnianie portalu marketplace](azure-stack-download-azure-marketplace-item.md)
* [Utwórz plany, oferty i subskrypcje dla użytkowników](azure-stack-plan-offer-quota-overview.md)
* [Monitorowanie kondycji i alertów](azure-stack-monitor-health.md)
* [Zarządzanie aktualizacjami w usłudze Azure Stack](azure-stack-updates.md)

**Samouczek szybkiego startu** Kafelek zawiera łącza do dokumentacji online dla typowych zadań.

Chociaż operator może tworzyć zasoby, takie jak maszyny wirtualne, sieci wirtualnych i kont magazynu w portalu administracyjnym, należy [Zaloguj się do portalu użytkowników](user/azure-stack-use-portal.md) do tworzenia i testowania zasobów.

>[!NOTE]
>**Utwórz maszynę wirtualną** ma link na kafelku samouczek Szybki Start, możesz utworzyć maszynę wirtualną w portalu administracyjnym, ale jest to przeznaczona tylko do sprawdzania, czy usługi Azure Stack został pomyślnie wdrożony.

## <a name="understand-subscription-behavior"></a>Poznanie zachowań subskrypcji

Istnieją trzy subskrypcje utworzone domyślnie w portalu administracyjnym; użycie domyślnego dostawcę i zliczania. Operator, przede wszystkim używasz *domyślne subskrypcję dostawcy*. Nie można dodać inne subskrypcje i używać ich w portalu administracyjnym. 

Inne subskrypcje są tworzone przez użytkowników w aplikacji portal użytkowników, w oparciu o plany i oferty, które utworzysz dla nich. Jednak portal użytkowników nie zapewnia dostęp do wszystkich możliwości operacyjnych lub administracyjnych w portalu administratora.

Portale administracji i użytkowników są wspierane przez osobnych wystąpień usługi Azure Resource Manager. Ze względu na to oddzielenie usługi Azure Resource Manager subskrypcji nie przechodzą portali. Na przykład, jeśli, operator usługi Azure Stack, zaloguj się do portalu użytkowników, nie masz dostępu do *domyślne subskrypcję dostawcy*. Mimo że nie masz dostępu do żadnych zadań administracyjnych, można utworzyć subskrypcji dla siebie z dostępnych ofert publicznych. Tak długo, jak zalogowany do portalu użytkowników są traktowane jako użytkownik dzierżawy.

  >[!NOTE]
  >W środowisku ASDK Jeśli użytkownik należy do tej samej dzierżawie katalogu jako operatora usługi Azure Stack, ich nie są blokowane logowanie do portalu administracyjnego. Jednak nie można uzyskać dostępu do żadnych funkcji administracyjnych lub Dodaj subskrypcje w celu uzyskania dostępu do oferty, które są dostępne do nich w aplikacji portal użytkowników.

## <a name="administration-portal-tips"></a>Wskazówki dotyczące portalu administrowania

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera zestaw domyślnych kafelków. Możesz wybrać **Edytuj pulpit nawigacyjny** zmodyfikować domyślny pulpit nawigacyjny, lub wybierz **nowy pulpit nawigacyjny** można dodać niestandardowy pulpit nawigacyjny. Możesz łatwo dodać Kafelki do pulpitu nawigacyjnego. Na przykład, możesz wybrać **+ Utwórz zasób**, kliknij prawym przyciskiem myszy **oferty i plany**, a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

Czasami może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, kliknij **Edytuj pulpit nawigacyjny**, a następnie kliknij prawym przyciskiem myszy i wybierz **przywrócony stan domyślny**.

### <a name="quick-access-to-online-documentation"></a>Szybki dostęp do dokumentacji online

Aby uzyskać dostęp do dokumentacji usługi Azure Stack operatora, skorzystaj z pomocy i obsługi ikonę (znak zapytania) w prawym górnym rogu portalu administratora. Przesuń kursor na ikonę, a następnie wybierz **Pomoc i obsługa techniczna**.

### <a name="quick-access-to-help-and-support"></a>Szybki dostęp do pomocy i obsługi technicznej

Jeśli wybierz ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie wybierz **nowe żądanie obsługi**, jedna z poniższych wyników sytuacji:

- Jeśli używasz zintegrowanego systemu, ta akcja powoduje otwarcie witryny, w którym można bezpośrednio otworzyć bilet pomocy technicznej za pomocą usług obsługi klienta firmy Microsoft (CSS). Zapoznaj się [do uzyskania pomocy technicznej](azure-stack-manage-basics.md#where-to-get-support) zrozumienie, kiedy należy go za pośrednictwem pomocy technicznej firmy Microsoft lub pomocy technicznej dostawcy sprzętu producenta sprzętu (OEM).
- Jeśli używasz ASDK, ta akcja powoduje otwarcie [witryna Forum usługi Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) bezpośrednio. Monitorowane są regularnie forów. Ponieważ ASDK jest środowisko wersji ewaluacyjnej, nie jest oficjalnym obsługiwane oferowane za pośrednictwem firmy Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Szybki dostęp do planu platformy Azure

Jeśli wybierzesz **Pomoc i obsługa techniczna** (znak zapytania) w prawym górnym rogu administrację portalu, a następnie wybierz **plan usługi Azure**, Nowa karta przeglądarki zostanie otwarty i przejście do planu platformy Azure. Wpisując **usługi Azure Stack** w **produktów** pola wyszukiwania, możesz zobaczyć wszystkie aktualizacje plan usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Rejestrowania usługi Azure Stack na platformie Azure](azure-stack-registration.md) i wypełnić [marketplace usługi Azure Stack](azure-stack-marketplace.md) przy użyciu elementów do zaoferowania użytkownikom. 
