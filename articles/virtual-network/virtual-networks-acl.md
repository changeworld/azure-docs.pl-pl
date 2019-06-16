---
title: Co to jest usługa Azure network listy kontroli dostępu?
description: Dowiedz się więcej o listach kontroli dostępu na platformie Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 3a7155380a51273d376226c6be7a004f386181ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035259"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co to jest punkt końcowy listy kontroli dostępu?

> [!IMPORTANT]
> Platforma Azure ma dwa różne [modeli wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia i pracy z zasobami: Usługi Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać z modelu wdrażania usługi Resource Manager. 

Punkt końcowy listy kontroli dostępu (ACL) jest rozszerzenie zabezpieczeń, dostępne dla danego wdrożenia platformy Azure. Lista ACL umożliwia selektywne akceptowanie lub odrzucanie ruchu dla punktu końcowego maszyny wirtualnej. Ta funkcja filtrowania pakietów zapewnia dodatkową warstwę zabezpieczeń. Można określić ACL sieci dla punktów końcowych tylko. Nie można określić listy ACL dla sieci wirtualnej lub określonej podsieci, znajdujących się w sieci wirtualnej. Zaleca się używać grup zabezpieczeń sieci (NSG) zamiast listy kontroli dostępu, jeśli to możliwe. Korzystając z sieciowymi grupami zabezpieczeń, listy kontroli dostępu do punktu końcowego zostaną zastąpione i przestaną być wymuszane. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md)

