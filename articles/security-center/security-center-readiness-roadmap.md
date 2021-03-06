---
title: Mapa gotowości usługi Azure Security Center | Microsoft Docs
description: Ten dokument zawiera mapę gotowości umożliwiającą zwiększenie możliwości usługi Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 9d74ea2b967112a794cda204cbbfcac707e1d7c4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879466"
---
# <a name="azure-security-center-readiness-roadmap"></a>Mapa gotowości usługi Azure Security Center
Ten dokument zapewnia mapę gotowości, która będzie pomocna w rozpoczynaniu pracy z usługą Azure Security Center.

## <a name="understanding-security-center"></a>Opis usługi Security Center
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. 

Użyj następujących zasobów, aby rozpocząć pracę z usługą Security Center.

Artykuły
* [Wprowadzenie do usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Przewodnik Szybki start dotyczący usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Filmy wideo
* [Wideo z szybkim wprowadzeniem](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Omówienie możliwości związanych z zapobieganiem, wykrywaniem i reagowaniem usługi Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planowanie i operacje

Aby w pełni wykorzystać zalety usługi Security Center, trzeba zrozumieć, w jak różny sposób będą używać usługi poszczególne osoby i zespoły w organizacji, gdyż umożliwi to spełnienie wymagań związanych z bezpieczeństwem obsługi, monitorowaniem, zarządzaniem i reagowaniem na zdarzenia.

Użyj następujących zasobów, aby uzyskać pomoc w procesach planowania i obsługi.

Artykuł
* [Przewodnik planowania i obsługi usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)


### <a name="onboarding-computers-to-security-center"></a>Dołączanie komputerów do usługi Security Center
Usługa Security Center automatycznie wykrywa wszelkie subskrypcje i obszary robocze platformy Azure nie włączone do standardu usługi Security Center. Obejmuje to subskrypcje platformy Azure korzystające z usługi Security Center w warstwie bezpłatnej oraz obszary robocze, które nie mają włączonego rozwiązania w zakresie zabezpieczeń.

Użyj następujących zasobów, aby uzyskać pomoc w procesach dołączania.

Artykuł
* [Dołączanie do standardu usługi Azure Security Center w celu zapewnienia większego bezpieczeństwa](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Film wideo
* [Hybrydowa usługa Azure Security Center — omówienie](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Zmniejszenie zagrożeń dla bezpieczeństwa przy użyciu usługi Security Center
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów.

Użyj następujących zasobów, aby uzyskać pomoc w zarządzaniu alertami zabezpieczeń i zabezpieczaniu swoich zasobów.

Artykuły    
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona sieci w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Ochrona usługi Azure SQL i danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Film wideo    
* [Zmniejszenie zagrożeń dla bezpieczeństwa przy użyciu usługi Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Korzystanie z usługi Security Center w celu reagowania na zdarzenia
Aby zmniejszyć koszty i uszkodzenia, przed atakiem należy wprowadzić plan reagowania na incydenty. Usługi Azure Security Center można używać na różnych etapach reagowania na zdarzenia.

Użyj następujących zasobów, aby zrozumieć sposób dołączenia usługi Security Center do procesu reagowania na zdarzenia.

Filmy wideo    
* [Usługa Azure Security Center na potrzeby reagowania na zdarzenia](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Szybkie reagowanie na zagrożenia przy użyciu zabezpieczeń, obsługi i badań nowej generacji](https://youtu.be/e8iFCz5RM4g)

Artykuły    
* [Używanie usługi Azure Security Center do reagowania na zdarzenia](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatyzacja reakcji za pomocą automatyzacji przepływu pracy](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Zaawansowana ochrona w chmurze

Maszyny wirtualne platformy Azure mogą wykorzystywać zaawansowane możliwości ochrony w chmurze w usłudze Security Center. Te możliwości obejmują dostęp do maszyny wirtualnej just-in-time (VM) i kontrolki aplikacji adaptacyjnych.

Użyj następujących zasobów, aby dowiedzieć się, jak korzystać z tych możliwości w usłudze Security Center.

Filmy wideo    
* [Usługa Azure Security Center — dostęp do maszyny wirtualnej just-in-time](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center — funkcje adaptacyjnego sterowania aplikacjami](https://youtu.be/wWWekI1Y9ck)

Artykuły    
* [Zarządzanie dostępem do maszyn wirtualnych przy użyciu dostępu just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Praktyczne działania

* [Praktyczne laboratorium usługi Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Podręcznik dotyczący zaleceń zapory aplikacji internetowej (WAF) w usłudze Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Podręcznik dotyczący usługi Azure Security Center: alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Strona dokumentacji usługi Security Center](https://docs.microsoft.com/azure/security-center/)
* [Strona dokumentacji interfejsu API REST usługi Security Center](https://msdn.microsoft.com/library/mt704034.aspx)
* [Często zadawane pytania dotyczące usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Strona cennika usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Najlepsze rozwiązania z zakresu zabezpieczeń tożsamości](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Najlepsze rozwiązania z zakresu zabezpieczeń sieci](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Zalecenia PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Zgodność](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Klienci korzystający z analizy dzienników mogą teraz używać usługi Azure Security Center do ochrony swoich obciążeń w chmurze hybrydowej](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Zasoby społeczności

* [Usługa Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Forum społeczności usługi Security Center](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



