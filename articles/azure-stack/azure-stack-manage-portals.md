---
title: Korzystanie z portalu administratora w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako operatorów usługi Azure Stack Dowiedz się, jak korzystać z portalu administratora.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: cea59d061dadfa3e10330cbce7b6005a286524cc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024170"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Korzystanie z portalu administratora w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Istnieją dwa portale w usłudze Azure Stack; portal administratora i portalu użytkowników (czasami określane jako *dzierżawy* portalu.) Jako operatorów usługi Azure Stack mogą korzystać z portalu administratora, bieżące zarządzanie i operacje usługi Azure Stack.

## <a name="access-the-administrator-portal"></a>Dostęp do portalu administratora

Zestaw środowisku programistycznym, trzeba najpierw upewnij się, że możesz [łączącego się z hostem development kit](azure-stack-connect-azure-stack.md) za pomocą połączenia pulpitu zdalnego lub za pośrednictwem wirtualnej sieci prywatnej (VPN).

Dostęp do portalu administratora, przejdź do adresu URL portalu i zaloguj się przy użyciu poświadczeń operatorów usługi Azure Stack. Zintegrowany system, aby uzyskać portalu, który różni się adres URL na podstawie nazwy regionu i zewnętrznych w pełni kwalifikowaną nazwę domeny (FQDN) wdrożenia usługi Azure Stack.

| Środowisko | Adres URL portalu administratora |   
| -- | -- | 
| Zestaw Development kit| https://adminportal.local.azurestack.external  |
| Systemy zintegrowane | https://adminportal.&lt; *region*&gt;.&lt; *Nazwy FQDN*&gt; | 
| | |

 ![Portal administratora](media/azure-stack-manage-portals/admin-portal.png)

W portalu administratora można wykonać czynności takich jak:

* Zarządzanie infrastrukturą (w tym kondycji systemu, aktualizacje, wydajność itp.)
* Wypełnianie portalu Marketplace treścią
* Tworzenie subskrypcji dla użytkowników
* Utwórz plany i oferty

**Samouczek szybkiego startu** Kafelek zawiera łącza do dokumentacji online dla typowych zadań.

Mimo że operator ma umożliwia tworzenie zasobów, takich jak maszyny wirtualne, sieci wirtualnych i kont magazynu w portalu administratora, wykonaj następujące czynności [Zaloguj się do portalu użytkowników](user/azure-stack-use-portal.md) do tworzenia i testowania zasobów.

>[!NOTE]
>**Utwórz maszynę wirtualną** ma link na kafelku samouczek Szybki Start, możesz utworzyć maszynę wirtualną w portalu administratora, ale jest to przeznaczona tylko do sprawdzania poprawności usługi Azure Stack po jego pierwszym wdrożeniu.

## <a name="understand-subscription-behavior"></a>Poznanie zachowań subskrypcji

Istnieje tylko jedna subskrypcja dostępne do użycia z portalu administratora. Ta subskrypcja jest *domyślne subskrypcję dostawcy*. Nie można dodać inne subskrypcje i używać ich w portalu administratora.

Jako operatorów usługi Azure Stack można dodawać subskrypcje dla użytkowników (w tym siebie) z portalu administratora. Użytkownicy (w tym siebie) można uzyskać dostęp do tych subskrypcji z **użytkownika** portalu. Jednak portal użytkowników nie zapewnia dostęp do wszystkich możliwości operacyjnych lub administracyjnych w portalu administratora.

Portale administratora i użytkownika są wspierane przez osobnych wystąpień usługi Azure Resource Manager. Ze względu na to oddzielenie Menedżera zasobów subskrypcji nie przechodzą portali. Na przykład, jeśli, operator usługi Azure Stack, zaloguj się do portalu użytkowników, nie masz dostępu do *domyślne subskrypcję dostawcy*. Mimo że nie masz dostępu do żadnych zadań administracyjnych, można utworzyć subskrypcji dla siebie z dostępnych ofert publicznych. Tak długo, jak zalogowany do portalu użytkowników są traktowane jako użytkownik dzierżawy.

  >[!NOTE]
  >W środowisku deweloperskim zestaw Jeśli użytkownik należy do tego samego katalogu dzierżawy operatora infrastruktury Azure Stack mogą nie Zablokowano Ci logowania się do portalu administratora. Jednak ich nie może uzyskiwać dostęp do wszystkich funkcji administracyjnych. Ponadto z poziomu portalu administratora nie mogą dodać subskrypcji lub dostępu do oferty, które są dostępne do nich w aplikacji portal użytkowników.

## <a name="administrator-portal-tips"></a>Wskazówki dotyczące portalu administratora

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera zestaw domyślnych kafelków. Możesz wybrać **Edytuj pulpit nawigacyjny** zmodyfikować domyślny pulpit nawigacyjny, lub wybierz **nowy pulpit nawigacyjny** można dodać niestandardowy pulpit nawigacyjny. Możesz łatwo dodać Kafelki do pulpitu nawigacyjnego. Na przykład, możesz wybrać **New**, kliknij prawym przyciskiem myszy **oferty i plany**, a następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

### <a name="quick-access-to-online-documentation"></a>Szybki dostęp do dokumentacji online

Dostępu dokumentacja operatora usługi Azure Stack, skorzystaj z pomocy i obsługi ikonę (znak zapytania) w prawym górnym rogu portalu administratora. Przesuń kursor na ikonę, a następnie wybierz **Pomoc i obsługa techniczna**.

### <a name="quick-access-to-help-and-support"></a>Szybki dostęp do pomocy i obsługi technicznej

Jeśli wybierz ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie wybierz **nowe żądanie obsługi**, jeden z następujących wyników się zdarzyć:

- Jeśli używasz zintegrowanego systemu, ta akcja powoduje otwarcie witryny, w którym można bezpośrednio otworzyć bilet pomocy technicznej za pomocą usług obsługi klienta firmy Microsoft (CSS). Zapoznaj się [do uzyskania pomocy technicznej](azure-stack-manage-basics.md#where-to-get-support) zrozumienie, kiedy należy go za pośrednictwem pomocy technicznej firmy Microsoft lub pomocy technicznej dostawcy sprzętu producenta sprzętu (OEM).
- Jeśli używasz deweloperski, ta akcja powoduje otwarcie witryny Forum usługi Azure Stack bezpośrednio. Monitorowane są regularnie forów. Ponieważ zestaw development kit jest środowisko wersji ewaluacyjnej, nie jest oficjalnym obsługiwane oferowane za pośrednictwem firmy Microsoft CSS.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)
