---
title: Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia używanie Azure Security Center do zarządzania zdarzeniami zabezpieczeń.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 2a22039b3499f1ba0f2d7acb2e86a1991d35a380
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202122"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Zarządzanie zdarzeniami zabezpieczeń w Azure Security Center

Klasyfikacja i badanie alertów zabezpieczeń może być czasochłonne dla nawet najbardziej wykwalifikowanych analityków zabezpieczeń, a dla wielu z nich trudno wiedzieć, gdzie zacząć. Używając [analizy](security-center-detection-capabilities.md) w celu powiązania informacji z różnych [alertów zabezpieczeń](security-center-managing-and-responding-alerts.md), usługa Security Center może dostarczyć pojedynczego widoku kampanii ataku i wszystkich powiązanych alertów — dzięki temu można szybko dowiedzieć się, jakie akcje zostały podjęte przez osobę atakującą i na jakie zasoby miały wpływ.

W tym temacie wyjaśniono informacje o zdarzeniach w Security Center i sposobach używania korygowania alertów.

## <a name="what-is-a-security-incident"></a>Co to jest zdarzenie naruszenia zabezpieczeń?

W usłudze Security Center zdarzenie naruszenia zabezpieczeń to agregacja wszystkich alertów zasobu, które są zgodne ze wzorcami [kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/), tzw. „zabójczego łańcucha”. Zdarzenia są wyświetlane na liście [alerty zabezpieczeń](security-center-managing-and-responding-alerts.md) . Kliknij zdarzenie, aby wyświetlić powiązane alerty, co pozwala uzyskać więcej informacji na temat każdego wystąpienia.

## <a name="managing-security-incidents"></a>Zarządzanie zdarzeniami naruszenia zabezpieczeń

1. Na pulpicie nawigacyjnym Security Center kliknij kafelek **alerty zabezpieczeń** . Zdarzenia i alerty są wymienione na liście. Należy zwrócić uwagę, że ikona opisu zdarzenia naruszenia zabezpieczeń różni się od ikon innych alertów.

    ![Wyświetl zdarzenia dotyczące zabezpieczeń](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Aby wyświetlić szczegóły, kliknij zdarzenie. Blok **wykryte zdarzenia zabezpieczeń** wyświetla dalsze szczegóły. Sekcja **Informacje ogólne** może oferować wgląd w to, co wyzwolił alert zabezpieczeń. Są w nim wyświetlane informacje, takie jak zasób docelowy, źródłowy adres IP (jeśli ma zastosowanie), jeśli alert jest nadal aktywny i zalecenia dotyczące sposobu korygowania.  

    ![Reagowanie na zdarzenia związane z bezpieczeństwem w Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Aby uzyskać więcej informacji na temat każdego alertu, kliknij alert. Czynności naprawcze sugerowane w Centrum zabezpieczeń różnią się w zależności od alertu zabezpieczeń.

   > [!NOTE]
   > Ten sam alert może istnieć jako część zdarzenia, a także być widoczny jako alert autonomiczny.

    ![Szczegóły alertu](./media/security-center-incident/security-center-incident-alert.png)

1. Wykonaj czynności zaradcze podane dla każdego alertu.

Aby uzyskać więcej informacji na temat alertów, [zarządzania alertami zabezpieczeń i reagowania na](security-center-managing-and-responding-alerts.md)nie.

Poniższe tematy przeprowadzą Cię przez różne alerty, zgodnie z typami zasobów:

* [Alerty maszyn wirtualnych i serwerów IaaS](security-center-alerts-iaas.md)
* [Natywne alerty obliczeniowe](security-center-alerts-compute.md)
* [Alerty usług danych](security-center-alerts-data-services.md)

W poniższych tematach opisano, jak Security Center używa różnych danych telemetrycznych zbieranych z integracji z infrastrukturą platformy Azure w celu zastosowania dodatkowych warstw ochrony dla zasobów wdrożonych na platformie Azure:

* [Alerty warstwy usług](security-center-alerts-service-layer.md)
* [Integracja z produktami zabezpieczeń platformy Azure](security-center-alerts-integration.md)

## <a name="see-also"></a>Zobacz także
W tym dokumencie przedstawiono sposób użycia funkcji zdarzeń naruszenia zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Alerty zabezpieczeń w Azure Security Center](security-center-alerts-overview.md).
* [Zarządzanie alertami zabezpieczeń](security-center-managing-and-responding-alerts.md)
* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
