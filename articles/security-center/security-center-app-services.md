---
title: Ochrona aplikacji internetowych usługi Azure App Service i interfejsów API
description: Ten artykuł ułatwia rozpoczęcie ochrony aplikacji sieci Web usługi Azure App Service i interfejsów API w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616484"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Ochrona aplikacji internetowych usługi Azure App Service i interfejsów API

Usługa Azure App Service to w pełni zarządzana platforma do tworzenia i hostowania aplikacji sieci web i interfejsów API bez obawy o konieczność zarządzania infrastrukturą. Zapewnia zarządzanie, monitorowanie i informacje operacyjne, aby spełnić wymagania dotyczące wydajności, zabezpieczeń i zgodności klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [Usługa Azure App Service](https://azure.microsoft.com/services/app-service/).

Aby włączyć zaawansowaną ochronę przed zagrożeniami dla planu usługi Azure App Service, należy:

* Subskrybuj standardową warstwę cenową usługi Azure Security Center
* Włącz plan usługi aplikacji, jak pokazano poniżej. Usługa Security Center jest natywnie zintegrowana z usługą App Service, eliminując potrzebę wdrażania i wdrażania — integracja jest przejrzysta.
* Mieć plan usługi app service, który jest skojarzony z dedykowanych maszyn. Obsługiwane plany to: Podstawowe, Standardowe, Premium, Izolowane lub Linux. Usługa Security Center nie obsługuje planów bezpłatnego, udostępnionego ani zużycia. Aby uzyskać więcej informacji, zobacz [Plany usługi app service](https://azure.microsoft.com/pricing/details/app-service/plans/).

Po włączeniu planu usługi app service usługa zabezpieczeń usługa Security Center ocenia zasoby objęte planem usługi App Service i generuje zalecenia dotyczące zabezpieczeń na podstawie jego ustaleń. Usługa Security Center chroni wystąpienie maszyny Wirtualnej, w którym jest uruchomiona usługa App Service, oraz interfejs zarządzania. Monitoruje również żądania i odpowiedzi wysyłane do i z aplikacji uruchomionych w usłudze App Service.

Usługa Security Center wykorzystuje skalę chmury i widoczność, jaką platforma Azure ma jako dostawca chmury, aby monitorować typowe ataki aplikacji sieci Web. Usługa Security Center może wykrywać ataki na aplikacje i identyfikować pojawiające się ataki — nawet gdy osoby atakujące znajdują się w fazie rozpoznania, skanowanie w celu zidentyfikowania luk w zabezpieczeniach wielu aplikacji hostowanych na platformie Azure. Jako usługa natywna dla platformy Azure usługa Security Center jest również w unikatowej pozycji, aby zaoferować analizę zabezpieczeń opartą na hoście obejmującą podstawowe węzły obliczeniowe dla tego paaS, umożliwiając centrum zabezpieczeń wykrywanie ataków na aplikacje sieci web, które zostały już wykorzystane. Aby uzyskać więcej informacji, zobacz [Ochrona przed zagrożeniami dla usługi Azure App Service](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Włączanie monitorowania i ochrony usługi app service

1. W witrynie Azure portal wybierz pozycję Centrum zabezpieczeń.
2. Przejdź do **ustawień & cennika** i wybierz subskrypcję.
3. W **obszarze Warstwa cenowa**w wierszu **usługi aplikacji** przełącz plan na **Włączone**.

    [![Włączanie usług aplikacji w subskrypcji warstwy Standardowa](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Liczba wystąpień wymienionych dla **ilości zasobów** reprezentuje całkowitą liczbę wystąpień obliczeniowych we wszystkich planach usługi App Service w tej subskrypcji, uruchomionych w momencie otwarcia bloku warstwy cenowej.
>
> Usługa Azure App Service oferuje różne plany. Plan usługi aplikacji definiuje zestaw zasobów obliczeniowych dla aplikacji sieci web do uruchomienia. Są one równoważne farmom serwerów w konwencjonalnym hostingu. Co najmniej jedna aplikacja może być skonfigurowana do uruchamiania na tych samych zasobach obliczeniowych (lub w tym samym planie usługi App Service).
>
>Aby sprawdzić poprawność liczby, przejdź do "Planów usługi aplikacji" w witrynie Azure Portal, gdzie można zobaczyć liczbę wystąpień obliczeniowych używanych przez każdy plan. 






Aby wyłączyć monitorowanie i zalecenia dotyczące usługi App Service, powtórz ten proces i przełącz plan **usługi App Service** na **Wyłączone**.



## <a name="see-also"></a>Zobacz też
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej o usłudze Azure Security Center, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Usługi aplikacji:](security-center-virtual-machine-protection.md#app-services)wyświetl listę środowisk usługi aplikacji ze podsumowaniami kondycji.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
