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
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521913"
---
# <a name="supported-platforms"></a>Obsługiwane platformy 

Na tej stronie przedstawiono platformy i środowiska obsługiwane przez usługę Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinacje środowisk<a name="vm-server"></a>

Usługa Azure Security Center obsługuje maszyny wirtualne i serwery w różnych typach środowisk hybrydowych:

* Tylko platforma Azure
* Platforma Azure i lokalna
* Platforma Azure i inne chmury
* Platforma Azure, inne chmury i lokalnie

W przypadku środowiska platformy Azure aktywowanego w ramach subskrypcji platformy Azure usługa Azure Security Center automatycznie odnajduje zasoby usługi IaaS wdrożone w ramach subskrypcji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Centrum zabezpieczeń zależy od [agenta analizy dzienników](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Upewnij się, że na komputerach jest uruchomiony jeden z obsługiwanych systemów operacyjnych dla tego agenta, jak opisano na następujących stronach:

* [Agent usługi Log Analytics dla obsługiwanych przez system operacyjny Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agent usługi Log Analytics dla systemów operacyjnych obsługiwanych przez system Linuksa](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Upewnij się również, że agent usługi Log Analytics jest [poprawnie skonfigurowany do wysyłania danych do usługi Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Aby dowiedzieć się więcej o określonych funkcjach Centrum zabezpieczeń dostępnych w systemach Windows i Linux, zobacz [Pokrycie funkcji dla komputerów](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Zarządzane usługi maszyn wirtualnych<a name="virtual-machine"></a>

Maszyny wirtualne są również tworzone w ramach subskrypcji klienta w ramach niektórych usług zarządzanych przez platformę Azure, takich jak usługi Azure Kubernetes (AKS), Azure Databricks i inne. Usługa Security Center odnajduje te maszyny wirtualne, a agent usługi Log Analytics można zainstalować i skonfigurować, jeśli obsługiwany system operacyjny jest dostępny.

## <a name="cloud-services"></a>Usługi w chmurze<a name="cloud-services"></a>

Obsługiwane są również maszyny wirtualne uruchamiane w usłudze w chmurze. Monitorowane są tylko role sieci web i procesowe usług w chmurze, które działają w gniazdach produkcyjnych. Aby dowiedzieć się więcej o usługach w chmurze, zobacz [Omówienie usług w chmurze Azure.](../cloud-services/cloud-services-choose-me.md)

Ochrona maszyn wirtualnych przebywających w usłudze Azure Stack jest również obsługiwana. Aby uzyskać więcej informacji na temat integracji usługi Security Center z usługą Azure Stack, zobacz [Dołączanie maszyn wirtualnych usługi Azure Stack do centrum zabezpieczeń.](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Usługa Security Center zbiera dane za pomocą agenta usługi Log Analytics.](security-center-enable-data-collection.md)
- Dowiedz się, jak [Usługa Security Center zarządza i zabezpiecza dane.](security-center-data-security.md)
- Dowiedz się, jak [zaplanować i zrozumieć zagadnienia dotyczące projektu, aby przyjąć usługę Azure Security Center.](security-center-planning-and-operations-guide.md)