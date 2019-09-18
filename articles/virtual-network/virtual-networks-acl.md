---
title: Co to jest lista kontroli dostępu do sieci platformy Azure?
description: Informacje o listach kontroli dostępu na platformie Azure
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 38655a9da103d1d669f87c6195be7f17702f9348
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056673"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Co to jest lista kontroli dostępu do punktów końcowych?

> [!IMPORTANT]
> Platforma Azure ma dwa różne [modele wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) służące do tworzenia zasobów i pracy z nimi: Menedżer zasobów i klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większość nowych wdrożeń korzystała z modelu wdrażania Menedżer zasobów. 

Lista kontroli dostępu do punktu końcowego jest ulepszeniem zabezpieczeń dostępnym dla wdrożenia platformy Azure. Lista ACL zapewnia możliwość selektywnego zezwalania na ruch lub odmawiania go dla punktu końcowego maszyny wirtualnej. Ta funkcja filtrowania pakietów zapewnia dodatkową warstwę zabezpieczeń. Listy ACL sieci można określić tylko dla punktów końcowych. Nie można określić listy ACL dla sieci wirtualnej lub określonej podsieci znajdującej się w sieci wirtualnej. Zaleca się używanie sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) zamiast list kontroli dostępu, jeśli jest to możliwe. W przypadku korzystania z sieciowych grup zabezpieczeń lista kontroli dostępu do punktu końcowego zostanie zastąpiona i nie będzie już wymuszana. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Omówienie grup zabezpieczeń sieci](security-overview.md)

