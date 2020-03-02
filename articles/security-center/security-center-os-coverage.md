---
title: Platformy obsługiwane przez Azure Security Center | Microsoft Docs
description: Ten dokument zawiera listę platform obsługiwanych przez Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208825"
---
# <a name="supported-platforms"></a>Obsługiwane platformy 

## Maszyny wirtualne/serwery<a name="vm-server"></a>

Security Center obsługuje maszyny wirtualne/serwery w różnych typach środowisk hybrydowych:

* Tylko platforma Azure
* Azure i lokalnie
* Platforma Azure i inne chmury
* Azure, inne chmury i lokalne

W przypadku środowiska platformy Azure aktywowanego w ramach subskrypcji platformy Azure usługa Azure Security Center automatycznie odnajdzie zasoby IaaS wdrożone w ramach subskrypcji.

> [!NOTE]
> Aby uzyskać pełny zestaw funkcji zabezpieczeń, musisz mieć [agenta log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), który jest używany przez Azure Security Center, zainstalowanych i [prawidłowo skonfigurowany do wysyłania danych do Azure Security Center](security-center-enable-data-collection.md#manual-agent).

W poniższych sekcjach wymieniono obsługiwane systemy operacyjne serwera, na których można uruchomić [agenta log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), który jest używany przez Azure Security Center.

### Systemy operacyjne Windows Server<a name="os-windows"></a>

|OS|Obsługiwane przez Azure Security Center|Obsługa integracji z usługą Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Aby dowiedzieć się więcej o obsługiwanych funkcjach systemów operacyjnych Windows wymienionych powyżej, zobacz [funkcje obsługiwane przez maszynę wirtualną/serwer](security-center-services.md#vm-server-features).

### Systemy operacyjne Windows<a name="os-windows (non-server)"></a>

Azure Security Center integruje się z usługami platformy Azure w celu monitorowania maszyn wirtualnych opartych na systemie Windows i ich ochrony.

### Systemy operacyjne Linux<a name="os-linux"></a>

64-bitowy

* CentOS 6 i 7
* Amazon Linux 2017,09
* Oracle Linux 6 i Oracle Linux 7
* Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS i 18,04 LTS
* SUSE Linux Enterprise Server 12

32 — bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS i 16,04 LTS

> [!NOTE]
> Ponieważ lista obsługiwanych systemów operacyjnych Linux jest ciągle zmieniana, kliknij [tutaj](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) , aby wyświetlić najbardziej aktualną listę obsługiwanych wersji, na wypadek zmiany od momentu ostatniej publikacji tego tematu.

Aby dowiedzieć się więcej o obsługiwanych funkcjach dla systemów operacyjnych Linux wymienionych powyżej, zobacz [funkcje obsługiwane przez maszynę wirtualną/serwer](security-center-services.md#vm-server-features).

### Zarządzane usługi maszyny wirtualnej<a name="virtual-machine"></a>

Maszyny wirtualne są również tworzone w ramach subskrypcji klienta jako część niektórych usług zarządzanych przez platformę Azure, takich jak Azure Kubernetes (AKS), Azure Databricks i wiele innych. Te maszyny wirtualne są również odnajdywane przez Azure Security Center, a Agent usługi log Analytics można zainstalować i skonfigurować zgodnie z obsługiwanymi [systemami operacyjnymi Windows/Linux](#os-windows).

### Cloud Services<a name="cloud-services"></a>

Obsługiwane są również maszyny wirtualne działające w usłudze w chmurze. Monitorowane są tylko role sieć Web i proces roboczy usług Cloud Services, które działają w gniazdach produkcyjnych. Aby dowiedzieć się więcej o usługach w chmurze, zobacz [Omówienie usługi Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Obsługiwane jest również ochrona Virtual Machines znajdujących się w Azure Stack. Aby uzyskać więcej informacji na temat integracji Security Center z Azure Stack, zobacz Dołączanie [Azure Stack maszyn wirtualnych do Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Dowiedz się, jak [planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center](security-center-planning-and-operations-guide.md).
- Dowiedz się więcej o [funkcjach dostępnych dla różnych środowisk chmury](security-center-services.md).
- Dowiedz się więcej o [ochronie przed zagrożeniami dla maszyn z systemami Windows i Linux w Azure Security Center](threat-protection.md#windows-machines).