Listy ACL można skonfigurować przy użyciu programu PowerShell lub witryny Azure portal. Aby skonfigurować listy ACL sieci przy użyciu programu PowerShell, zobacz [listy kontroli dostępu zarządzanie dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md). Aby skonfigurować listy ACL sieci przy użyciu witryny Azure portal, zobacz [jak konfigurować punkty końcowe do maszyny wirtualnej](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Korzystając z list ACL sieci, można wykonać następujące czynności:

* Selektywnie zezwalać lub odmawiać ruchu przychodzącego w oparciu o zakres adresów IPv4 do wejściowego punktu końcowego maszyny wirtualnej w podsieci zdalnej.
* Lista zablokowanych adresów IP
* Tworzenie wielu reguł dla punktu końcowego maszyny wirtualnej
* Użyj reguł porządkowanie aby zapewnić poprawny zestaw reguł są stosowane w punkcie końcowym podanej maszyny wirtualnej (od najmniejszych do największych)
* Określ listy ACL dla określonej podsieci zdalny adres IPv4.

Zobacz [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artykuł dotyczący limitów listy ACL.

## <a name="how-acls-work"></a>Sposobu działania list kontroli dostępu
Lista ACL jest obiektem, który zawiera listę reguł. Podczas tworzenia listy ACL i zastosować je do punktu końcowego maszyny wirtualnej, filtrowanie pakietów odbywa się w węźle hosta maszyny wirtualnej. Oznacza to, że ruch z zdalne adresy IP są filtrowane według węzeł hosta pasujące reguły listy ACL zamiast na maszynie Wirtualnej. Zapobiega to cennego cykle procesora CPU podstawą przedstawiają analizę wydatków filtrowanie pakietów z maszyną Wirtualną.

Po utworzeniu maszyny wirtualnej, domyślna lista ACL jest umieszczany w miejsce na blokowanie całego ruchu przychodzącego. Jednakże jeśli punkt końcowy jest tworzony dla (port 3389), następnie domyślnej listy ACL jest modyfikowany, aby zezwolić na cały ruch przychodzący dla tego punktu końcowego. Ruch przychodzący z dowolnej podsieci zdalnej jest wówczas dozwolone do określonego punktu końcowego i bez aprowizacji zapory jest wymagana. Wszystkie pozostałe porty są blokowane dla ruchu przychodzącego, chyba że punkty końcowe są tworzone dla tych portów. Ruch wychodzący jest dozwolony domyślnie.

**Przykładowa tabela domyślnej listy ACL**

| **Reguła #** | **Podsieci zdalnej** | **Punkt końcowy** | **Akceptowanie/niezezwalania** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Zezwala na |

## <a name="permit-and-deny"></a>Zezwala na używanie i odmowa
Można selektywnie zezwolić lub zezwalają na ruch sieciowy do wejściowego punktu końcowego maszyny wirtualnej, tworząc zasady określające "pozwolenie" lub "Odmów". Należy zauważyć, że domyślnie po utworzeniu punktu końcowego cały ruch jest dozwolony do punktu końcowego. Z tego powodu ważne jest zrozumienie, jak utworzyć reguły zezwolenia/niezezwalania i umieścić je w odpowiedniej kolejności pierwszeństwa, jeśli zapewnia szczegółową kontrolę ruchu sieciowego, który chcesz zezwolić na docieranie do punktu końcowego maszyny wirtualnej.

Kwestie do rozważenia:

1. **Listy kontroli dostępu —** domyślnie po utworzeniu punktu końcowego, firma Microsoft zezwala na wszystkie dla punktu końcowego.
2. **Zezwala na -** podczas dodawania co najmniej jeden "pozwolenie" zakresów, domyślnie Odrzucasz inne zakresy. Tylko pakiety z dozwolonego zakresu adresów IP będzie można nawiązać połączenia z usługą punktu końcowego maszyny wirtualnej.
3. **Odmów -** podczas dodawania co najmniej jeden "Odmów" zakresów, można wprowadzać wszystkie zakresy ruchu domyślnie.
4. **Kombinacja zezwalanie i odmowa —** można użyć kombinacji "pozwolenie" i "Odmów", gdy użytkownik chce wydzielenie przez niego określonego zakresu adresów IP mają być dozwolone lub odmowa dostępu.

## <a name="rules-and-rule-precedence"></a>Pierwszeństwo reguły i zasady
Listy ACL sieci można skonfigurować pod kątem w punktach końcowych określonej maszyny wirtualnej. Na przykład można określić sieć listy ACL dla punktu końcowego protokołu RDP, utworzony na maszynie wirtualnej, które blokad szczegółów dostępu do określonych adresów IP. W poniższej tabeli przedstawiono sposób, aby udzielić dostępu do publicznych wirtualnych adresów IP (VIP) dla określonego zakresu zezwalanie na dostęp dla protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. Wykonamy *najniższą ma pierwszeństwo przed* reguły zamówienia.

### <a name="multiple-rules"></a>Wiele reguł
W poniższym przykładzie, jeśli chcesz zezwolić na dostęp do punktu końcowego protokołu RDP tylko z dwa zakresy publicznych adresów IPv4 (65.0.0.0/8 i 159.0.0.0/8), można to osiągnąć, określając dwa *zezwolić* reguły. W takim przypadku od protokołu RDP jest tworzone domyślnie dla maszyny wirtualnej, można zablokować dostęp do portu protokołu RDP na podstawie podsieci zdalnej. W poniższym przykładzie pokazano sposób, aby udzielić dostępu do publicznych wirtualnych adresów IP (VIP) dla określonego zakresu zezwalanie na dostęp dla protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. To działa, ponieważ listy ACL sieci można skonfigurować dla punktu końcowego określonej maszyny wirtualnej, a domyślnie odmowa dostępu.

**Przykład — wiele reguł**

| **Reguła #** | **Podsieci zdalnej** | **Punkt końcowy** | **Akceptowanie/niezezwalania** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Zezwala na |
| 200 |159.0.0.0/8 |3389 |Zezwala na |

### <a name="rule-order"></a>Kolejność reguł
Ponieważ wiele reguł może zostać określony dla punktu końcowego, musi być sposób organizowania zasad w celu ustalenia, która reguła ma pierwszeństwo. Kolejność reguł określa priorytet. Postępuj zgodnie z list ACL sieci *najniższą ma pierwszeństwo przed* reguły zamówienia. W poniższym przykładzie punktu końcowego na porcie 80 selektywnie udzielany jest dostęp do tylko określonych zakresów adresów IP. Aby to skonfigurować, mamy regułę Odmów (zasada \# 100) dla adresów w przestrzeni 175.1.0.1/24. Następnie określono drugiej reguły o priorytecie 200, która zezwala na dostęp do wszystkich adresów w obszarze 175.0.0.0/8.

**Przykład — priorytet reguły**

| **Reguła #** | **Podsieci zdalnej** | **Punkt końcowy** | **Akceptowanie/niezezwalania** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Zablokuj |
| 200 |175.0.0.0/8 |80 |Zezwala na |

## <a name="network-acls-and-load-balanced-sets"></a>Sieć listy kontroli dostępu i zestawy o zrównoważonym obciążeniu
Listy ACL sieci można określić w punkcie końcowym zestawu o zrównoważonym obciążeniu. Jeśli lista ACL jest określona dla zestawu o zrównoważonym obciążeniu, listy ACL sieci są stosowane do wszystkich maszyn wirtualnych w tym zestawie o zrównoważonym obciążeniu. Na przykład jeśli ze zrównoważonym obciążeniem "Port 80" jest tworzony zestaw i zestawu o zrównoważonym obciążeniu zawiera 3 maszyny wirtualne, utworzona sieć listy ACL w punkcie końcowym "Port 80" jednej z maszyn wirtualnych zostaną automatycznie zastosowane do innych maszyn wirtualnych.

![Sieć listy kontroli dostępu i zestawy o zrównoważonym obciążeniu](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Następne kroki
[Zarządzanie listami kontroli dostępu dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md)

