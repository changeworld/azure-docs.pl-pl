---
title: Alerty zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym temacie opisano alerty zabezpieczeń i różne typy dostępne w Azure Security Center.
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
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013293"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w Centrum zabezpieczeń Azure

W Azure Security Center istnieje wiele alertów dla wielu różnych typów zasobów. Security Center generuje alerty dotyczące zasobów wdrożonych na platformie Azure, a także dla zasobów wdrożonych w środowiskach lokalnych i hybrydowych w chmurze. 

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?

Alerty są powiadomieniami generowanymi przez Security Center w przypadku wykrycia zagrożeń dotyczących zasobów. Określa priorytet i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Security Center również zawiera zalecenia dotyczące sposobu korygowania ataku.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center wykrywać zagrożenia?

Aby wykrywać rzeczywiste zagrożenia i zmniejszyć liczbę fałszywych wyników, Security Center gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure i sieci. Współpracuje również z rozwiązaniami partnerskimi, takimi jak zapora i rozwiązania programu Endpoint Protection. Security Center analizuje te informacje, często skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

Security Center monitoruje zasoby w dowolnym środowisku, w którym się znajdują. Aby dowiedzieć się więcej na temat wykrywania zagrożeń i reagowania na nie, zobacz [jak Security Center wykrywać zagrożenia i reagować](security-center-detection-capabilities.md#asc-detects)na nie.

## <a name="security-alert-types"></a>Typy alertów zabezpieczeń

Poniższe tematy przeprowadzą Cię przez różne alerty, zgodnie z typami zasobów:

* [Alerty maszyn wirtualnych i serwerów IaaS](security-center-alerts-iaas.md)
* [Natywne alerty obliczeniowe](security-center-alerts-compute.md)
* [Alerty usług danych](security-center-alerts-data-services.md)

W poniższych tematach opisano, jak Security Center używa różnych danych telemetrycznych zbieranych z integracji z infrastrukturą platformy Azure w celu zastosowania dodatkowych warstw ochrony dla zasobów wdrożonych na platformie Azure:

* [Alerty warstwy usług](security-center-alerts-service-layer.md)
* [Integracja z produktami zabezpieczeń platformy Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Co to są zdarzenia związane z bezpieczeństwem?

Zdarzenie zabezpieczeń to kolekcja powiązanych alertów, a nie każdego pojedynczego alertu. Security Center używa [korelacji alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md) w celu skorelowania różnych alertów i niskiej dokładności sygnałów do zdarzeń zabezpieczeń.

Korzystając z incydentów, Security Center zapewnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie działań podejmowanych przez osobę atakującą oraz zasobów, których dotyczy. Aby uzyskać więcej informacji, zobacz [korelacja alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Wprowadzenie do alertów

Poniżej przedstawiono artykuły, które ułatwiają zapoznanie się z informacjami o zasobach monitorowanych przez Security Center i przedstawiono wskazówki dotyczące sposobu reagowania na przedstawione alerty.

* [Platformy i funkcje obsługiwane przez usługę Azure Security Center](security-center-os-coverage.md)  
* [Jak obsłużyć zdarzenia zabezpieczeń w Azure Security Center](security-center-incident.md) 
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Walidacja alertów w usłudze Azure Security Center](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uaktualnij do wersji Standard na potrzeby zaawansowanych wykryć

Aby skonfigurować wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. 

1. Z menu **Security Center** wybierz pozycję **zasady zabezpieczeń**.
2. W przypadku subskrypcji, które chcesz przenieść do warstwy Standardowa, wybierz pozycję **Edytuj ustawienia**. 
3. Na stronie Ustawienia wybierz pozycję **warstwa cenowa**. 
   Bezpłatna wersja próbna jest dostępna w danym miesiącu. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o różnych typach alertów dostępnych w Security Center. Aby uzyskać więcej informacji, zobacz:

* [Przewodnik planowania i obsługi usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center — często zadawane pytania](https://docs.microsoft.com/azure/security-center/security-center-faq)

