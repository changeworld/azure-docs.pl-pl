---
title: Ochrona usług App Service w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia rozpoczęcie pracy, chroni Twoje usługi App Services w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: v-mohabe
ms.openlocfilehash: 6a6b5b6e247bd7c105286b86257a6ae11c1d1c60
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551860"
---
# <a name="protect-app-service-with-azure-security-center"></a>Ochrona usługi App Service z usługą Azure Security Center
Ten artykuł ułatwia monitorowanie oraz chronić Twoich aplikacji uruchomionych na podstawie usługi App Service przy użyciu usługi Azure Security Center.

Usługa App Service umożliwia tworzenie i hostowanie wybrane aplikacje sieci web w języku programowania bez zarządzania infrastrukturą. Usługa App Service oferuje automatyczne skalowanie i wysoka dostępność, obsługuje zarówno Windows i Linux, a także używanie wdrożeń zautomatyzowanych z usługi GitHub, DevOps platformy Azure lub dowolnego repozytorium Git. 

Luki w zabezpieczeniach w aplikacji sieci web są często wykorzystane przez osoby atakujące, ponieważ mają one typowe i dynamicznego interfejsu dla organizacji, prawie każdy w Internecie. Żądania do aplikacji działających na podstawie usługi App Service przechodzi przez kilka bram wdrożony w centrach danych platformy Azure na całym świecie, odpowiedzialny za routing każdego żądania do jego odpowiadającej jej aplikacji. 

Usługa Azure Security Center można uruchomić oceny i zalecenia na Twoich aplikacji uruchomionych w usłudze App Service w piaskownicach maszyny Wirtualnej lub wystąpienia na żądanie. Dzięki wykorzystaniu widoczność, takiej jak dostawca usług w chmurze platformy Azure, usługa Security Center analizuje dzienniki wewnętrzny usługi App Service na potrzeby typowych ataków aplikacji sieci web, które często jest uruchamiany w wielu lokalizacjach docelowych monitorowania.

Usługa Security Center wykorzystuje skalowalność chmury do identyfikowania ataków na swoich aplikacjach usługi App Service i skupić się na ataki pojawiających się osoby atakujące w fazie rekonesansu skanowanie w celu zidentyfikowania luk w zabezpieczeniach na wielu witryn sieci Web hostowanych na platformie Azure. Centrum zabezpieczeń używa analizy i modeli uczenia maszynowego na pokrycie wszystkich interfejsów, co umożliwia klientom interakcję z ich aplikacji, czy za pośrednictwem protokołu HTTP lub przy użyciu metod zarządzania. Ponadto usługa firmy Microsoft na platformie Azure usługa Security Center jest również w stanie unikatowy oferują zabezpieczenia oparte na hoście analizy obejmujący podstawowych węzłów obliczeniowych na tym PaaS, włączenie Security Center umożliwia wykrywanie ataków na aplikacje sieci web, które były już wykorzystana.

## <a name="prerequisites"></a>Wymagania wstępne

Aby monitorować i zabezpieczyć usługę App Service, musisz mieć plan usługi App Service, który jest skojarzony z dedykowanymi maszynami. Te plany są następujące: Podstawowy, Standardowy, Premium, Izolowany lub Linux. Usługa Azure Security Center nie obsługuje planów Bezpłatny, Współdzielony ani Zużycie. Aby uzyskać więcej informacji, zobacz [planów usługi App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Ochrona usługi Security Center

Usługa Azure Security Center chroni wystąpienia maszyny Wirtualnej, w którym jest uruchomiony usługi App Service i interfejs zarządzania. Monitoruje również żądań i odpowiedzi wysyłane do i z aplikacji uruchomionych w usłudze App Service.

Usługa Security Center natywnej integracji z usługą App Service, eliminując potrzebę wdrażania i dołączania — Integracja jest całkowicie przezroczysty.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Włączanie monitorowania i ochrony usługi App Service

1. Na platformie Azure wybierz opcję Usługa Security Center.
2. Przejdź do **ceny u & stawienia** i wybierz subskrypcję.
3. W obszarze **warstwa cenowa**w **usługi App service** wiersz, Przełącz plan **włączone**.

![Przełącz usługi aplikacji](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Liczba wystąpień dla ilość zasobów reprezentuje liczbę wystąpień odpowiednie aktywnej w momencie, gdy zostanie otwarty blok warstwy cenowej usługi App service. Ponieważ ta liczba może się zmieniać w zależności od wybranych opcji skalowania liczby wystąpień są naliczane za zostaną odpowiednio modyfikowane.

Aby wyłączyć monitorowanie i zalecenia dotyczące usługi App Service, powtórz ten proces i Przełącz swoje **usługi App Service** planujesz **wyłączone**.



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md): Informacje o sposobie konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Usługi aplikacji](security-center-virtual-machine-protection.md#app-services):  Wyświetl listę usługi App service Environment z podsumowania kondycji.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
