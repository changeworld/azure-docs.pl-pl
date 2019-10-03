---
title: Zastosuj szyfrowanie dysków w Azure Security Center | Microsoft Docs
description: W tym dokumencie przedstawiono sposób implementacji zaleceń Azure Security Center **zastosowania szyfrowania dysków**.
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
ms.openlocfilehash: b3d05e71726ae37dd30bbb68ceb84b67a3bef0e5
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822274"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Zastosuj szyfrowanie dysków w Azure Security Center
Azure Security Center zaleca zastosowanie szyfrowania dysków, jeśli masz dyski maszyn wirtualnych z systemem Windows lub Linux, które nie są zaszyfrowane przy użyciu Azure Disk Encryption. Szyfrowanie dysków umożliwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS.  Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej.

Szyfrowanie dysków używa standardowej funkcji [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux. Te funkcje zapewniają system operacyjny i szyfrowanie danych, które pomagają chronić i zabezpieczać dane oraz spełniać zobowiązania dotyczące zabezpieczeń i zgodności organizacji. Szyfrowanie dysków jest zintegrowane z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , które ułatwiają kontrolowanie kluczy szyfrowania dysków i wpisów tajnych oraz zarządzanie nimi w subskrypcji Key Vault, dzięki czemu wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption jest obsługiwana w następujących systemach operacyjnych Windows Server — Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Szyfrowanie dysków jest obsługiwane w następujących systemach operacyjnych serwera Linux — Ubuntu, CentOS, SUSE i SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. W bloku **zalecenia** wybierz pozycję **Zastosuj szyfrowanie dysków**.
2. W bloku **Zastosuj szyfrowanie dysku** zostanie wyświetlona lista maszyn wirtualnych, dla których zalecane jest szyfrowanie dysków.
3. Postępuj zgodnie z instrukcjami, aby zastosować szyfrowanie do tych maszyn wirtualnych.

![][1]

Aby zaszyfrować Virtual Machines platformy Azure, które zostały zidentyfikowane przez Security Center jako wymagające szyfrowania, zalecamy wykonanie następujących czynności:

* Zainstaluj i skonfiguruj program Azure PowerShell. Umożliwia to uruchomienie poleceń programu PowerShell wymaganych do skonfigurowania wymagań wstępnych dotyczących szyfrowania Virtual Machines platformy Azure.
* Uzyskaj i uruchom Azure Disk Encryption wymagania wstępne Azure PowerShell skrypt.
* Zaszyfruj maszyny wirtualne.

[Szyfrowanie maszyny wirtualnej z systemem Windows IaaS z Azure PowerShell](../virtual-machines/linux/disk-encryption-powershell-quickstart.md) przeprowadzi Cię przez te kroki. W tym temacie przyjęto założenie, że używasz komputera klienckiego z systemem Windows, z którego można skonfigurować szyfrowanie dysków.

Istnieje wiele metod, których można użyć w przypadku usługi Azure Virtual Machines. Jeśli użytkownik ma dużą wiedzę na temat programu Azure PowerShell lub interfejsu wiersza polecenia Azure, może wybrać inne rozwiązania. Aby dowiedzieć się więcej na temat tych innych metod, zobacz [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób implementacji zalecenia Security Center "Zastosuj szyfrowanie dysków". Aby dowiedzieć się więcej na temat szyfrowania dysków, zobacz następujące tematy:

* [Szyfrowanie i zarządzanie kluczami za pomocą Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (wideo, 36 min 39 s) — Dowiedz się, jak używać funkcji zarządzania szyfrowaniem dysków dla maszyn wirtualnych IaaS i Azure Key Vault do ochrony danych.
* [Azure Disk Encryption](../security/azure-security-disk-encryption-overview.md) (dokument) — Dowiedz się, jak włączyć szyfrowanie dysków dla maszyn wirtualnych z systemami Windows i Linux.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) --Dowiedz się, jak skonfigurować zasady zabezpieczeń.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — zawiera wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