Listy ACL można skonfigurować przy użyciu programu PowerShell lub Azure Portal. Aby skonfigurować listę ACL sieci przy użyciu programu PowerShell, zobacz [Zarządzanie listami kontroli dostępu dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md). Aby skonfigurować listę ACL sieci przy użyciu Azure Portal, zobacz [jak skonfigurować punkty końcowe na maszynie wirtualnej](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Przy użyciu list ACL sieci można wykonać następujące czynności:

* Wybiórcze Zezwalanie na ruch przychodzący lub odmawianie go na podstawie zakresu adresów IPv4 podsieci zdalnej do wejściowego punktu końcowego maszyny wirtualnej.
* Lista zablokowanych adresów IP
* Utwórz wiele reguł na punkt końcowy maszyny wirtualnej
* Użyj kolejności reguł, aby upewnić się, że odpowiedni zestaw reguł jest stosowany w danym punkcie końcowym maszyny wirtualnej (od najniższego do najwyższego)
* Określ listę kontroli dostępu dla określonego adresu IPv4 podsieci zdalnej.

Zobacz artykuł dotyczący [limitów dotyczących platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) , aby uzyskać limity listy ACL.

## <a name="how-acls-work"></a>Jak działają listy ACL
Lista ACL jest obiektem, który zawiera listę reguł. Po utworzeniu listy ACL i zastosowaniu jej do punktu końcowego maszyny wirtualnej, filtrowanie pakietów odbywa się w węźle hosta maszyny wirtualnej. Oznacza to, że ruch ze zdalnych adresów IP jest filtrowany przez węzeł hosta dla zgodnych reguł listy ACL zamiast na maszynie wirtualnej. Zapobiega to wykorzystaniu przez maszynę wirtualną cennych cykli procesora podczas filtrowania pakietów.

Po utworzeniu maszyny wirtualnej jest umieszczana domyślna lista ACL, która blokuje cały ruch przychodzący. Jeśli jednak zostanie utworzony punkt końcowy dla (Port 3389), domyślna lista ACL zostanie zmodyfikowana tak, aby zezwalała na cały ruch przychodzący dla tego punktu końcowego. Ruch przychodzący z dowolnej podsieci zdalnej jest następnie dozwolony dla tego punktu końcowego i nie jest wymagane zapewnienie obsługi administracyjnej przez zaporę. Wszystkie inne porty są blokowane dla ruchu przychodzącego, o ile dla tych portów są tworzone punkty końcowe. Ruch wychodzący jest domyślnie dozwolony.

**Przykład domyślnej tabeli listy ACL**

| **Rule #** | **Podsieć zdalna** | **Punkt końcowy** | **Zezwalaj/Odmów** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Było |

## <a name="permit-and-deny"></a>Zezwalaj i Odmów
Można wybiórczo zezwolić na ruch sieciowy dla wejściowego punktu końcowego maszyny wirtualnej lub go odmówić, tworząc reguły określające wartość "Zezwalaj" lub "Odmów". Należy pamiętać, że domyślnie po utworzeniu punktu końcowego cały ruch jest dozwolony do punktu końcowego. Z tego powodu ważne jest, aby zrozumieć, jak utworzyć reguły zezwalania/odmowy i umieścić je w odpowiedniej kolejności pierwszeństwa, jeśli chcesz uzyskać szczegółową kontrolę nad ruchem sieciowym, który zezwolił na dostęp do punktu końcowego maszyny wirtualnej.

Kwestie, które należy wziąć pod uwagę:

1. **Brak listy ACL —** Domyślnie po utworzeniu punktu końcowego zezwalamy na wszystkie te punkty dla punktu końcowego.
2. **Zezwalaj —** Po dodaniu jednego lub kilku zakresów "Zezwalaj" domyślnie odrzucane są wszystkie inne zakresy. Tylko pakiety z dozwolonego zakresu adresów IP będą mogły komunikować się z punktem końcowym maszyny wirtualnej.
3. **Odmów —** Dodanie jednego lub kilku zakresów "Odmów" pozwala domyślnie zezwolić na wszystkie inne zakresy ruchu.
4. **Kombinacja zezwoleń i Odmów-** Możesz użyć kombinacji "Zezwalaj" i "Odmów", jeśli chcesz wydzielenie określony zakres adresów IP, aby można było zezwolić na dostęp lub odmówić.

## <a name="rules-and-rule-precedence"></a>Reguły i pierwszeństwo reguł
Listy ACL sieci można skonfigurować w określonych punktach końcowych maszyn wirtualnych. Na przykład można określić listę ACL sieci dla punktu końcowego RDP utworzonego na maszynie wirtualnej, która blokuje dostęp dla określonych adresów IP. W poniższej tabeli przedstawiono sposób udzielania dostępu do publicznych wirtualnych adresów IP (VIP) określonego zakresu w celu zezwolenia na dostęp do protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. *Obowiązuje najniższy porządek reguł pierwszeństwa* .

### <a name="multiple-rules"></a>Wiele reguł
W poniższym przykładzie, jeśli chcesz zezwolić na dostęp do punktu końcowego protokołu RDP tylko z dwóch publicznych zakresów adresów IPv4 (65.0.0.0/8 i 159.0.0.0/8), możesz to osiągnąć, określając dwie reguły zezwalania . W tym przypadku, ponieważ domyślnie jest tworzony protokół RDP dla maszyny wirtualnej, możesz chcieć zablokować dostęp do portu RDP na podstawie podsieci zdalnej. W poniższym przykładzie pokazano sposób udzielania dostępu do publicznych wirtualnych adresów IP (VIP) określonego zakresu w celu zezwolenia na dostęp do protokołu RDP. Wszystkie inne zdalne adresy IP są odrzucane. Dzieje się tak, ponieważ listy ACL sieci można skonfigurować dla określonego punktu końcowego maszyny wirtualnej i domyślnie odmowa dostępu.

**Przykład — wiele reguł**

| **Rule #** | **Podsieć zdalna** | **Punkt końcowy** | **Zezwalaj/Odmów** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Było |
| 200 |159.0.0.0/8 |3389 |Było |

### <a name="rule-order"></a>Kolejność reguł
Ponieważ można określić wiele reguł dla punktu końcowego, musi istnieć sposób organizowania reguł w celu ustalenia, która reguła ma pierwszeństwo. Kolejność reguł Określa pierwszeństwo. Listy ACL sieci mają *najniższą kolejność pierwszeństwa* reguł. W poniższym przykładzie punkt końcowy na porcie 80 jest selektywnie przyznany dostęp tylko do określonych zakresów adresów IP. Aby skonfigurować ten element, mamy regułę odmowy (reguła \# 100) dla adresów w przestrzeni 175.1.0.1/24. Druga reguła jest następnie określona z pierwszeństwem 200, który zezwala na dostęp do wszystkich innych adresów w obszarze 175.0.0.0/8.

**Przykład — pierwszeństwo reguł**

| **Rule #** | **Podsieć zdalna** | **Punkt końcowy** | **Zezwalaj/Odmów** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Zablokuj |
| 200 |175.0.0.0/8 |80 |Było |

## <a name="network-acls-and-load-balanced-sets"></a>Listy ACL sieci i zestawy o zrównoważonym obciążeniu
Listy ACL sieci można określić w punkcie końcowym zestawu o zrównoważonym obciążeniu. Jeśli lista ACL jest określona dla zestawu o zrównoważonym obciążeniu, lista ACL sieci zostanie zastosowana do wszystkich maszyn wirtualnych w tym zestawie o zrównoważonym obciążeniu. Na przykład jeśli zestaw o zrównoważonym obciążeniu jest tworzony przy użyciu portu 80, a zestaw o zrównoważonym obciążeniu zawiera 3 maszyny wirtualne, lista ACL sieci utworzona w punkcie końcowym "port 80" jednej maszyny wirtualnej będzie automatycznie stosowana do innych maszyn wirtualnych.

![Listy ACL sieci i zestawy o zrównoważonym obciążeniu](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Następne kroki
[Zarządzanie listami kontroli dostępu dla punktów końcowych przy użyciu programu PowerShell](virtual-networks-acl-powershell.md)

