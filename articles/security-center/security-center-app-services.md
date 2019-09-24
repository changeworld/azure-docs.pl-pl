---
title: Ochrona App Services w Azure Security Center | Microsoft Docs
description: Ten artykuł pomaga rozpocząć ochronę App Services w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202467"
---
# <a name="protect-app-service-with-azure-security-center"></a>Ochrona App Service przy użyciu Azure Security Center
Ten artykuł ułatwia używanie Azure Security Center do monitorowania i ochrony aplikacji działających w oparciu o App Service.

App Service umożliwia tworzenie i hostowanie aplikacji sieci Web w wybranym języku programowania bez zarządzania infrastrukturą. App Service oferuje automatyczne skalowanie i wysoką dostępność, obsługują zarówno systemy Windows, jak i Linux, a także zautomatyzowane wdrożenia z usług GitHub, Azure DevOps lub dowolnego repozytorium git. 

Luki w zabezpieczeniach aplikacji sieci Web są często wykorzystywane przez osoby atakujące, ponieważ mają wspólny i dynamiczny interfejs niemal każdej organizacji w Internecie. Żądania do aplikacji uruchamianych na App Service przechodzą przez kilka bram wdrożonych w centrach danych platformy Azure na całym świecie odpowiedzialne za kierowanie poszczególnych żądań do odpowiedniej aplikacji. 

Azure Security Center można uruchamiać oceny i zalecenia dotyczące aplikacji uruchamianych w App Service w piaskownicach na maszynie wirtualnej lub w wystąpieniach na żądanie. Dzięki wykorzystaniu widoczności platformy Azure jako dostawcy chmury Security Center analizuje App Service dzienniki wewnętrzne, aby monitorować typowe ataki aplikacji sieci Web, które często działają w wielu celach.

Security Center wykorzystuje skalę chmury w celu identyfikowania ataków na aplikacje App Service i skoncentrowanie się na pojawiających się atakach, podczas gdy osoby atakujące znajdują się w fazie Rekonesans, co umożliwia wykrywanie luk w zabezpieczeniach w wielu witrynach sieci Web hostowanych na platformie Azure. Security Center korzysta z modeli analizy i uczenia maszynowego, aby uwzględnić wszystkie interfejsy umożliwiające klientom interakcję z aplikacjami, niezależnie od tego, czy za pośrednictwem protokołu HTTP, czy za pośrednictwem metod zarządzania. Ponadto jako usługa pierwszej firmy na platformie Azure Security Center jest również w unikatowym miejscu, aby oferować analizę zabezpieczeń opartą na hoście obejmującą bazowe węzły obliczeniowe dla tego PaaS, umożliwiając Security Center wykrywające ataki dla aplikacji sieci Web, które zostały została już wykorzystana.

## <a name="prerequisites"></a>Wymagania wstępne

Aby monitorować i zabezpieczyć usługę App Service, musisz mieć plan usługi App Service, który jest skojarzony z dedykowanymi maszynami. Te plany są następujące: Podstawowy, Standardowy, Premium, Izolowany lub Linux. Usługa Azure Security Center nie obsługuje planów Bezpłatny, Współdzielony ani Zużycie. Aby uzyskać więcej informacji, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Ochrona Security Center

Azure Security Center chroni wystąpienie maszyny wirtualnej, w której jest uruchomiony App Service i interfejs zarządzania. Monitoruje również żądania i odpowiedzi wysyłane do i z aplikacji uruchamianych w App Service.

Security Center jest natywnie zintegrowana z App Service, eliminując konieczność wdrażania i dołączania — integracja jest całkowicie niewidoczna.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Włączanie monitorowania i ochrony App Service

1. Na platformie Azure wybierz Security Center.
2. Przejdź do pozycji **cennik & Ustawienia** i wybierz subskrypcję.
3. W obszarze **warstwa cenowa**w wierszu **usługi App Service** Przełącz swój plan na **włączony**.

![Przełączanie usługi App Service](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Liczba wystąpień na liście dla danej liczby zasobów reprezentuje liczbę ważnych wystąpień usługi App Service, która jest aktywna w momencie otwarcia bloku warstwy cenowej. Ponieważ ta liczba może ulec zmianie w zależności od wybranych opcji skalowania, liczba wystąpień, dla których nastąpi opłata, zostanie odpowiednio zmodyfikowana.

Aby wyłączyć monitorowanie i zalecenia dotyczące App Service, Powtórz ten proces i Przełącz plan **App Service** na **wyłączony**.



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): Dowiedz się, jak skonfigurować ustawienia zabezpieczeń w Azure Security Center.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [App Services](security-center-virtual-machine-protection.md#app-services):  Wyświetl listę środowisk usługi App Service za pomocą podsumowań kondycji.
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
