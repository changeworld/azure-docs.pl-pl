---
title: Zaporę platformy Azure Stack planowania dla usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: W tym artykule opisano zagadnienia dotyczące zapory usługi Azure Stack dla wdrożeniach z wieloma węzłami podłączonej do platformy Azure, Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: eff526118f6fd127ba720d28296baf86abd01393
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246445"
---
# <a name="azure-stack-firewall-integration"></a>Integracja z zaporą Azure Stack
Zaleca się używać urządzenia zapory w celu bezpiecznej usługi Azure Stack. Mimo że zapory może ułatwić elementów, takich jak rozproszonej typu "odmowa usługi" (DDOS) ataki, Wykrywanie nieautoryzowanego dostępu i inspekcji zawartości, może również zostać przepływności wąskiego gardła dla usług Azure storage, takie jak obiekty BLOB, tabel i kolejek.

Oparte na modelu tożsamości usługi Azure Active Directory (Azure AD) lub Windows Server Active Directory Federation Services (AD FS), może być konieczne do publikowania punktu końcowego usług AD FS. Jeśli używany jest tryb wdrożenia o odłączony, należy opublikować punktu końcowego usług AD FS. Aby uzyskać więcej informacji, zobacz [datacenter integracja tożsamości artykułu](azure-stack-integrate-identity.md).

Usługi Azure Resource Manager (administrator), portal administratora i punkty końcowe usługi Key Vault (administrator) niekoniecznie zewnętrznych publikowania. Na przykład jako dostawca usług możesz chcieć ograniczyć obszar narażony na ataki i tylko administrowanie Azure Stack z wewnątrz sieci, a nie z Internetu.

Przedsiębiorstwa to organizacje zewnętrznej sieci mogą być istniejącej sieci firmowej. W takiej sytuacji należy opublikować te punkty końcowe do działania usługi Azure Stack z sieci firmowej.

### <a name="network-address-translation"></a>Translator adresów sieciowych
Translacji adresów sieciowych (NAT) jest zalecane, aby zezwolić na wdrożenie maszyny wirtualnej (Menedżer DVM), dostęp do zasobów zewnętrznych i Internetu podczas wdrażania, a także maszyn wirtualnych z konsoli odzyskiwania awaryjnego (ERCS) lub uprzywilejowanego punktu końcowego (program ten) podczas Rejestracja i rozwiązywania problemów.

Translator adresów Sieciowych można także alternatywę dla publicznych adresów IP w sieci zewnętrznej lub publicznych adresów VIP. Jednak nie zaleca się zrobić, ponieważ ogranicza środowisko użytkownika dzierżawy i zwiększa złożonością. Dwie opcje będą NAT 1:1, która nadal wymaga jeden publiczny adres IP na adres IP użytkownika, w puli lub wiele: 1 translatora adresów Sieciowych wymaga reguły translatora adresów Sieciowych dla każdego użytkownika adresu VIP, zawierający skojarzenia do wszystkich portów użytkownik może użyć.

Oto niektóre z wad korzystania z translatora adresów Sieciowych dla publicznych adresów VIP są:
- Translator adresów Sieciowych dodaje obciążenie związane z zarządzaniem reguły zapory, ponieważ użytkownicy kontrolować swoje własne punkty końcowe i własne reguły publikowania w stosie programowalnej sieci (komputerowej SDN). Użytkownicy powinni skontaktować się ze operatora infrastruktury Azure Stack, aby uzyskać ich adresy VIP opublikowane i można zaktualizować listy portów.
- Podczas użycia translatora adresów Sieciowych ogranicza środowisko użytkownika, zapewnia pełną kontrolę dla operatora przez opublikowanie żądania.
- W przypadku scenariuszy chmury hybrydowej przy użyciu platformy Azure należy wziąć pod uwagę Azure nie obsługuje konfigurowanie tunelu sieci VPN do punktu końcowego przy użyciu translatora adresów sieciowych.

