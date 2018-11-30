---
title: Ochrona usług App Service w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia rozpoczęcie pracy, chroni Twoje usługi App Services w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2018
ms.author: rkarlin
ms.openlocfilehash: f46837751901015fd80d3ecc7a620878cadb3f12
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317878"
---
# <a name="protect-app-service-with-azure-security-center"></a>Ochrona usługi App Service z usługą Azure Security Center
Ten artykuł ułatwia monitorowanie oraz chronić Twoich aplikacji uruchomionych na podstawie usługi App Service przy użyciu usługi Azure Security Center.

Usługa App Service umożliwia tworzenie i hostowanie wybrane aplikacje sieci web w języku programowania bez zarządzania infrastrukturą. Usługa App Service oferuje automatyczne skalowanie i wysoka dostępność, obsługuje zarówno Windows i Linux, a także używanie wdrożeń zautomatyzowanych z usługi GitHub, Visual Studio Team Services lub dowolnego repozytorium Git. 

Luki w zabezpieczeniach w aplikacji sieci web są często wykorzystane przez osoby atakujące, ponieważ mają one typowe i dynamicznego interfejsu dla organizacji, prawie każdy w Internecie. Żądania do aplikacji działających na podstawie usługi App Service przechodzi przez kilka bram wdrożony w centrach danych platformy Azure na całym świecie, odpowiedzialny za routing każdego żądania do jego odpowiadającej jej aplikacji. 

Usługa Azure Security Center można uruchomić oceny i zalecenia na Twoich aplikacji uruchomionych w usłudze App Service w piaskownicach maszyny Wirtualnej lub wystąpienia na żądanie. Dzięki wykorzystaniu widoczność, takiej jak dostawca usług w chmurze platformy Azure, usługa Security Center analizuje dzienniki wewnętrzny usługi App Service na potrzeby typowych ataków aplikacji sieci web, które często jest uruchamiany w wielu lokalizacjach docelowych monitorowania.

Usługa Security Center wykorzystuje skalowalność chmury do identyfikowania ataków na swoich aplikacjach usługi App Service i skupić się na ataki pojawiających się osoby atakujące w fazie rekonesansu skanowanie w celu zidentyfikowania luk w zabezpieczeniach na wielu witryn sieci Web hostowanych na platformie Azure. Centrum zabezpieczeń używa analizy i modeli uczenia maszynowego na pokrycie wszystkich interfejsów, co umożliwia klientom interakcję z ich aplikacji, czy za pośrednictwem protokołu HTTP lub przy użyciu metod zarządzania. Ponadto usługa firmy Microsoft na platformie Azure usługa Security Center jest również w stanie unikatowy oferują zabezpieczenia oparte na hoście analizy obejmujący podstawowych węzłów obliczeniowych na tym PaaS, włączenie Security Center umożliwia wykrywanie ataków na aplikacje sieci web, które były już wykorzystana.

## <a name="prerequisites"></a>Wymagania wstępne

Aby monitorować i zabezpieczanie usługi App Service, musisz mieć plan usługi App Service, który jest skojarzony z dedykowanych maszynach. Te plany są: podstawowa, standardowa, Premium, izolowany lub Linux. Usługa Azure Security Center nie obsługuje plany bezpłatna, współdzielona ani użycie. Aby uzyskać więcej informacji, zobacz [planów usługi App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Ochrona usługi Security Center

Usługa Azure Security Center chroni wystąpienia maszyny Wirtualnej, w którym jest uruchomiony usługi App Service i interfejs zarządzania. Monitoruje również żądań i odpowiedzi wysyłane do i z aplikacji uruchomionych w usłudze App Service.

Usługa Security Center natywnej integracji z usługą App Service, eliminując potrzebę wdrażania i dołączania — Integracja jest całkowicie przezroczysty.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Włączanie monitorowania i ochrony usługi App Service

1. Na platformie Azure wybierz opcję Usługa Security Center.
2. Przejdź do **zasady zabezpieczeń** i wybierz subskrypcję.
3. Na końcu wiersza subskrypcji, kliknij przycisk **edytować ustawienia**.
4. W obszarze **warstwa cenowa**w **usługi App service** wiersz, Przełącz plan **włączone**.

![Przełącz usługi aplikacji](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Liczba wystąpień dla ilość zasobów reprezentuje liczbę wystąpień odpowiednie aktywnej w momencie, gdy zostanie otwarty blok warstwy cenowej usługi App service. Ponieważ ta liczba może się zmieniać w zależności od wybranych opcji skalowania liczby wystąpień są naliczane za zostaną odpowiednio modyfikowane.

Aby wyłączyć monitorowanie i zalecenia dotyczące usługi App Service, powtórz ten proces i Przełącz swoje **usługi App Service** planujesz **wyłączone**.



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
