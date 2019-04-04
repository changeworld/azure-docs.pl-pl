---
title: Obsługiwane systemy operacyjne gościa dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Te systemy operacyjne gościa może służyć w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/17/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a271edd710356ff612a4558034e9f7459a17ecaa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666294"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systemy operacyjne gościa obsługiwane w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

## <a name="windows"></a>Windows

Usługa Azure Stack obsługuje systemy operacyjne Windows gościa, wymienione w poniższej tabeli:

| System operacyjny | Opis | Dostępne w witrynie Marketplace |
| --- | --- | --- |
| W systemie Windows Server w wersji 1709 | 64-bitowa | Core za pomocą kontenerów |
| Windows Server 2016 | 64-bitowa |  Centrum danych, podstawowe centrum danych, Datacenter z kontenerami |
| Windows Server 2012 R2 | 64-bitowa |  Centrum danych |
| Windows Server 2012 | 64-bitowa |  Centrum danych |
| Windows Server 2008 R2 SP1 | 64-bitowa |  Centrum danych |
| Windows Server 2008 SP2 | 64-bitowa |  Możliwość skorzystania z własnego obrazu |
| Windows 10 *(patrz Uwaga 1)* | 64-bitowy, Pro i Enterprise | Możliwość skorzystania z własnego obrazu |

> [!NOTE]
> Aby wdrożyć systemy operacyjne klienta systemu Windows 10 w usłudze Azure Stack, konieczne jest posiadanie [Windows licencjonowania na użytkownika](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) lub kupić w ramach kwalifikowaną dostawcy usług hostingowych wielodostępnej ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Obrazy z Marketplace są dostępne dla płatności za użycie lub licencjonowania BYOL (umowy EA/SPLA). Zarówno w pojedynczym wystąpieniu usługi Azure Stack nie jest obsługiwane. Podczas wdrażania usługi Azure Stack wprowadza odpowiedniej wersji agenta gościa do obrazu.

Wersje Datacenter są dostępne w witrynie marketplace termin pobieranie; Klienci mogą przenieść swoje własne obrazy serwera, w tym inne wersje. Obrazy klienta Windows nie są dostępne w portalu Marketplace.

## <a name="linux"></a>Linux

Dystrybucje systemu Linux wyświetlane jako dostępne w portalu Marketplace zawierają niezbędne Windows Azure Linux Agent (WALA). W przypadku przeniesienia z własnego obrazu do usługi Azure Stack, postępuj zgodnie z wytycznymi podanymi w [Dodawanie obrazów systemu Linux do usługi Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Obrazy niestandardowe powinny zostać skompilowane z najnowszą wersją publiczną WALA lub z wersją 2.2.20. W usłudze Azure Stack w wersji wcześniejszej niż 2.2.20 i między 2.2.20 i 2.2.35.1 (wyłącznie) może nie działać prawidłowo. 
>
> [Pakiet cloud-init](https://cloud-init.io/) nie jest obsługiwana w usłudze Azure Stack w tej chwili.

| Dystrybucja | Opis | Wydawca | Portal Marketplace |
| --- | --- | --- | --- |
| 6,9 opartych na systemie centOS | 64-bitowa | Rogue Wave | Yes |
| Opartych na systemie centOS 7.4 | 64-bitowa | Rogue Wave | Yes |
| ClearLinux | 64-bitowa | ClearLinux.org | Yes |
| Kontener systemu Linux |  64-bitowa | CoreOS | Stable |
| Debian 8 "Jessie" | 64-bitowa | credativ |  Yes |
| Debian 9 "Stretch." | 64-bitowa | credativ | Yes |
| Red Hat Enterprise Linux 7.x | 64-bitowa | Red Hat |Możliwość skorzystania z własnego obrazu |
| SLES 11SP4 | 64-bitowa | SUSE | Yes |
| SLES 12SP3 | 64-bitowa | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-bitowa | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-bitowa | Canonical | Yes |
| Ubuntu 18.04-LTS | 64-bitowa | Canonical | Yes |

Aby uzyskać informacje dotyczące obsługi w systemie Red Hat Enterprise Linux, zobacz [firmy Red Hat i usługi Azure Stack: Często zadawane pytania dotyczące](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat portalu Marketplace usługi Azure Stack zobacz następujące artykuły:

- [Pobieranie elementów portalu Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)
