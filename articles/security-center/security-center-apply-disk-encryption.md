---
title: Zastosuj szyfrowanie dysków w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przedstawia sposób wykonania zalecenia Centrum zabezpieczeń Azure ** zastosowanie szyfrowania dysku **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866261"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Zastosuj szyfrowanie dysków w Centrum zabezpieczeń Azure
Centrum zabezpieczeń Azure zaleca zastosowanie szyfrowania dysków, jeśli masz dysków systemu Windows lub maszyny Wirtualnej systemu Linux, które nie są szyfrowane przy użyciu szyfrowania dysków Azure. Szyfrowanie dysku umożliwia zaszyfrowanie dysków systemu Windows i maszyny Wirtualnej systemu Linux IaaS.  Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej.

Szyfrowanie dysków korzysta ze standardu branżowego [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcji systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux. Funkcje te zapewniają szyfrowanie danych i systemu operacyjnego, aby chronić i ochrony danych i spełnia Twoje organizacyjnej zobowiązań zabezpieczeń i zgodności. Szyfrowanie dysków jest zintegrowany z [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzać kluczami szyfrowania dysku i kluczy tajnych w ramach subskrypcji usługi Key Vault, podczas gdy zapewnienie, że wszystkie dane na dyskach maszyny Wirtualnej są szyfrowane, gdy w Twojej [Magazynu azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Szyfrowanie dysków Azure jest obsługiwane w następujących systemów operacyjnych Windows server - Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2. Szyfrowanie dysków jest obsługiwana w następujących serwera systemach operacyjnych Linux - Ubuntu, CentOS, SUSE i SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Wykonania zalecenia
1. W **zalecenia** bloku, wybierz opcję **zastosować szyfrowanie dysku**.
2. W **zastosować szyfrowanie dysku** bloku, zobacz listę maszyn wirtualnych, dla których szyfrowanie dysków jest zalecane.
3. Postępuj zgodnie z instrukcjami, aby zastosować szyfrowanie do tych maszyn wirtualnych.

![][1]

Aby zaszyfrować maszyny wirtualne platformy Azure, które zostały zidentyfikowane przez Centrum zabezpieczeń jako wymagające szyfrowania, zaleca się następujące czynności:

* Zainstaluj i skonfiguruj program Azure PowerShell. Umożliwia to uruchamianie poleceń programu PowerShell wymaganych do skonfigurowania wymagań wstępnych dotyczących szyfrowania maszyn wirtualnych platformy Azure.
* Uzyskaj i uruchom skrypt programu PowerShell systemu Azure wymagania wstępne szyfrowania dysków Azure.
* Zaszyfruj maszyny wirtualne.

[Szyfrowanie maszyny wirtualnej platformy Azure](security-center-disk-encryption.md) przedstawiono te kroki.  W tym temacie założono, że używasz systemu Windows 10 jako komputer kliencki, w którym można skonfigurować szyfrowanie dysków.

Istnieje wiele metod, które mogą być używane dla maszyn wirtualnych platformy Azure. Jeśli użytkownik ma dużą wiedzę na temat programu Azure PowerShell lub interfejsu wiersza polecenia Azure, może wybrać inne rozwiązania. Aby dowiedzieć się więcej o tych innych metod, zobacz [szyfrowania dysków Azure](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Zobacz też
Ten dokument pokazano sposób wykonania zalecenia Centrum zabezpieczeń "Zastosuj szyfrowanie dysków". Aby dowiedzieć się więcej na temat szyfrowania dysków, zobacz następujące tematy:

* [Szyfrowanie i klucz Zarządzanie za pomocą usługi Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (wideo, 36 min 39 s) — informacje o sposobie użyj przystawki Zarządzanie dyskami szyfrowania dla maszyn wirtualnych IaaS i usługi Azure Key Vault ułatwia ochronę i ochrony danych.
* [Szyfrowanie maszyny wirtualnej platformy Azure](security-center-disk-encryption.md) (dokument) — informacje o szyfrowaniu maszynach wirtualnych platformy Azure.
* [Szyfrowanie dysków Azure](../security/azure-security-disk-encryption.md) (dokument) — Dowiedz się, jak włączyć szyfrowanie dysku dla systemu Windows i maszyn wirtualnych systemu Linux.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń platformy Azure i zgodności.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
