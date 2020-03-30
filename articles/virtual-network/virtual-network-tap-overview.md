---
title: Omówienie TAP sieci wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o sieci wirtualnej TAP. Tap sieci wirtualnej zapewnia głęboką kopię ruchu sieciowego maszyny wirtualnej, które mogą być przesyłane strumieniowo do modułu zbierającego pakiety.
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
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279613"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Usługa Azure virtual network TAP (Terminal Access Point) umożliwia ciągłe przesyłanie strumieniowe ruchu sieciowego maszyny wirtualnej do modułu zbierającego pakiety sieciowego lub narzędzia analitycznego. Narzędzie do tworzenia lub analizy jest dostarczane przez partnera [urządzenia wirtualnego sieci.](https://azure.microsoft.com/solutions/network-appliances/) Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane do pracy z siecią wirtualną TAP, zobacz [rozwiązania partnerów](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Tap sieci wirtualnej jest obecnie w wersji zapoznawczej we wszystkich regionach platformy Azure. Aby korzystać z tap sieci wirtualnej, należy <azurevnettap@microsoft.com> zarejestrować się w wersji zapoznawczej, wysyłając wiadomość e-mail z identyfikatorem subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail. Nie możesz korzystać z tej funkcji, dopóki nie otrzymasz wiadomości e-mail z potwierdzeniem. Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie powinna być używana dla obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Szczegółowe informacje można znaleźć w [dodatkowych warunkach użytkowania w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) platformy Microsoft Azure.

## <a name="virtual-network-tap-partner-solutions"></a>Rozwiązania partnerskie TAP w sieci wirtualnej

### <a name="network-packet-brokers"></a>Brokerzy pakietów sieciowych

- [Big Switch Duża tkanina monitorująca](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Pryzmaty Nubeva](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analiza zabezpieczeń, zarządzanie wydajnością sieci/aplikacji

- [Obudź bezpieczeństwa](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Chmura](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Ślad ciemny](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Cyberbezpieczeństwo Fidelis](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon ( Flowmon )](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [Platforma RSA NetWitness®](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Na poniższej ilustracji przedstawiono działanie tap sieci wirtualnej. Można dodać konfigurację TAP w [interfejsie sieciowym](virtual-network-network-interface.md) dołączonym do maszyny wirtualnej wdrożonej w sieci wirtualnej. Miejsce docelowe to adres IP sieci wirtualnej w tej samej sieci wirtualnej co monitorowany interfejs sieciowy lub [sieć wirtualna równorzędna.](virtual-network-peering-overview.md) Rozwiązanie modułu zbierającego dla sieci wirtualnej TAP można wdrożyć za modułem równoważenia obciążenia wewnętrznego platformy Azure w celu zapewnienia wysokiej dostępności. Aby ocenić opcje wdrażania dla poszczególnych rozwiązań, zobacz [rozwiązania partnerskie](#virtual-network-tap-partner-solutions).

![Jak działa tap sieci wirtualnej](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem tap sieci wirtualnej, musisz otrzymać wiadomość z potwierdzeniem, że jesteś zarejestrowany w wersji zapoznawczej i mieć jedną lub więcej maszyn wirtualnych utworzonych przy użyciu modelu wdrażania [usługi Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i rozwiązania partnerskiego do agregowania ruchu TAP w tym samym regionie platformy Azure. Jeśli nie masz rozwiązania partnerskiego w sieci wirtualnej, zobacz [rozwiązania partnerskie,](#virtual-network-tap-partner-solutions) aby go wdrożyć. Za pomocą tego samego zasobu TAP sieci wirtualnej można agregować ruch z wielu interfejsów sieciowych w tej samej lub innej subskrypcji. Jeśli monitorowane interfejsy sieciowe znajdują się w różnych subskrypcjach, subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Ponadto monitorowane interfejsy sieciowe i docelowy punkt końcowy do agregowania ruchu TAP mogą znajdować się w sieciach wirtualnych równorzędnych w tym samym regionie. Jeśli używasz tego modelu wdrażania upewnij się, że [komunikacja równorzędna sieci wirtualnej](virtual-network-peering-overview.md) jest włączona przed skonfigurowaniem tap sieci wirtualnej.

## <a name="permissions"></a>Uprawnienia

Konta używane do stosowania konfiguracji TAP w interfejsach sieciowych muszą być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano niezbędne akcje z poniższej tabeli:

| Akcja | Nazwa |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Wymagane do tworzenia, aktualizowania, odczytywania i usuwania zasobu TAP sieci wirtualnej |
| Microsoft.Network/networkInterfaces/read | Wymagane do odczytu zasobu interfejsu sieciowego, na którym zostanie skonfigurowany tap |
| Microsoft.Network/tapKonfiguracje/* | Wymagane do tworzenia, aktualizowania, odczytywania i usuwania konfiguracji TAP w interfejsie sieciowym |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć tap sieci wirtualnej](tutorial-tap-virtual-network-cli.md).
