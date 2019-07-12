---
title: Włączanie łączenia w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć łączenia w przez wartownika platformy Azure.
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
ms.openlocfilehash: 1e17afb2f06265975442c127baecc8cab75d63f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611255"
---
# <a name="enable-fusion"></a>Włączanie łączenia

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Uczenie maszynowe w platformie Azure przez wartownika jest wbudowana bezpośrednio od samego początku. Zaprojektowaliśmy odnoszącej system innowacje uczenia Maszynowego, mające na celu zapewnienie analityków zabezpieczeń, zabezpieczenia danych naukowcom i inżynierom produktywności. Jednego takiego innowacji jest — łączenie z usługami platformy Azure przez wartownika wbudowane zwłaszcza w celu zmniejszenia zmęczenie alertu.

Fusion używa algorytmów uczenia maszynowego wykres obsługiwane korelacji między milionów niższe wierności nietypowych działań z różnych produktów, takich jak Azure AD Identity Protection i Microsoft Cloud App Security, aby połączyć je w zarządzaniu szereg interesujące przypadków zabezpieczeń.

## <a name="enable-fusion"></a>Włączanie łączenia

1. W witrynie Azure portal wybierz ikonę aby otworzyć usługę Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  W **Witamy w usłudze Cloud Shell** systemu windows, które otwiera poniżej, wybierz opcję PowerShell.

3.  Wybierz subskrypcję, w którym została wdrożona, przez wartownika platformy Azure, i **Utwórz magazyn**.

4. Po uwierzytelnieniu i dysku platformy Azure został utworzony, w wierszu polecenia Uruchom następujące polecenia:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Wyłącz łączenia

Wykonaj tę samą procedurę jak powyżej, a następnie uruchom następujące polecenie:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Wyświetl stan łączenia

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób włączenia łączenia w przez wartownika platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

