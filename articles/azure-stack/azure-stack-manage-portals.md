---
title: Korzystanie z portalu administratora w stosie Azure | Dokumentacja firmy Microsoft
description: Jako operator stosu Azure jak korzystać z portalu administratora.
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
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248524"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Korzystanie z portalu administratora w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Istnieją dwa portale w stosie Azure; portal administratora i portalu użytkownika (czasem nazywane *dzierżawy* portalu.) Jako operator stosu Azure mogą korzystać z portalu administratora, bieżące zarządzanie i operacje stosu Azure.

## <a name="access-the-administrator-portal"></a>Uzyskiwanie dostępu do portalu administratora

Środowisko deweloperskie zestawu, należy najpierw upewnić się, że można [połączyć się z hostem zestawu programowanie](azure-stack-connect-azure-stack.md) za pomocą połączenia pulpitu zdalnego lub za pośrednictwem wirtualnej sieci prywatnej (VPN).

Aby uzyskać dostęp do portalu administratora, przejdź do adresu URL portalu i zaloguj się przy użyciu poświadczeń operator stosu Azure. Zintegrowany system portalu, do którego jest zależny od adresu URL na podstawie nazwa regionu i zewnętrznych pełną nazwę domeny (FQDN) wdrożenia stosu Azure.

| Środowisko | Adres URL portalu administratora |   
| -- | -- | 
| Zestaw deweloperski| https://adminportal.local.azurestack.external  |
| Systemy zintegrowane | https://adminportal.&lt; *region*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Portal administratora](media/azure-stack-manage-portals/image1.png)

W portalu administratora należy wykonać czynności takich jak:

* Zarządzanie infrastrukturą (w tym kondycji systemu, aktualizacje, pojemność itp.)
* Wypełnianie portalu Marketplace treścią
* Utwórz subskrypcje dla użytkowników
* Tworzenie planów i oferty

**Samouczek Szybki Start** kafelka zawiera linki do dokumentacji online dla najbardziej typowych zadań.

Operator ma można utworzyć zasobów takich jak maszyn wirtualnych, sieci wirtualnych i kont magazynu w portalu administratora, należy [Zaloguj się do portalu użytkowników](user/azure-stack-use-portal.md) do utworzenia i przetestowania zasobów.

>[!NOTE]
>**Utwórz maszynę wirtualną** łącza na kafelku samouczek Szybki Start jest tworzenie maszyny wirtualnej w portalu administratora, ale ta wartość jest przeznaczona tylko do sprawdzania poprawności stosu Azure po raz pierwszy wdrożeniu.

## <a name="understand-subscription-behavior"></a>Zrozumienie zachowania subskrypcji

Istnieje tylko jedna subskrypcja dostępne do użycia z portalu administratora. Ta subskrypcja jest *domyślny dostawca subskrypcji*. Nie można dodać inne subskrypcje i używać ich w portalu administratora.

Jako operator stosu Azure można dodać subskrypcji dla użytkowników (w tym siebie) z portalu administratora. Użytkownicy (w tym siebie) dostępu i używania te subskrypcje z **użytkownika** portalu. Jednak portalu użytkowników nie zapewnia dostęp do wszystkich funkcji administracyjnych lub operacyjnych portalu administratora.

Portale administratora i użytkownika bazują na oddzielnego wystąpienia usługi Azure Resource Manager. Ze względu na to oddzielenie Menedżera zasobów subskrypcji nie przechodzą portali. Na przykład, jeśli, jako operator stosu Azure logowania się do portalu użytkowników, nie masz dostępu do *domyślny dostawca subskrypcji*. Mimo że nie masz dostępu do żadnych funkcji administracyjnych, można utworzyć subskrypcji dla siebie z dostępnych ofert publicznych. Pod warunkiem, że nastąpiło zalogowanie do aplikacji portal użytkowników są uważane za użytkownika dzierżawcy.

  >[!NOTE]
  >W środowisku programistycznym zestawu Jeśli użytkownik należy do tego samego katalogu dzierżawy jako operator stosu Azure ich nie są blokowane logowanie do portalu administratora. Jednak ich nie może uzyskać dostępu do żadnych funkcji administracyjnych. Ponadto w portalu administratora nie mogą dodać subskrypcji lub dostęp oferują, które są dostępne do nich w aplikacji portal użytkowników.

## <a name="administrator-portal-tips"></a>Wskazówki dotyczące portalu administratora

### <a name="customize-the-dashboard"></a>Dostosowywać pulpit nawigacyjny

Pulpit nawigacyjny zawiera zestaw domyślnych kafelków. Możesz wybrać **edycji pulpitu nawigacyjnego** do modyfikacji domyślnego pulpitu nawigacyjnego lub wybierz **nowego pulpitu nawigacyjnego** można dodać niestandardowy pulpit nawigacyjny. Można łatwo dodać Kafelki na pulpicie nawigacyjnym. Na przykład można wybrać **nowy**, kliknij prawym przyciskiem myszy **oferuje + plany**, a następnie wybierz **Przypnij do pulpitu nawigacyjnego**.

### <a name="quick-access-to-online-documentation"></a>Szybki dostęp do dokumentacji online

Aby uzyskać dostęp do dokumentacji operator stosu Azure, w pomocy i obsługi ikona (znak zapytania) w prawym górnym rogu portalu administratora. Przesuń wskaźnik do ikony, a następnie wybierz **Pomoc i obsługa techniczna**.

### <a name="quick-access-to-help-and-support"></a>Szybki dostęp do pomocy i obsługi technicznej

Jeśli wybierz ikonę Pomoc i obsługa techniczna (znak zapytania) w prawym górnym rogu portalu administratora, a następnie wybierz **nowy obsługuje żądania**, jeden z następujących wyników stanie:

- Jeśli używasz zintegrowanego systemu, Akcja ta otwiera witrynę, której można bezpośrednio otworzyć bilet pomocy technicznej z usług obsługi klienta firmy Microsoft (CSS). Zapoznaj się [do uzyskania pomocy technicznej](azure-stack-manage-basics.md#where-to-get-support) zrozumienie, kiedy należy przejść za pomocą techniczną firmy Microsoft lub działem pomocy technicznej dostawcy sprzętu producenta sprzętu (OEM).
- Jeśli używasz zestaw deweloperski, ta akcja powoduje otwarcie witryny fora stosu Azure bezpośrednio. Fora te są regularnie monitorowane. Ponieważ zestaw deweloperski środowiska do oceny, nie jest oficjalną obsługiwane oferowane przez firmy Microsoft CSS.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie regionu Azure stosu](azure-stack-region-management.md)
