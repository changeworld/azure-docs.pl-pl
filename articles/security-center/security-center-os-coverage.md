---
title: Obsługiwane platformy w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument zawiera listę systemów Windows i Linux operatings obsługiwane w Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 1bb0b61fe8a74edd35bb096fd295aedc67f6efb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335894"
---
# <a name="supported-platforms-in-azure-security-center"></a>Obsługiwane platformy w Centrum zabezpieczeń Azure
Monitorowanie stanu zabezpieczeń i zalecenia są dostępne dla maszyn wirtualnych (VM), utworzone za pomocą zarówno classic i modeli wdrażania usługi Resource Manager i komputery.

> [!NOTE]
> Dowiedz się więcej o [klasycznego i modeli wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Obsługiwane platformy dla komputerów z systemem Windows i maszyny wirtualne
Obsługiwane systemy operacyjne Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Obsługiwane platformy dla komputerów z systemem Linux i maszyny wirtualne
Obsługiwane systemy operacyjne Linux:

* Wersje Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian w wersji 6, 7, 8, 9
* CentOS w wersji 5, 6, 7
* Red Hat Enterprise Linux (RHEL) w wersji 5, 6, 7
* SUSE Linux Enterprise Server (SLES) w wersji 11, 12
* Oracle Linux w wersji 5, 6, 7
* Linux Amazon 2012.09 za pośrednictwem 2017
* Biblioteki Openssl 1.1.0 jest obsługiwana tylko na platformach x86_64 (64-bitowe)

> [!NOTE]
> Analizy behawioralnej maszyny wirtualnej nie są jeszcze dostępne dla systemów operacyjnych Linux.
>
>

## <a name="vms-and-cloud-services"></a>Maszyny wirtualne i usługi w chmurze
Maszyn wirtualnych uruchomionych w usłudze chmury są również obsługiwane. Tylko chmury usługi sieci web i proces roboczy role uruchomione w środowisku produkcyjnym, które są monitorowane gniazda. Aby dowiedzieć się więcej na temat usługi w chmurze, zobacz [Omówienie usługi w chmurze](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Kolejne kroki

- [Przewodnik planowania Centrum zabezpieczeń Azure i obsługi](security-center-planning-and-operations-guide.md) — informacje o sposobie planowania i projektowania przyjęcie Centrum zabezpieczeń Azure
- [Alerty zabezpieczeń według typu w Centrum zabezpieczeń Azure](security-center-alerts-type.md#virtual-machine-behavioral-analysis) — Dowiedz się więcej na temat analizy behawioralnej maszyny wirtualnej i awarii zrzutu pamięci analizy w Centrum zabezpieczeń
- [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności
