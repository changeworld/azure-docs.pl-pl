---
title: Co to jest delegowanie podsieci w sieci wirtualnej platformy Azure?
description: Dowiedz się więcej o delegowaniu podsieci w sieci wirtualnej platformy Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281339"
---
# <a name="what-is-subnet-delegation"></a>Co to jest delegowanie podsieci?

Delegowanie podsieci umożliwia wyznaczenie określonej podsieci dla wybranej usługi PaaS platformy Azure, która musi zostać wstrzyknięta do sieci wirtualnej. Delegowanie podsieci zapewnia klientowi pełną kontrolę nad zarządzaniem integracją usług platformy Azure z ich sieciami wirtualnymi.

Po przekazaniu podsieci do usługi platformy Azure, można zezwolić tej usłudze do ustanowienia niektórych podstawowych reguł konfiguracji sieci dla tej podsieci, które pomagają usługi platformy Azure obsługiwać ich wystąpień w sposób stabilny. W rezultacie usługa platformy Azure może ustanowić pewne warunki przed lub po wdrożeniu, takie jak:
- wdrożyć usługę w udostępnionej i dedykowanej podsieci.
- dodać do usługi zestaw zasad intencji sieci po wdrożeniu, które jest wymagane do prawidłowego działania usługi.

##  <a name="advantages-of-subnet-delegation"></a>Zalety delegowania podsieci

Delegowanie podsieci do określonych usług zapewnia następujące korzyści:

- pomaga wyznaczyć podsieć dla jednej lub więcej usług platformy Azure i zarządzać wystąpieniami w podsieci zgodnie z wymaganiami. Na przykład właściciel sieci wirtualnej może zdefiniować następujące dla delegowanej podsieci, aby lepiej zarządzać zasobami i uzyskać dostęp w następujący sposób:
    - zasady filtrowania sieci z sieciowymi grupami zabezpieczeń.
    - zasady routingu z trasami zdefiniowanymi przez użytkownika.
    - integracji usług z konfiguracjami punktów końcowych usług.
- pomaga wstrzyknąć usługom, aby lepiej zintegrować się z siecią wirtualną, definiując ich wstępne warunki wdrożeń w postaci zasad intencji sieci. Dzięki temu wszelkie działania, które mogą mieć wpływ na funkcjonowanie wtryskiwania usługi mogą być blokowane w PUT.


## <a name="who-can-delegate"></a>Kto może delegować?
Delegowanie podsieci jest ćwiczeniem, które właściciele sieci wirtualnej muszą wykonać w celu wyznaczenia jednej z podsieci dla określonej usługi Azure. Usługa Azure z kolei wdraża wystąpienia w tej podsieci do użycia przez obciążenia klientów.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Wpływ delegowania podsieci na podsieci
Każda usługa platformy Azure definiuje swój własny model wdrażania, gdzie można zdefiniować, jakie właściwości robią lub nie obsługują w podsieci delegowanej do celów iniekcji, takich jak:
- współużytkowana podsieć z innymi usługami platformy Azure lub skalowanie maszyny wirtualnej / maszyny wirtualnej ustawiona w tej samej podsieci lub obsługuje tylko dedykowaną podsieć z tylko wystąpieniami tej usługi w niej.
- obsługuje skojarzenie sieciowej sieciowej z podsiecią delegowanej.
- obsługuje sieciowej sieciowej sieciowej skojarzonej z podsiecią delegowaną można również skojarzyć z dowolną inną podsiecią.
- umożliwia skojarzenie tabeli marszruty z podsiecią delegowaną.
- umożliwia powiązanie tabeli marszruty skojarzonej z podsiecią delegowaną z dowolną inną podsiecią.
- określa minimalną liczbę adresów IP w delegowanej podsieci.
- określa przestrzeń adresu IP w podsieci delegowanej z przestrzeni prywatnego adresu IP (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- określa, że niestandardowa konfiguracja DNS ma wpis dns platformy Azure.

Usługi wstrzyknięte mogą również dodawać własne zasady w następujący sposób:
- **Zasady zabezpieczeń:** Zbieranie reguł zabezpieczeń wymaganych do pracy danej usługi.
- **Zasady tras:** Zbieranie tras wymaganych do pracy danej usługi.

## <a name="what-subnet-delegation-does-not-do"></a>Nie wykonuje delegowania podsieci

Usługi platformy Azure wstrzykowane do delegowanej podsieci nadal mają podstawowy zestaw właściwości, które są dostępne dla podsieci niedelegowanych, takich jak:
-  Usługi platformy Azure można wstrzyknąć wystąpień do podsieci klienta, ale nie może mieć wpływu na istniejące obciążenia.
-  Zasady lub trasy, które mają zastosowanie te usługi są elastyczne i mogą zostać zastąpione przez klienta.

## <a name="next-steps"></a>Następne kroki

- [Delegowanie podsieci](manage-subnet-delegation.md)
