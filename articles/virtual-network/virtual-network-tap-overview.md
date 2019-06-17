---
title: Usługa Azure virtual network TAP Przegląd | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze virtual network TAP. Sieć wirtualna wzorca TAP umożliwia głęboką kopię ruchu sieciowego maszyny wirtualnej, który może być przesyłany strumieniowo do modułu zbierającego w pakiecie.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593184"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Usługa Azure virtual network TAP (Terminal punktem dostępu) umożliwia ciągłego strumienia do narzędzia sieci pakiet modułu zbierającego lub analizy ruchu sieciowego maszyny wirtualnej. Narzędzie modułu zbierającego lub analizy są dostarczane przez [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) partnera. Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane do pracy z usługą virtual network TAP zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Sieć wirtualna wzorca TAP jest obecnie w wersji zapoznawczej we wszystkich regionach platformy Azure. Aby korzystać z PODSŁUCHU sieci wirtualnej, musisz zarejestrować się w wersji zapoznawczej, wysyłając wiadomość e-mail na adres <azurevnettap@microsoft.com> przy użyciu identyfikatora subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie można użyć funkcji, dopóki nie otrzymasz wiadomość e-mail z potwierdzeniem. Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług i nie powinna być używana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać szczegółowe informacje.

## <a name="virtual-network-tap-partner-solutions"></a>Rozwiązania partnerów wzorca TAP sieci wirtualnej

### <a name="network-packet-brokers"></a>Brokerzy pakietów sieciowych

- [Big przełącznika big Data, monitorowanie sieci szkieletowej](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Narzędzia analizy zabezpieczeń, zarządzanie wydajnością aplikacji/Sieć

- [Aktywne zabezpieczeń](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch chmury](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cyberbezpieczeństwa](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Platforma NetWitness® RSA](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Na poniższym obrazie jak wirtualne sieci działa wzorca TAP. Można dodać konfiguracji NACIŚNIJ [interfejs sieciowy](virtual-network-network-interface.md) dołączony do maszyny wirtualnej wdrożony w sieci wirtualnej. Miejsce docelowe jest adresem IP sieci wirtualnej w tej samej sieci wirtualnej jako interfejs sieciowy monitorowanych lub [skomunikowane równorzędnie wirtualnego](virtual-network-peering-overview.md) sieci. Rozwiązania modułu zbierającego dla sieci wirtualnej wzorca TAP można wdrożyć za zaporą [Azure wewnętrznego modułu równoważenia obciążenia](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) wysokiej dostępności. Umożliwia ocenę opcji wdrażania dla poszczególnych rozwiązań, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).

![Sieć wirtualna jak wzorca TAP działa](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem sieci wirtualnej wzorca TAP, muszą Odebrano wiadomość e-mail z potwierdzeniem, które są zarejestrowane w wersji zapoznawczej i mieć jeden lub więcej maszyn wirtualnych utworzonych przy użyciu [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) partnerem i modelu wdrażania rozwiązanie dla agregacji ruchu NACIŚNIJ w tym samym regionie platformy azure. Jeśli nie masz rozwiązania partnerskiego w usłudze virtual network, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions) Aby wdrożyć. Można użyć tej samej sieci wirtualnej zasobów wzorca TAP agregacji ruchu z wieloma interfejsami sieciowymi w tej samej lub różnych subskrypcji. W przypadku interfejsów sieciowych monitorowanych w różnych subskrypcjach, subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Ponadto interfejsów sieciowych monitorowanych i docelowy punkt końcowy dla agregacji ruchu wzorca TAP może być w równorzędnych sieciach wirtualnych, w tym samym regionie. Jeśli używasz tego modelu wdrażania upewnij się, że [wirtualne sieci równorzędne](virtual-network-peering-overview.md) jest włączona, przed skonfigurowaniem PODSŁUCHU sieci wirtualnej.

## <a name="permissions"></a>Uprawnienia

Konta, użyjesz do zastosowania wzorca TAP konfiguracji w interfejsach sieciowych muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany niezbędne czynności z poniższej tabeli:

| Akcja | Name (Nazwa) |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Wymagane do utworzenia, aktualizacji, Odczyt i usuwanie zasobów wybierz sieć wirtualną |
| Microsoft.Network/networkInterfaces/read | Wymagany do odczytu zasobu interfejsu sieciowego, skonfigurować wzorcu TAP |
| Microsoft.Network/tapConfigurations/* | Wymagane do utworzenia, aktualizacji, Odczyt i usuwanie konfiguracji wzorca TAP w interfejsie sieciowym |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie sieci wirtualnej NACIŚNIJ](tutorial-tap-virtual-network-cli.md).
