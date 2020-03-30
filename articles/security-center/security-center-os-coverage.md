---
title: Platformy obsługiwane przez usługę Azure Security Center | Dokumenty firmy Microsoft
description: Ten dokument zawiera listę platform obsługiwanych przez usługę Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208825"
---
# <a name="supported-platforms"></a>Obsługiwane platformy 

## <a name="virtual-machines--servers"></a>Maszyny wirtualne / serwery<a name="vm-server"></a>

Usługa Security Center obsługuje maszyny wirtualne / serwery w różnych typach środowisk hybrydowych:

* Tylko platforma Azure
* Platforma Azure i lokalna
* Platforma Azure i inne chmury
* Platforma Azure, inne chmury i lokalnie

W przypadku środowiska platformy Azure aktywowanego w ramach subskrypcji platformy Azure usługa Azure Security Center automatycznie odnajduje zasoby usługi IaaS wdrożone w ramach subskrypcji.

> [!NOTE]
> Aby otrzymać pełny zestaw funkcji zabezpieczeń, musisz mieć [agenta analizy dzienników,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)który jest używany przez usługę Azure Security Center, zainstalowany i [poprawnie skonfigurowany do wysyłania danych do usługi Azure Security Center.](security-center-enable-data-collection.md#manual-agent)

W poniższych sekcjach przedstawiono obsługiwane systemy operacyjne serwera, w których można uruchomić [agenta usługi Log Analytics,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)który jest używany przez usługę Azure Security Center.

### <a name="windows-server-operating-systems"></a>Systemy operacyjne Windows Server<a name="os-windows"></a>

|System operacyjny|Obsługiwane przez usługę Azure Security Center|Obsługa integracji z programem Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Aby dowiedzieć się więcej o obsługiwanych funkcjach systemów operacyjnych Windows, wymienionych powyżej, zobacz [Funkcje obsługiwane przez maszynę wirtualną/ serwer](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Systemy operacyjne Windows<a name="os-windows (non-server)"></a>

Usługa Azure Security Center integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn wirtualnych opartych na systemie Windows.

### <a name="linux-operating-systems"></a>Systemy operacyjne Linux<a name="os-linux"></a>

64-bitowy

* CentOS 6 i 7
* Amazon Linux 2017.09
* Oracle Linux 6 i Oracle Linux 7
* Serwer Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bitowa
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS i 16.04 LTS

> [!NOTE]
> Ponieważ lista obsługiwanych systemów operacyjnych Linux stale się zmienia, jeśli wolisz, kliknij [tutaj,](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) aby wyświetlić najbardziej aktualną listę obsługiwanych wersji, w przypadku, gdy nastąpiły zmiany od czasu ostatniej publikacji tego tematu.

Aby dowiedzieć się więcej o obsługiwanych funkcjach dla systemów operacyjnych Linux, wymienionych powyżej, zobacz [Funkcje obsługiwane przez maszynę wirtualną / serwer](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Zarządzane usługi maszyn wirtualnych<a name="virtual-machine"></a>

Maszyny wirtualne są również tworzone w ramach subskrypcji klienta w ramach niektórych usług zarządzanych platformy Azure, takich jak usługi Azure Kubernetes (AKS), Azure Databricks i inne. Te maszyny wirtualne są również odnajdowane przez usługę Azure Security Center, a agent analizy dzienników można zainstalować i skonfigurować zgodnie z [obsługiwanymi systemami operacyjnymi Windows/Linux,](#os-windows)wymienionych powyżej.

### <a name="cloud-services"></a>Usługi w chmurze<a name="cloud-services"></a>

Obsługiwane są również maszyny wirtualne uruchamiane w usłudze w chmurze. Monitorowane są tylko role sieci web i procesowe usług w chmurze, które działają w gniazdach produkcyjnych. Aby dowiedzieć się więcej o usługach w chmurze, zobacz [Omówienie usług w chmurze Azure.](../cloud-services/cloud-services-choose-me.md)

Ochrona maszyn wirtualnych zamieszkałych w usłudze Azure Stack jest również obsługiwana. Aby uzyskać więcej informacji na temat integracji usługi Security Center z usługą Azure Stack, zobacz [Dołączanie maszyn wirtualnych usługi Azure Stack do centrum zabezpieczeń.](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Usługa Security Center zbiera dane i agenta analizy dzienników](security-center-enable-data-collection.md).
- Dowiedz się, jak [Usługa Security Center zarządza i zabezpiecza dane.](security-center-data-security.md)
- Dowiedz się, jak [zaplanować i zrozumieć zagadnienia dotyczące projektu, aby przyjąć usługę Azure Security Center.](security-center-planning-and-operations-guide.md)
- Dowiedz się więcej o [funkcjach dostępnych dla różnych środowisk chmury.](security-center-services.md)
- Dowiedz się więcej o [ochronie przed zagrożeniami dla komputerów z systemem Windows i Linux w usłudze Azure Security Center](threat-protection.md#windows-machines).
