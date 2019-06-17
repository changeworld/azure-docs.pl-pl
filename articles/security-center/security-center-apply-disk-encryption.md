---
title: Zastosuj szyfrowanie dysków w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Zastosuj szyfrowanie dysków**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d285461aef1e56dfd08a162e51ada0340d6eeae4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095425"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Zastosuj szyfrowanie dysków w usłudze Azure Security Center
Usługa Azure Security Center zaleca zastosowanie szyfrowania dysków, jeśli masz dyski Windows lub maszyny Wirtualnej systemu Linux, które nie są szyfrowane przy użyciu usługi Azure Disk Encryption. Disk Encryption umożliwia szyfrowanie dysków Windows i maszyn wirtualnych IaaS z systemem Linux.  Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej.

Disk Encryption korzysta z branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux. Te funkcje umożliwiają szyfrowanie systemu operacyjnego i danych, aby chronić i ochronie danych i spełniać zabezpieczeń i zgodności obowiązującymi w organizacji. Szyfrowanie dysków jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzaniu wpisami tajnymi w ramach subskrypcji usługi Key Vault i kluczami szyfrowania dysku, podczas gdy zapewnienie, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w Twojej [Usługi azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Usługa Azure Disk Encryption jest obsługiwane na następujących Windows systemy operacyjne serwerów — Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Szyfrowanie dysku jest obsługiwane w następujących systemów operacyjnych Linux serwera — Ubuntu, CentOS, SUSE i systemem SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **Zastosuj szyfrowanie dysków**.
2. W **Zastosuj szyfrowanie dysków** bloku zobaczysz listę maszyn wirtualnych, dla których zaleca się szyfrowanie dysków.
3. Postępuj zgodnie z instrukcjami, aby zastosować szyfrowanie tych maszyn wirtualnych.

![][1]

Aby zaszyfrować maszyny wirtualne platformy Azure, które zostały zidentyfikowane przez usługę Security Center jako wymagające szyfrowania, zalecamy następujące czynności:

* Zainstaluj i skonfiguruj program Azure PowerShell. Umożliwia to uruchamianie poleceń programu PowerShell, wymagane do skonfigurowania wymagań wstępnych dotyczących szyfrowania maszyn wirtualnych platformy Azure.
* Uzyskaj i uruchom skrypt programu Azure PowerShell Azure dysku szyfrowanie wymagań wstępnych.
* Zaszyfruj maszyny wirtualne.

[Szyfrowanie maszyny Wirtualnej IaaS Windows przy użyciu programu Azure PowerShell](../security/quick-encrypt-vm-powershell.md) przeprowadzi Cię przez następujące kroki. W tym temacie założono, że używasz komputera klienta Windows, w którym można skonfigurować szyfrowanie dysków.

Istnieje wiele metod, których można użyć usługi Azure Virtual Machines. Jeśli użytkownik ma dużą wiedzę na temat programu Azure PowerShell lub interfejsu wiersza polecenia Azure, może wybrać inne rozwiązania. Aby dowiedzieć się więcej o tych innych metod, zobacz [usługa Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Zobacz także
W tym dokumencie pokazano sposób implementacji zalecenia usługi Security Center "Zastosuj szyfrowanie dysków". Aby dowiedzieć się więcej o szyfrowaniu dysku, zobacz następujące tematy:

* [Szyfrowanie i zarządzanie kluczami za pomocą usługi Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (wideo, 36 min 39 sek.) — Dowiedz się, jak chronić i chronić swoje dane za pomocą przystawki Zarządzanie dyskami szyfrowania maszyn wirtualnych IaaS oraz usługi Azure Key Vault.
* [Usługa Azure disk encryption](../security/azure-security-disk-encryption-overview.md) (dokumenty) — Dowiedz się, jak włączyć szyfrowanie dysku dla Windows i maszyn wirtualnych systemu Linux.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
