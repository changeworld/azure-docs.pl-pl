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
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473275"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Stosowanie szyfrowania dysku w usłudze Azure Security Center

Usługa Azure Security Center zaleca używanie szyfrowania dysków platformy Azure na dyskach niezaszyfrowanych zarówno na maszynach wirtualnych systemu Windows, jak i systemu Linux. Usługa Disk Encryption umożliwia szyfrowanie dysków maszyn wirtualnych IaaS z systemem Windows i Linux.  Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej.

Szyfrowanie dysku wykorzystuje standardową w branży funkcję [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcję [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux. Te funkcje zapewniają szyfrowanie systemu operacyjnego i danych, aby chronić i chronić dane oraz spełniać zobowiązania dotyczące bezpieczeństwa i zgodności z przepisami organizacji. Szyfrowanie dysków jest zintegrowane z [usługą Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) aby ułatwić kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w ramach subskrypcji usługi Key Vault oraz zarządzanie nimi, zapewniając jednocześnie, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w magazynie [Azure.](https://azure.microsoft.com/documentation/services/storage/)

Aby uzyskać listę obsługiwanych wersji systemu Windows i Linux, zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) w dokumentacji szyfrowania dysków azure.

## <a name="implement-the-recommendation"></a>Wdrożenie zalecenia
1. Na stronie **Zalecenia** wybierz opcję **Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych**.
2. Z zasobów w **złej kondycji**wybierz maszynę wirtualną, dla której zalecane jest szyfrowanie dysku.
3. Postępuj zgodnie z instrukcjami, aby zastosować szyfrowanie do tych maszyn wirtualnych.

![Stosowanie szyfrowania dysku](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Aby zaszyfrować maszyny wirtualne platformy Azure, które zostały zidentyfikowane przez usługę Security Center jako wymagające szyfrowania, zalecamy wykonanie następujących kroków:

* Zainstaluj i skonfiguruj program Azure PowerShell. Dzięki temu można uruchomić polecenia programu PowerShell wymagane do skonfigurowania wymagań wstępnych wymaganych do szyfrowania maszyn wirtualnych platformy Azure.
* Uzyskiwanie i uruchamianie skryptu wymagań wstępnych szyfrowania dysków platformy Azure i uruchamiania.
* Szyfruj swoje maszyny wirtualne.

[Szyfruj maszynę wirtualną systemu Windows IaaS za pomocą programu Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) — przeprowadzi Cię przez te kroki i zakłada, że używasz komputera klienckiego systemu Windows, z którego można skonfigurować szyfrowanie dysku.

Istnieje wiele metod, które mogą być używane dla maszyn wirtualnych platformy Azure. Jeśli jesteś już dobrze zorientowany w usłudze Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz wolisz używać alternatywnych metod. Aby dowiedzieć się więcej o tych innych podejściach, zobacz [Szyfrowanie dysków platformy Azure](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Zobacz też
W tym dokumencie pokazano, jak zaimplementować zalecenie Centrum zabezpieczeń "Zastosuj szyfrowanie dysku". Aby dowiedzieć się więcej o szyfrowaniu dysku, zobacz:

* [Szyfrowanie i zarządzanie kluczami za pomocą usługi Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (wideo, 36 min 39 s) — dowiedz się, jak używać zarządzania szyfrowaniem dysków dla maszyn wirtualnych IaaS i usługi Azure Key Vault w celu ochrony i ochrony danych.
* [Szyfrowanie dysków platformy Azure](../security/fundamentals/encryption-overview.md) (dokument)--Dowiedz się, jak włączyć szyfrowanie dysków dla maszyn wirtualnych z systemem Windows i Linux.