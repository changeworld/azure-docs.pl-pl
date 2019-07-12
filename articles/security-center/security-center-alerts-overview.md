---
title: Alerty zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, czym są alerty zabezpieczeń i różnych typów w usłudze Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571675"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w usłudze Azure Security Center

W tym artykule przedstawiono różne typy alertów zabezpieczeń dostępnych w usłudze Azure Security Center (ASC). Istnieją różne alerty dotyczące wielu różnych typów zasobów. ASC generuje alerty dla obu zasobów wdrożonych na platformie Azure i zasoby wdrażane na lokalne i hybrydowe środowiska w chmurze. 

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?

Alerty to powiadomienia, generowane przez Centrum zabezpieczeń po wykryciu zagrożenia na zasoby. Jego priorytet i wyświetla alerty wraz z informacjami, służące do szybkiego analizowania problemu. Usługa Security Center zawiera także zalecenia dotyczące można sposobu wyeliminowania skutków ataku.

## <a name="how-does-security-center-detect-threats"></a>W jaki sposób usługa Security Center wykrywa zagrożenia

Aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów, usługa Security Center zbiera, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak zapory i rozwiązań ochrony punktów końcowych. Usługa Security Center analizuje te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia.

ASC monitoruje zasobów, czy wdrożone na platformie Azure wdrożone w innych w środowisku lokalnym i hybrydowych środowiskach chmury. Aby uzyskać więcej informacji na temat wykrywania zagrożeń i reagowanie na, zobacz [jak usługa Security Center wykrywa zagrożenia i reaguje na](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Typy alertów zabezpieczeń

W poniższych tematach przedstawiono za pośrednictwem różnych alertów ASC według typów zasobów:

* [Maszyny wirtualne IaaS i serwery alertów](security-center-alerts-iaas.md)
* [Alerty natywne możliwości obliczeniowe](security-center-alerts-compute.md)
* [Data services — alerty](security-center-alerts-data-services.md)

W poniższych tematach opisano, jak usługa Security Center korzysta z różnych danych telemetrycznych, które są zbierane z integracji z usługą infrastruktury platformy Azure w celu zastosowania warstwy dodatkową ochronę dla zasobów wdrożonych na platformie Azure:

* [Alerty warstwy usług](security-center-alerts-service-layer.md)
* [Integracja z produktami zabezpieczeń platformy Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Co to są zdarzenia alertów?

Zdarzenie naruszenia zabezpieczeń to kolekcja powiązanych alertów, zamiast wymieniać każdy alert indywidualnie. Centrum zabezpieczeń używa fusion służące do skorelowania sygnałów o niskiej wierności i różnych alertów w zdarzenia związane z bezpieczeństwem.

Za pomocą zdarzeń, usługa Security Center umożliwia pojedynczego widoku kampanii ataku i wszystkich powiązanych alertów. W tym widoku można szybko zrozumieć, jakie akcje podjęte przez osobę atakującą i jakie zasoby miały wpływ. Aby uzyskać więcej informacji, zobacz [inteligentne korelacji Alert Cloud](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Rozpoczynanie pracy z alertami

W poniższych tematach, aby dowiedzieć się więcej o zasobach monitorowane przez usługę ASC, a także wskazówki dotyczące sposobu reagowania na alerty przedstawiony przez usługę ASC.

* Aby zobaczyć, które platformy i funkcje, które są chronione przez usługę ASC, zobacz [platform i funkcji obsługiwanych przez usługę Azure Security Center](security-center-os-coverage.md).  
* Aby dowiedzieć się, czym są związane z bezpieczeństwem i jak ASC reaguje na nich, zobacz [sposób obsługi zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md). 
* Aby dowiedzieć się, jak zarządzać alertami, pojawi się, zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md).
* Aby uzyskać informacje na temat sprawdzania poprawności Centrum zabezpieczeń jest poprawnie skonfigurowany i aby stymulować alert testowy, zobacz [Walidacja alertów w usłudze Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Uaktualnij do warstwy standardowa wykrywanie zaawansowane

Aby skonfigurować wykrywanie zaawansowane, przeprowadź uaktualnienie usługi Azure Security Center do wersji Standard. 

1. Wybierz z menu usługi Security Center, **zasady zabezpieczeń**.
2. W przypadku subskrypcji, czy chcesz przenieść się do warstwy standardowa, kliknij przycisk **edytować ustawienia**. 
3. Na stronie ustawień wybierz **warstwy cenowej**. 
   Bezpłatna wersja próbna jest dostępna w miesiącu. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono, jakie są alerty zabezpieczeń i różne typy alertów, które są dostępne w usłudze Security Center. Więcej informacji znajduje się w następujących tematach:

* [Przewodnik planowania i obsługi usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Usługa Azure Security Center — często zadawane pytania](https://docs.microsoft.com/azure/security-center/security-center-faq): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

