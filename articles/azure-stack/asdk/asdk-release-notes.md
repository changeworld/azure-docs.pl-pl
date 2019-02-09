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
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 35a3850cdda7f6844258439b74b29dcc20852a89
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959599"
---
# <a name="asdk-release-notes"></a>Informacje o wersji ASDK

Ten artykuł zawiera informacje o zmianach, poprawki i znane problemy w usłudze Azure Stack Development Kit (ASDK). Jeśli nie masz pewności, której wersji używasz, możesz to zrobić [korzystanie z portalu, aby sprawdzić](../azure-stack-updates.md#determine-the-current-version).

Na bieżąco what's new in ASDK subskrybując [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanału informacyjnego](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Tworzenie 1.1901.0.95

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

## <a name="build-11809090"></a>Tworzenie 1.1809.0.90

### <a name="new-features"></a>Nowe funkcje

Aby uzyskać listę nowych funkcji w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1809.md#new-features) informacje o wersji usługi Azure Stack.

### <a name="fixed-issues"></a>Rozwiązane problemy

Aby uzyskać listę problemów rozwiązanych w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Znane problemy

Aby uzyskać listę znanych problemów występujących w tej wersji, zobacz [w tej sekcji](../azure-stack-update-1809.md#known-issues-post-installation).
