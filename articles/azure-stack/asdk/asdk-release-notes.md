---
title: Informacje o wersji programu Microsoft Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące usługi Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915887"
---
# <a name="asdk-release-notes"></a>Informacje o wersji ASDK

Ten artykuł zawiera informacje o zmianach, poprawki i znane problemy w usłudze Azure Stack Development Kit (ASDK). Jeśli nie masz pewności, której wersji używasz, możesz to zrobić [korzystanie z portalu, aby sprawdzić](../azure-stack-updates.md#determine-the-current-version).

Na bieżąco what's new in ASDK subskrybując [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanału informacyjnego](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-1903"></a>Build 1903

Ładunek 1903 nie zawiera wersji ASDK.

## <a name="build-11902069"></a>Tworzenie 1.1902.0.69

### <a name="new-features"></a>Nowe funkcje

- Kompilacja 1902 wprowadza nowy interfejs użytkownika w portalu administratora usługi Azure Stack do tworzenia plany, oferty, przydziały i planów dodatków. Aby uzyskać więcej informacji, w tym ze zrzutami ekranu, zobacz [Tworzenie planów, ofert i przydziałów](../azure-stack-create-plan.md).

- Aby uzyskać listę innych zmian i ulepszeń w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1902.md#improvements) informacje o wersji w usłudze Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Znane problemy

- Wykryto problem dotyczący porzucenia pakietów ponad 1450 bajtów do wewnętrznego obciążenia Balancer (ILB). Problem jest następstwem ustawienia MTU na hoście jest za mała, aby pomieścić VXLAN hermetyzowanych pakietów, przechodzących przez rolą, która od 1901 została przeniesiona do hosta. Istnieją co najmniej dwa scenariusze, które mogą wystąpić w przypadku których Zaobserwowaliśmy ten problem, objawiać:

  - Zapytania SQL do bazy danych SQL na zawsze za wewnętrznego obciążenia Balancer (ILB), a ponad 660 bajtów.
  - Wdrożenia rozwiązania Kubernetes się nie powieść, jeśli próba włączenia wielu wzorców.  

  Ten problem występuje, gdy masz komunikacji między wewnętrznym modułem równoważenia obciążenia i maszyny Wirtualnej w tej samej sieci wirtualnej, ale w różnych podsieciach. Ten problem można obejść, uruchamiając następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień na hoście ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Tworzenie 1.1901.0.95

Zobacz [kompilacji ważne informacje w informacjach o wersji usługi Azure Stack](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Zmiany

Ta kompilacja obejmuje następujące ulepszenia dla usługi Azure Stack:

- Składniki protokołu BGP i translatora adresów Sieciowych są teraz wdrażane na hoście fizycznym. Eliminuje konieczność dwa publiczne lub firmowe adresy IP do wdrażania ASDK, a także upraszcza wdrażanie.
- Systemy kopii zapasowych można teraz zintegrowane usługi Azure Stack [można sprawdzić poprawności](asdk-validate-backup.md) przy użyciu **asdk installer.ps1** skrypt programu PowerShell.

### <a name="new-features"></a>Nowe funkcje

- Aby uzyskać listę nowych funkcji w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1901.md#new-features) informacje o wersji usługi Azure Stack.

### <a name="fixed-and-known-issues"></a>Naprawiono i znane problemy

- Aby uzyskać listę problemów rozwiązanych w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1901.md#fixed-issues) informacje o wersji usługi Azure Stack. Aby uzyskać listę znanych problemów, zobacz [w tej sekcji](../azure-stack-update-1901.md#known-issues-post-installation).
- Należy pamiętać, że [dostępnych poprawek usługi Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) nie mają zastosowania do ASDK stosu platformy Azure.

## <a name="build-118110101"></a>Build 1.1811.0.101

### <a name="changes"></a>Zmiany

Ta kompilacja obejmuje następujące ulepszenia dla usługi Azure Stack:  

- Istnieje zestaw nowych minimalne i zalecane wymagania sprzętowe i programowe dla ASDK. Te nowe zalecane specyfikacje są udokumentowane w artykule [zagadnienia dotyczące wdrażania usługi Azure Stack](asdk-deploy-considerations.md). Jak platforma Azure Stack został przekształcony, więcej usług są teraz dostępne i inne zasoby mogą być wymagane. Zwiększona specyfikacje odzwierciedlać te poprawione zalecenia.

### <a name="new-features"></a>Nowe funkcje

Aby uzyskać listę nowych funkcji w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1811.md#new-features) informacje o wersji usługi Azure Stack.

### <a name="fixed-and-known-issues"></a>Naprawiono i znane problemy

Aby uzyskać listę problemów rozwiązanych w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1811.md#fixed-issues) informacje o wersji usługi Azure Stack. Aby uzyskać listę znanych problemów, zobacz [w tej sekcji](../azure-stack-update-1811.md#known-issues-post-installation).
