---
title: Obsługiwane systemy operacyjne gościa dla stosu Azure | Dokumentacja firmy Microsoft
description: Te systemy operacyjne gościa może służyć na stosie Azure.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: ff3aea4e449e3d489b0c0f01345ecd9773c7d885
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systemy operacyjne gościa obsługiwane na stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="windows"></a>Windows
Stos Azure obsługuje systemy operacyjne Windows gościa, które są wymienione w poniższej tabeli: obrazów w witrynie Marketplace są dostępne do pobrania stos Azure. Obrazy klienta systemu Windows nie są dostępne w witrynie Marketplace.

Podczas wdrażania stos Azure injects odpowiedniej wersji agenta gościa w obrazie.

| System operacyjny | Opis | Dostępne w witrynie Marketplace |
| --- | --- | --- | --- | --- | --- |
| W systemie Windows Server w wersji 1709 | 64-bitowa | Podstawowe z kontenerami |
| Windows Server 2016 | 64-bitowa |  Centrum danych, podstawowe centrum danych centrum danych z kontenerami |
| Windows Server 2012 R2 | 64-bitowa |  Centrum danych |
| Windows Server 2012 | 64-bitowa |  Centrum danych |
| Windows Server 2008 R2 SP1 | 64-bitowa |  Centrum danych |
| Windows Server 2008 SP2 | 64-bitowa |  Przełącz własnego obrazu |
| Windows 10 *(patrz Uwaga 1)* | 64-bitowy, Pro i Enterprise | Przełącz własnego obrazu |

***Uwaga 1:****do wdrażania systemów operacyjnych klienta systemu Windows 10 na stosie Azure, musi mieć [Windows licencjonowania na użytkownika](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) lub zakupu za pośrednictwem dostawcy usług hostingowych wielodostępnej kwalifikowana ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Obrazy Marketplace są dostępne dla płatności jako — użytkownik użycia lub licencjonowania BYOL (EA/SPLA). Użycie zarówno w pojedynczym wystąpieniu Azure stosu nie jest obsługiwane. 

Tylko wersje Datacenter są dostępne w witrynie marketplace; klientów można przełączyć własnych obrazów server, także w innych wersjach.

## <a name="linux"></a>Linux

Dystrybucje systemu Linux wymienione w tym miejscu zawierają niezbędne systemu Windows Azure Linux Agent (WALA).

> [!NOTE]   
> Niestandardowe obrazy powinny zostać skompilowane z najnowszą wersją WALA publicznych. Na stosie Azure w wersji wcześniejszej niż 2.2.18 mogą nie działać poprawnie.  
>
> [init chmury](https://cloud-init.io/) na stosie Azure nie jest obsługiwana w tej chwili.

| Dystrybucja | Opis | Wydawca | Portal Marketplace |
| --- | --- | --- | --- | --- | --- |
| Kontener systemu Linux |  64-bitowa | CoreOS | Stable |
| Na podstawie centOS 6,9 | 64-bitowa | Nieautoryzowany Wave | Yes |
| 7.4 na podstawie centOS | 64-bitowa | Nieautoryzowany Wave | Yes |
| ClearLinux | 64-bitowa | ClearLinux.org | Yes |
| Debian 8 "Joasia." | 64-bitowa | credativ |  Yes |
| Debian 9 "Stretch" | 64-bitowa | credativ | Yes |
| Red Hat Enterprise Linux 7.x (oczekiwanie) | 64-bitowa | Red Hat |Przełącz własnego obrazu |
| SLES 11SP4 | 64-bitowa | SUSE | Yes |
| SLES 12SP3 | 64-bitowa | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-bitowa | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-bitowa | Canonical | Yes |

Inne dystrybucje systemu Linux mogą być obsługiwane w przyszłości.
