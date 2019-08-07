---
title: Włącz funkcję Fusion w wersji zapoznawczej platformy Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak włączyć funkcję Fusion na platformie Azure — wskaźnik.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780441"
---
# <a name="enable-fusion"></a>Włączanie łączenia

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning na platformie Azure — wskaźnik jest wbudowany bezpośrednio od początku. Thoughtfully zaprojektowano system z innowacjemi w ML, których celem jest umożliwienie analitykom bezpieczeństwa, specjalistom ds. ochrony danych i inżynierów. Takie innowacje odbywają się szczególnie w przypadku ograniczenia zmęczenia alertów na platformie Azure.

Funkcja Fusion używa opartych na grafie algorytmów uczenia maszynowego w celu skorelowania między milionami mniejszych nietypowych działań z różnych produktów, takich jak Azure AD Identity Protection, i Microsoft Cloud App Security, aby połączyć je z możliwością zarządzania interesujące zdarzenia związane z bezpieczeństwem.

## <a name="enable-fusion"></a>Włączanie łączenia

1. W Azure Portal wybierz ikonę, aby otworzyć Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  W oknie **Zapraszamy do Cloud Shell** systemu Windows, który zostanie otwarty poniżej, wybierz pozycję PowerShell.

3.  Wybierz subskrypcję, w ramach której wdrożono platformę Azure, i **Utwórz magazyn**.

4. Po uwierzytelnieniu i utworzeniu dysku platformy Azure w wierszu polecenia Uruchom następujące polecenia:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Wyłącz fuzję

Wykonaj tę samą procedurę, jak powyżej, i uruchom następujące polecenie:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Wyświetlanie stanu fuzji

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób włączania fuzji na platformie Azure — wskaźnik. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

