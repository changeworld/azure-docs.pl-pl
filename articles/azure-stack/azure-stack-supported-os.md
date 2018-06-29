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
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 8d9337053c8905886ed4429d64f8ef5b4e2c7d14
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060451"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Systemy operacyjne gościa obsługiwane na stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="windows"></a>Windows

Stos Azure obsługuje systemy operacyjne Windows gościa wymienione w poniższej tabeli:

| System operacyjny | Opis | Dostępne w witrynie Marketplace |
| --- | --- | --- | --- | --- | --- |
| W systemie Windows Server w wersji 1709 | 64-bitowa | Podstawowe z kontenerami |
| Windows Server 2016 | 64-bitowa |  Centrum danych, podstawowe centrum danych centrum danych z kontenerami |
| Windows Server 2012 R2 | 64-bitowa |  Centrum danych |
| Windows Server 2012 | 64-bitowa |  Centrum danych |
| Windows Server 2008 R2 SP1 | 64-bitowa |  Centrum danych |
| Windows Server 2008 SP2 | 64-bitowa |  Przełącz własnego obrazu |
| Windows 10 *(patrz Uwaga 1)* | 64-bitowy, Pro i Enterprise | Przełącz własnego obrazu |

***Uwaga 1:*** *do wdrażania systemów operacyjnych klienta systemu Windows 10 na stosie Azure, musi mieć [Windows licencjonowania na użytkownika](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) lub zakupu za pośrednictwem dostawcy usług hostingowych wielodostępnej kwalifikowana ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Obrazy Marketplace są dostępne dla płatności jako — użytkownik użycia lub licencjonowania BYOL (EA/SPLA). Zarówno w pojedynczym wystąpieniu Azure stosu nie jest obsługiwane. Podczas wdrażania stos Azure injects odpowiedniej wersji agenta gościa w obrazie.

 Wersje Datacenter są dostępne w witrynie marketplace pobierania; klientów można przełączyć własnych obrazów server, także w innych wersjach. Obrazy klienta systemu Windows nie są dostępne w witrynie Marketplace.

## <a name="linux"></a>Linux

Dystrybucje systemu Linux wyświetlane jako dostępne w witrynie Marketplace zawiera niezbędne systemu Windows Azure Linux Agent (WALA). Stos Azure w przypadku przeniesienia własny obraz, postępuj zgodnie ze wskazówkami podanymi w [obrazów dodać Linux Azure stos](azure-stack-linux.md).

> [!NOTE]
> Niestandardowe obrazy powinny zostać skompilowane z najnowszą wersją WALA publicznych. Na stosie Azure w wersji wcześniejszej niż 2.2.18 mogą nie działać poprawnie.
>
> [init chmury](https://cloud-init.io/) na stosie Azure nie jest obsługiwana w tej chwili.

| Dystrybucja | Opis | Wydawca | Portal Marketplace |
| --- | --- | --- | --- | --- | --- |
| Na podstawie centOS 6,9 | 64-bitowa | Nieautoryzowany Wave | Yes |
| 7.4 na podstawie centOS | 64-bitowa | Nieautoryzowany Wave | Yes |
| ClearLinux | 64-bitowa | ClearLinux.org | Yes |
| Kontener systemu Linux |  64-bitowa | CoreOS | Stable |
| Debian 8 "Joasia." | 64-bitowa | credativ |  Yes |
| Debian 9 "Stretch" | 64-bitowa | credativ | Yes |
| Red Hat Enterprise Linux 7.x | 64-bitowa | Red Hat |Przełącz własnego obrazu |
| SLES 11SP4 | 64-bitowa | SUSE | Yes |
| SLES 12SP3 | 64-bitowa | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-bitowa | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-bitowa | Canonical | Yes |
| Ubuntu 18.04-LTS | 64-bitowa | Canonical | Yes |

Inne dystrybucje systemu Linux mogą być obsługiwane w przyszłości.

Red Hat Enterprise Linux informacje pomocy technicznej, można znaleźć [Red Hat i Azure stosu: często zadawane pytania](https://access.redhat.com/articles/3413531).
