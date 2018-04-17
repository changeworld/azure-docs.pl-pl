---
title: Co to jest lista kontroli dostępu sieci platformy Azure?
description: Więcej informacji na temat list kontroli dostępu na platformie Azure
services: virtual-network
documentationcenter: na
author: genli
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
ms.openlocfilehash: b2239ae6393e74a518522594d36f7b9c30d2a6f7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co to jest punkt końcowy listy kontroli dostępu?

> [!IMPORTANT]
> Platforma Azure ma dwa różne [modele wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia i pracy z zasobami: Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większości nowych wdrożeń korzystać modelu wdrażania usługi Resource Manager. 

Punkt końcowy listy kontroli dostępu (ACL) jest ulepszenie zabezpieczeń, dostępne dla danego wdrożenia usługi Azure. Listy ACL zapewnia możliwość selektywnego akceptowanie lub odrzucanie ruchu dla punktu końcowego maszyny wirtualnej. Ta możliwość filtrowania pakietów zapewnia dodatkową warstwę zabezpieczeń. Można określić listy ACL tylko punktów końcowych sieci. Nie można określić listy ACL dla sieci wirtualnej lub określonej podsieci zawarte w sieci wirtualnej. Zaleca się używanie grup zabezpieczeń sieci (NSG) zamiast listy kontroli dostępu, jeśli to możliwe. Aby dowiedzieć się więcej na temat grup NSG, zobacz [omówienie grupy zabezpieczeń sieci](virtual-networks-nsg.md)

Listy kontroli dostępu można skonfigurować przy użyciu programu PowerShell lub w portalu Azure. Aby skonfigurować sieć listy ACL za pomocą programu PowerShell, zobacz [listy kontroli dostępu zarządzanie dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md). Aby skonfigurować sieć listy ACL za pomocą portalu Azure, zobacz [sposobu konfigurowania punktów końcowych do maszyny wirtualnej](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Za pomocą listy kontroli dostępu w sieci, można wykonać następujące czynności:

* Selektywnie akceptowanie lub odrzucanie ruchu przychodzącego na podstawie podsieci zdalne zakres adresów IPv4 do wejściowy punkt końcowy maszyny wirtualnej.
* Czarna lista adresów IP
* Tworzenie wielu reguł dla punktu końcowego maszyny wirtualnej
* Użyj reguł kolejności zapewnienie poprawny zestaw reguł są stosowane w punkcie końcowym podanej maszyny wirtualnej (najniższy najwyższe)
* Określ listy ACL dla określonej podsieci zdalnego adresu IPv4.

Zobacz [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) artykułu limitów listy ACL.

## <a name="how-acls-work"></a>Jak działają listy kontroli dostępu
Lista ACL jest obiekt, który zawiera listę reguł. Po utworzeniu listy ACL i zastosować je do punktu końcowego maszyny wirtualnej, filtrowanie pakietów odbywa się w węźle hosta maszyny wirtualnej. Oznacza to, że ruch z zdalne adresy IP są filtrowane według węzła hosta pasujących reguł listy ACL zamiast na maszynie Wirtualnej. Zapobiega to wydatków cenny cykli Procesora na filtrowanie pakietów maszyny Wirtualnej.

Po utworzeniu maszyny wirtualnej, domyślne listy ACL jest umieszczany w miejscu, aby zablokować cały ruch przychodzący. Jednak jeśli punkt końcowy jest tworzony dla (port 3389), następnie domyślnej listy ACL są modyfikowane w celu zezwolić na cały ruch przychodzący dla tego punktu końcowego. Ruch przychodzący z żadnych podsieci zdalnej jest następnie dozwolone do określonego punktu końcowego i nie obsługi zapory jest wymagany. Wszystkie inne porty są blokowane dla ruchu przychodzącego, chyba że dla tych portów są tworzone punkty końcowe. Domyślnie jest mogą ruchu wychodzącego.

**Przykład tabeli domyślnej listy kontroli dostępu**

| **Reguła #** | **Podsieci zdalnej** | **punkt końcowy** | **Akceptowanie/odrzucanie** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Zezwolenie na |

## <a name="permit-and-deny"></a>Zezwalaj i Odmów
Można selektywnie zezwolenie na lub zezwalają na ruch sieciowy wejściowego punktu końcowego maszyny wirtualnej przez utworzenie reguły określające "zezwala na" lub "odmowy". Należy pamiętać, że domyślnie po utworzeniu punktu końcowego, cały ruch jest dozwolony do punktu końcowego. Dlatego ważne jest zrozumienie sposobu tworzenia reguły akceptowanie/odrzucanie i umieścić je w prawidłowej kolejności pierwszeństwa, jeśli szczegółową kontrolę nad ruchu sieciowego, który chce zezwalać na łączności z punktem końcowym maszyny wirtualnej.

Kwestie do rozważenia:

1. **Nie listy ACL —** domyślnie po utworzeniu punktu końcowego, firma Microsoft zezwala na wszystkie dla punktu końcowego.
2. **Zezwolenie na -** podczas dodawania co najmniej jeden "zezwala na" zakresów, domyślnie Odrzucasz innych zakresów. Tylko pakiety z zakresu dozwolonych adresów IP będzie mogła nawiązać połączenia z punktem końcowym maszyny wirtualnej.
3. **Odmów -** podczas dodawania co najmniej jeden "odmowy" zakresów, można wprowadzać innych zakresów ruchu domyślnie.
4. **Kombinacja zezwalanie i odmowa -** można użyć kombinacji "zezwala na" i "odmowy", aby dotycząca szczególnych limit określony zakres IP mają być dozwolone lub odmowa dostępu.

## <a name="rules-and-rule-precedence"></a>Pierwszeństwo reguły i zasady
Listy kontroli dostępu sieciowego można skonfigurować w punktach końcowych określonej maszyny wirtualnej. Na przykład można określić sieci listy ACL dla punktu końcowego protokołu RDP utworzone na maszynie wirtualnej, które blokad klawisz dostępu dla niektórych adresów IP. W poniższej tabeli przedstawiono sposób przyznać dostęp do publicznych wirtualnych adresów IP (VIP) dla określonego zakresu pozwalające na dostęp dla protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. Firma Microsoft wykonaj *najniższa ma pierwszeństwo przed* reguły kolejności.

### <a name="multiple-rules"></a>Wiele reguł
W poniższym przykładzie, jeśli chcesz zezwolić na dostęp do punktu końcowego protokołu RDP tylko z dwa zakresy publicznych adresów IPv4 (65.0.0.0/8 i 159.0.0.0/8), można to osiągnąć, określając dwa *zezwolenie na* reguły. W takim przypadku od chwili utworzenia RDP jest domyślnie dla maszyny wirtualnej, można zablokować dostęp do portu protokołu RDP na podstawie podsieci zdalnej. W poniższym przykładzie pokazano sposób przyznać dostęp do publicznych wirtualnych adresów IP (VIP) dla określonego zakresu pozwalające na dostęp dla protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. Dzieje się tak, ponieważ sieci listy kontroli dostępu można skonfigurować dla punktu końcowego określonej maszyny wirtualnej, i domyślnie odmówiono dostępu.

**Przykład — wiele reguł**

| **Reguła #** | **Podsieci zdalnej** | **punkt końcowy** | **Akceptowanie/odrzucanie** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Zezwolenie na |
| 200 |159.0.0.0/8 |3389 |Zezwolenie na |

### <a name="rule-order"></a>Kolejność reguł
Ponieważ wiele reguł można określić dla punktu końcowego, musi być sposób organizowania zasad w celu ustalenia, które ma pierwszeństwo. Kolejność reguł określa priorytet. Następujące listy ACL sieci *najniższa ma pierwszeństwo przed* reguły kolejności. W poniższym przykładzie punktu końcowego na porcie 80 selektywnie uzyskuje dostęp do tylko określonych zakresów adresów IP. Aby to skonfigurować, istnieje reguła odmowy (reguły \# 100) dla adresów w obszarze 175.1.0.1/24. Następnie określono drugą regułę z ustawieniem pierwszeństwa 200 pozwala na dostęp do wszystkich adresów w obszarze 175.0.0.0/8.

**Przykład — pierwszeństwo reguły**

| **Reguła #** | **Podsieci zdalnej** | **punkt końcowy** | **Akceptowanie/odrzucanie** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Zablokuj |
| 200 |175.0.0.0/8 |80 |Zezwolenie na |

## <a name="network-acls-and-load-balanced-sets"></a>Sieci listy kontroli dostępu i zestawy równoważeniem obciążenia
Listy kontroli dostępu w sieci można określić dla punktu końcowego zestawu o zrównoważonym obciążeniu. Jeśli lista ACL jest określona dla zestawu o zrównoważonym obciążeniu, sieci listy kontroli dostępu jest stosowany do wszystkich maszyn wirtualnych w tym zestaw o zrównoważonym obciążeniu. Na przykład jeśli o zrównoważonym obciążeniu zestaw jest tworzony z "Port 80" i zestawu o zrównoważonym obciążeniu zawiera 3 maszyn wirtualnych, sieci ACL utworzone w punkcie końcowym "Port 80" jednej maszyny Wirtualnej zostanie automatycznie zastosowana do innych maszyn wirtualnych.

![Sieci listy kontroli dostępu i zestawy równoważeniem obciążenia](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Następne kroki
[Zarządzanie listami kontroli dostępu dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md)

