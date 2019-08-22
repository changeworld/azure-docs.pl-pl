---
title: Alerty zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym temacie wyjaśniono, co to są alerty zabezpieczeń i różne typy dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647322"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w Azure Security Center

W tym artykule przedstawiono różne typy alertów zabezpieczeń dostępnych w Azure Security Center (ASC). Istnieje wiele różnych alertów dla wielu różnych typów zasobów. ASC generuje alerty dla obu zasobów wdrożonych na platformie Azure oraz zasobów wdrożonych w środowiskach lokalnych i hybrydowych w chmurze. 

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?

Alerty są powiadomieniami generowanymi przez Security Center w przypadku wykrycia zagrożeń dotyczących zasobów. Określa ona priorytet i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Security Center również zawiera zalecenia dotyczące sposobu korygowania ataku.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center wykrywać zagrożenia?

Aby wykrywać rzeczywiste zagrożenia i zmniejszyć liczbę fałszywych wyników, Security Center gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak zapora i rozwiązania do ochrony punktów końcowych. Security Center analizuje te informacje, często skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

ASC monitoruje zasoby, niezależnie od tego, czy zostały wdrożone na platformie Azure, czy wdrożone w środowiskach chmur lokalnych i hybrydowych. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i reagowania na nie, zobacz [jak Security Center wykrywać zagrożenia i reagować](security-center-detection-capabilities.md#asc-detects)na nie.

## <a name="security-alert-types"></a>Typy alertów zabezpieczeń

Poniższe tematy przeprowadzą Cię przez różne alerty ASC według typów zasobów:

* [IaaS maszyny wirtualne & alerty serwerów](security-center-alerts-iaas.md)
* [Natywne alerty obliczeniowe](security-center-alerts-compute.md)
* [Alerty usług danych](security-center-alerts-data-services.md)

W poniższych tematach opisano, jak Security Center wykorzystuje różne dane telemetryczne zbierane z integracji z infrastrukturą platformy Azure w celu zastosowania dodatkowych warstw ochrony dla zasobów wdrożonych na platformie Azure:

* [Alerty warstwy usług](security-center-alerts-service-layer.md)
* [Integracja z produktami zabezpieczeń platformy Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Co to są zdarzenia związane z alertami?

Zdarzenie zabezpieczeń to kolekcja powiązanych alertów, a nie każdego pojedynczego alertu. Security Center używa [korelacji alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md) w celu skorelowania różnych alertów i niskiej dokładności sygnałów do zdarzeń zabezpieczeń.

Korzystając z incydentów, Security Center zapewnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie działań podejmowanych przez osobę atakującą oraz zasobów, których dotyczy problem. Aby uzyskać więcej informacji, zobacz [korelacja alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Wprowadzenie do alertów

Zapoznaj się z następującymi tematami, aby dowiedzieć się więcej o zasobach monitorowanych przez zasady ASC oraz o sposobach reagowania na alerty prezentowane w ramach usługi ASC.

* Aby zobaczyć, które platformy i funkcje są chronione przez funkcję ASC, zobacz [platformy i funkcje obsługiwane przez Azure Security Center](security-center-os-coverage.md).  
* Aby dowiedzieć się, co to są zdarzenia związane z zabezpieczeniami i jak jest w nim odpowiedzi ASC, zobacz [jak obsłużyć zdarzenia zabezpieczeń w Azure Security Center](security-center-incident.md). 
* Aby dowiedzieć się, jak zarządzać alertami otrzymywanymi, zobacz [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md).
* Aby uzyskać informacje na temat sprawdzania, czy Security Center jest prawidłowo skonfigurowany i stymulowanie alertu testowego, zobacz [Sprawdzanie poprawności alertów w Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uaktualnij do wersji Standard na potrzeby zaawansowanych wykryć

Aby skonfigurować wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. 

1. Z menu Security Center wybierz pozycję **zasady zabezpieczeń**.
2. W przypadku subskrypcji, które chcesz przenieść do warstwy Standardowa, kliknij pozycję **Edytuj ustawienia**. 
3. Na stronie Ustawienia wybierz pozycję **warstwa cenowa**. 
   Bezpłatna wersja próbna jest dostępna w danym miesiącu. Aby dowiedzieć się więcej, zobacz [stronę](https://azure.microsoft.com/pricing/details/security-center/)z cennikiem. 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o tym, co to są alerty zabezpieczeń i różne typy alertów dostępne w Security Center. Więcej informacji znajduje się w następujących tematach:

* [Przewodnik planowania i obsługi usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center często zadawane pytania](https://docs.microsoft.com/azure/security-center/security-center-faq): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

