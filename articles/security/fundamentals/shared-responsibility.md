---
title: Wspólna odpowiedzialność w chmurze - Microsoft Azure
description: Poznaj model współużytkowania i zadania zabezpieczeń obsługiwane przez dostawcę chmury i które zadania są obsługiwane przez użytkownika.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518371"
---
# <a name="shared-responsibility-in-the-cloud"></a>Wspólna odpowiedzialność w chmurze

Podczas rozpatrywania i oceny publicznych usług w chmurze ważne jest zrozumienie modelu współdzielonej odpowiedzialności i zadań zabezpieczeń obsługiwanych przez dostawcę chmury i zadań obsługiwanych przez użytkownika. Obowiązki związane z obciążeniem pracą różnią się w zależności od tego, czy obciążenie jest hostowane w programie Software as a Service (SaaS), Platform as a Service (PaaS), Infrastructure as a Service (IaaS) czy w lokalnym centrum danych

## <a name="division-of-responsibility"></a>Podział odpowiedzialności
W lokalnym centrum danych jesteś właścicielem całego stosu. W miarę przechodzenia do chmury niektóre obowiązki są przenoszone do firmy Microsoft. Na poniższym diagramie przedstawiono obszary odpowiedzialności między użytkownikiem a firmą Microsoft, w zależności od typu wdrożenia stosu.

![Strefy odpowiedzialności](./media/shared-responsibility/shared-responsibility.png)

W przypadku wszystkich typów wdrożeń w chmurze posiadasz własne dane i tożsamości. Użytkownik jest odpowiedzialny za ochronę bezpieczeństwa danych i tożsamości, zasobów lokalnych i kontrolował składników chmury (który różni się w zależności od typu usługi).

Niezależnie od typu wdrożenia, następujące obowiązki są zawsze zachowywane przez Ciebie:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

## <a name="cloud-security-advantages"></a>Zalety zabezpieczeń w chmurze
Chmura oferuje znaczące korzyści w rozwiązywaniu długotrwałych wyzwań związanych z bezpieczeństwem informacji. W środowisku lokalnym organizacje prawdopodobnie mają niezaspokojone obowiązki i ograniczone zasoby dostępne do inwestowania w zabezpieczenia, co tworzy środowisko, w którym osoby atakujące mogą wykorzystywać luki we wszystkich warstwach.

Na poniższym diagramie przedstawiono tradycyjne podejście, w którym wiele obowiązków związanych z zabezpieczeniami jest niezaspokojonych z powodu ograniczonych zasobów. W podejściu obsługującym chmurę można przenosić codzienne obowiązki związane z zabezpieczeniami do dostawcy chmury i ponownie przydzielać zasoby.

![Zalety bezpieczeństwa w erze chmury](./media/shared-responsibility/cloud-enabled-security.png)

W podejściu obsługującym chmurę można również wykorzystać funkcje zabezpieczeń oparte na chmurze, aby zwiększyć skuteczność i korzystać z analizy chmury, aby poprawić czas wykrywania zagrożeń i reagowania. Przenosząc obowiązki na dostawcę chmury, organizacje mogą uzyskać większy zakres zabezpieczeń, co umożliwia im przeniesienie zasobów zabezpieczeń i budżetu na inne priorytety biznesowe.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat podziału odpowiedzialności między użytkownikiem a firmą Microsoft w ramach wdrażania systemów SaaS, PaaS i IaaS, zobacz [Współużytkowane obowiązki związane z przetwarzaniem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)w chmurze .
