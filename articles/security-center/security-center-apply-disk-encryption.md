---
title: Stosowanie szyfrowania dysku w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym dokumencie pokazano, jak zaimplementować zalecenie usługi Azure Security Center **Zastosuj szyfrowanie dysku**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604512"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Stosowanie szyfrowania dysku w usłudze Azure Security Center
Usługa Azure Security Center zaleca stosowanie szyfrowania dysku, jeśli masz dyski maszyny wirtualnej z systemem Windows lub Linux, których nie zaszyfrowano przy użyciu usługi Azure Disk Encryption. Usługa Disk Encryption umożliwia szyfrowanie dysków maszyn wirtualnych IaaS z systemem Windows i Linux.  Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej.

Szyfrowanie dysku wykorzystuje standardową w branży funkcję [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcję [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux. Te funkcje zapewniają szyfrowanie systemu operacyjnego i danych, aby chronić i chronić dane oraz spełniać zobowiązania dotyczące bezpieczeństwa i zgodności z przepisami organizacji. Szyfrowanie dysków jest zintegrowane z [usługą Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w ramach subskrypcji usługi Key Vault oraz zarządzanie nimi, zapewniając jednocześnie, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w magazynie [Azure.](https://azure.microsoft.com/documentation/services/storage/)

> [!NOTE]
> Szyfrowanie dysków platformy Azure jest obsługiwane w następujących systemach operacyjnych windows server — Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Szyfrowanie dysku jest obsługiwane w następujących systemach operacyjnych serwerów Linux - Ubuntu, CentOS, SUSE i SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Wdrożenie zalecenia
1. W bloku **Zalecenia** wybierz pozycję **Zastosuj szyfrowanie dysku**.
2. W bloku **Zastosuj szyfrowanie dysku** zostanie wyświetlona lista maszyn wirtualnych, dla których zalecane jest szyfrowanie dysków.
3. Postępuj zgodnie z instrukcjami, aby zastosować szyfrowanie do tych maszyn wirtualnych.

![][1]

Aby zaszyfrować maszyny wirtualne platformy Azure, które zostały zidentyfikowane przez usługę Security Center jako wymagające szyfrowania, zalecamy wykonanie następujących kroków:

* Zainstaluj i skonfiguruj program Azure PowerShell. Dzięki temu można uruchomić polecenia programu PowerShell wymagane do skonfigurowania wymagań wstępnych wymaganych do szyfrowania maszyn wirtualnych platformy Azure.
* Uzyskiwanie i uruchamianie skryptu wymagań wstępnych szyfrowania dysków platformy Azure i uruchamiania.
* Szyfruj swoje maszyny wirtualne.

[Szyfrowanie maszyny Wirtualnej IaaS systemu Windows za pomocą programu Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) przeprowadzi Cię przez te kroki. W tym temacie przyjęto założenie, że używasz komputera klienckiego systemu Windows, z którego można skonfigurować szyfrowanie dysku.

Istnieje wiele metod, które mogą być używane dla maszyn wirtualnych platformy Azure. Jeśli użytkownik ma dużą wiedzę na temat programu Azure PowerShell lub interfejsu wiersza polecenia Azure, może wybrać inne rozwiązania. Aby dowiedzieć się więcej o tych innych podejściach, zobacz [Szyfrowanie dysków platformy Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Zobacz też
W tym dokumencie pokazano, jak zaimplementować zalecenie Centrum zabezpieczeń "Zastosuj szyfrowanie dysku". Aby dowiedzieć się więcej o szyfrowaniu dysku, zobacz:

* [Szyfrowanie i zarządzanie kluczami za pomocą usługi Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (wideo, 36 min 39 s) — dowiedz się, jak używać zarządzania szyfrowaniem dysków dla maszyn wirtualnych IaaS i usługi Azure Key Vault w celu ochrony i ochrony danych.
* [Szyfrowanie dysków platformy Azure](../security/fundamentals/encryption-overview.md) (dokument) — dowiedz się, jak włączyć szyfrowanie dysków dla maszyn wirtualnych z systemem Windows i Linux.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — dowiedz się, jak skonfigurować zasady zabezpieczeń.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — dowiedz się, jak zalecenia pomagają chronić zasoby platformy Azure.
* [Blog usługi Azure Security](https://blogs.msdn.com/b/azuresecurity/) — znajdź wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
