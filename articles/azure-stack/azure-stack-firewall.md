---
title: Planowanie stosu Azure zaporę stosu platformy Azure zintegrowanych systemów | Dokumentacja firmy Microsoft
description: Zawiera opis zagadnień zapory Azure stosu dla połączonych Azure stosu Azure wdrożeniach z wieloma węzłami.
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
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 919618c0779d47f0add02d5e7d3ab9ab4b5bdd10
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-firewall-integration"></a>Integracja z zaporą Azure stosu
Używanie urządzenia zapory ułatwiające bezpieczne stosu Azure jest zalecane. Mimo że zapory można ułatwić rozproszonej typu "odmowa usługi" (DDOS) ataków, wykrywania nieautoryzowanego dostępu i inspekcji zawartości, może również zostać wąskie gardło przepływności dla usług magazynu Azure, takich jak obiekty BLOB, tabel i kolejek.

Oparte na modelu tożsamości usługi Azure Active Directory (Azure AD) lub Windows Server Active Directory Federation Services (AD FS), może być konieczne do publikowania punktu końcowego usług AD FS. Jeśli używany jest tryb odłączonego wdrożenia, należy opublikować punktu końcowego usług AD FS. Aby uzyskać więcej informacji, zobacz [datacenter integracja tożsamości artykułu](azure-stack-integrate-identity.md).

Usługi Azure Resource Manager (administrator), portalu administratora i punktów końcowych usługi Key Vault (administrator) nie wymaga zewnętrznego publikowania. Na przykład jako dostawcę usługi można ograniczyć obszar narażony na ataki i tylko administrowania stosu Azure z wewnątrz sieci, a nie z Internetu.

W przypadku organizacji enterprise zewnętrznej sieci może być istniejącej sieci firmowej. W takiej sytuacji należy opublikować te punkty końcowe działanie stosu Azure z sieci firmowej.

### <a name="network-address-translation"></a>Translator adresów sieciowych
Translatora adresów sieciowych (NAT) jest to zalecana metoda, aby umożliwić wdrożenie maszyny wirtualnej (Menedżer DVM), dostęp do zasobów zewnętrznych i internet podczas wdrażania, jak również maszyn wirtualnych do konsoli odzyskiwania awaryjnego (ERCS) lub uprzywilejowanego punktu końcowego (program ten) podczas Rejestracja i rozwiązywania problemów.

Translator adresów Sieciowych, można także zamiast publiczny adres IP w sieci zewnętrznej lub publicznych adresów VIP. Jednak nie zaleca się zrobić, ponieważ ogranicza środowisko użytkownika dzierżawcy i zwiększa złożoności. Istnieją dwie opcje byłoby NAT 1:1, które nadal wymaga jednego publicznego adresu IP na adres IP użytkownika w puli lub translator adresów Sieciowych, co wymaga reguły NAT na wirtualne adresy IP użytkownika, zawierający skojarzenia do wszystkich portów, które użytkownik może używać wiele: 1.

Downsides użycia translatora adresów Sieciowych dla publicznych adresów VIP, należą:
- Translator adresów Sieciowych dodaje nakłady związane z zarządzaniem reguły zapory, ponieważ użytkownicy kontrolować własnych punktów końcowych i własne reguły publikowania w sieci stosu zdefiniowanych przez oprogramowanie (SDN). Użytkownicy, musisz skontaktować się z operatora stosu Azure można pobrać ich adresy VIP opublikowane oraz do aktualizowania listy portów.
- Podczas użycia NAT ogranicza środowisko użytkownika, zapewnia pełnej kontroli dla operatora za pośrednictwem publikowania żądania.
- W scenariuszach chmur hybrydowych przy użyciu platformy Azure należy wziąć pod uwagę Azure nie obsługuje konfigurowania tunel sieci VPN do punktu końcowego przy użyciu translatora adresów sieciowych.

### <a name="ssl-decryption"></a>Odszyfrowywania SSL
Zalecane jest obecnie wyłączone odszyfrowywania SSL na cały ruch stosu Azure. Jeśli jest obsługiwany w przyszłej aktualizacji, będzie można wskazówki o sposobie włączania odszyfrowywania SSL stosu Azure.

## <a name="edge-firewall-scenario"></a>Scenariusz zapory krawędzi
We wdrożeniu krawędzi stosu Azure jest wdrażany bezpośrednio za router brzegowy lub zaporą. W tych scenariuszach jest obsługiwane dla zapory, znajdować się nad obramowania lub działający jako urządzenie obramowania, jeśli obsługuje ona równa koszt Multi ścieżki ECMP () przy użyciu protokołu BGP lub routing statyczny.

Zazwyczaj publiczne rutowalne adresy IP są określone dla publicznych pulę adresów VIP z siecią zewnętrzną w czasie wdrażania. W scenariuszu krawędzi nie zaleca używania publicznych adresów IP z obsługą routingu w innych sieci ze względów bezpieczeństwa. Ten scenariusz umożliwia użytkownikowi wystąpić środowisko pełnej chmury własnym kontrolowane w chmurze publicznej, takich jak Azure.  

![Przykład zapory krawędzi w usłudze Azure stosu](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Enterprise intranet lub obwód zapory scenariusz sieci
W sieci intranet lub obwód wdrożenia w przedsiębiorstwie stosu Azure jest wdrażany zaporę przydzielony do strefy multi lub Between zaporą brzegową, a Zapora wewnętrznej sieci firmowej. Ruch jest dystrybuowane między bezpiecznego, sieci obwodowej (lub sieć obwodowa), a niezabezpieczone strefy, jak opisano poniżej:

- **Bezpieczne strefy**: jest to używający wewnętrzny lub firmowych routingu adresów IP sieci wewnętrznej. Bezpiecznej sieci można podzielić, mają wychodzący dostęp do Internetu za pośrednictwem NAT zapory i jest zazwyczaj dostępna z dowolnego miejsca w centrum danych za pośrednictwem sieci wewnętrznej. Wszystkie sieci Azure stosu powinien znajdować się w strefie bezpieczny, z wyjątkiem zewnętrznej sieci publicznych puli adresów VIP.
- **Strefy granicznej**. Sieci obwodowej jest w przypadku, gdy zewnętrzne lub aplikacji, takich jak serwery sieci Web są zwykle wdrażane internetowy. Zazwyczaj jest monitorowany przez zaporę, aby uniknąć ataków DDoS i włamań (przejęcie), umożliwiając określony ruch przychodzący z Internetu. Tylko zewnętrzne sieci publicznych pulę adresów VIP stosu Azure powinien znajdować się w sieci obwodowej.
- **Niezabezpieczone strefy**. Jest to zewnętrznej sieci internet. On **nie jest** zalecane w celu wdrożenia stosu Azure w strefie niezabezpieczonych.

![Przykład sieci obwodowej w usłudze Azure stosu](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>Dowiedz się więcej
Dowiedz się więcej o [portach i protokołach używanych przez punkty końcowe stosu Azure](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Kolejne kroki
[Wymagania dotyczące usługi Azure stosu infrastruktury kluczy publicznych](azure-stack-pki-certs.md)