### <a name="ssl-decryption"></a>Odszyfrowywania protokołu SSL
Zalecane jest obecnie wyłączone odszyfrowywania protokołu SSL na cały ruch do usługi Azure Stack. Jeśli jest obsługiwany w przyszłych aktualizacjach, zostaną udostępnione wskazówki o sposobie włączania odszyfrowywania protokołu SSL dla usługi Azure Stack.

## <a name="edge-firewall-scenario"></a>Scenariusz zapory na krawędzi
We wdrożeniu programu edge usługi Azure Stack jest wdrażany bezpośrednio pod routera brzegowego lub zapory. W tych scenariuszach jest obsługiwane w przypadku zaporę aby być nad obramowanie (scenariusz 1), w których obsługuje zarówno konfiguracji aktywne aktywne i aktywny / pasywny zapory lub działając jako urządzenie obramowania (scenariusz z 2), gdzie obsługuje on tylko aktywne aktywne zapory Konfiguracja jednostki uzależnionej na równy koszt wiele ścieżek (ECMP) za pomocą protokołu BGP lub routingu statycznego dla trybu failover.

Zazwyczaj publiczne adresy IP routingowi określono pulę publicznych adresów VIP w sieci zewnętrznej w czasie wdrażania. W scenariuszu edge nie zaleca używania publicznych adresów IP z obsługą routingu w innych sieci ze względów bezpieczeństwa. W tym scenariuszu umożliwia użytkownikowi środowisko środowisko pełni opartej na chmurze własnym kontrolowanego, tak jak w chmurze publicznej, takich jak platforma Azure.  

![Przykład zapory na krawędzi w usłudze Azure Stack](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Intranet lub obwód sieci zapory zrealizować scenariusz przedsiębiorstwa
W sieci intranet lub obwód wdrożenia w przedsiębiorstwie usługi Azure Stack jest wdrażany w zaporze podzielone na strefy multi lub Between zapory na krawędzi, a Zapora wewnętrznej sieci firmowej. Ruch jest dystrybuowane między bezpiecznego, sieci obwodowej (lub sieć obwodowa), a niezabezpieczone strefy, jak opisano poniżej:

- **Bezpieczne strefy**: To jest siecią wewnętrzną, która wykorzystuje wewnętrzną lub korporacyjną rutowalne adresy IP. Bezpiecznej sieci mogą zostać podzielone ma wychodzący dostęp do Internetu za pośrednictwem translatora adresów Sieciowych na zaporze i jest zazwyczaj dostępny z dowolnego miejsca w centrum danych za pośrednictwem sieci wewnętrznej. Wszystkie sieci usługi Azure Stack powinien znajdować się w strefie bezpieczny, z wyjątkiem pulę publicznych adresów VIP sieci zewnętrznej.
- **Obwodowej strefę**. W sieci obwodowej jest w przypadku, gdy zewnętrznych lub aplikacji, takich jak serwery sieci Web są zazwyczaj wdrożone z Internetem. Zazwyczaj jest monitorowany przez zaporę, aby uniknąć ataków, takich jak przed atakami DDoS i włamań (stosowanie metod hakerskich), umożliwiając jednocześnie określony ruch przychodzący z Internetu. Tylko zewnętrzne sieci publicznych pulę adresów VIP stosu Azure powinien znajdować się w sieci obwodowej.
- **Niezabezpieczony strefy**. Jest to zewnętrznej sieci internet. Jego **nie** zaleca, aby wdrożyć usługę Azure Stack w strefie niezabezpieczonych.

![Przykład sieci obwodowej w usłudze Azure Stack](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Dowiedz się więcej
Dowiedz się więcej o [portach i protokołach używanych przez punkty końcowe usługi Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Kolejne kroki
[Wymagania dotyczące usługi Azure Stack infrastruktury kluczy publicznych](azure-stack-pki-certs.md)

