---
title: Plan gotowości Azure Security Center | Microsoft Docs
description: Ten dokument zawiera Przewodnik po gotowości do rozdzielenia na Azure Security Center.
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
ms.openlocfilehash: b2b4dc1aa9486e432fb2fae48343c21fba620221
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996698"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plan gotowości Azure Security Center
Ten dokument zawiera Przewodnik po gotowości, który pomoże Ci rozpocząć pracę z Azure Security Center.

## <a name="understanding-security-center"></a>Informacje o Security Center
Azure Security Center zapewnia ujednolicone Zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami dla obciążeń uruchomionych na platformie Azure, lokalnie i w innych chmurach. 

Aby rozpocząć pracę z usługą Security Center, Skorzystaj z następujących zasobów.

Artykułu
* [Wprowadzenie do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Przewodnik Szybki Start Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

Wideo
* [Film przedstawiający szybkie wprowadzenie](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Przegląd możliwości zapobiegania Security Center, wykrywania i reagowania](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planowanie i operacje
Aby w pełni korzystać z Security Center, ważne jest, aby zrozumieć, w jaki sposób różne osoby lub zespoły w organizacji używają usługi do zaspokajania potrzeb związanych z bezpieczeństwem, monitorowaniem, zarządzaniem i reagowaniem na zdarzenia.

Użyj następujących zasobów, aby pomóc w procesie planowania i operacji.


Art
* [Przewodnik planowania i obsługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Plików
* [Ochrona obciążenia chmury hybrydowej za pomocą Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Dołączanie komputerów do Security Center
Security Center automatycznie wykrywa wszelkie subskrypcje platformy Azure lub obszary robocze, które nie są włączone dla Security Center Standard. Obejmuje to subskrypcje platformy Azure korzystające z Security Center bezpłatnych i obszarów roboczych, dla których nie włączono rozwiązania zabezpieczeń.

Użyj następujących zasobów, aby pomóc podczas procesu dołączania.

Art
* [Dołączanie do Azure Security Center Standard w celu zapewnienia zwiększonych zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Plików
* [Azure Security Center hybrydowe — Omówienie](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Eliminowanie problemów z zabezpieczeniami przy użyciu Security Center
Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci i połączonych rozwiązań partnerskich, takich jak zapora i rozwiązania do ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i zmniejszać liczbę fałszywych pozytywnych.

Skorzystaj z następujących zasobów, aby pomóc w zarządzaniu alertami zabezpieczeń i ochronie zasobów.

Artykułu    
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Ochrona maszyn i aplikacji w Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona sieci w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Ochrona usługi Azure SQL i danych w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Plików   
* [Eliminowanie problemów z zabezpieczeniami przy użyciu Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center odpowiedzi na zdarzenia
Aby zmniejszyć koszty i szkody, ważne jest, aby przed atakiem uzyskać plan reagowania na zdarzenia. Azure Security Center można użyć na różnych etapach reakcji na zdarzenie.

Skorzystaj z następujących zasobów, aby zrozumieć, jak Security Center można włączyć do procesu reagowania na zdarzenia.

Wideo  
* [Azure Security Center w odpowiedzi na zdarzenie](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Szybkie reagowanie na zagrożenia przy użyciu operacji zabezpieczeń nowej generacji oraz badanie](https://youtu.be/e8iFCz5RM4g)

Artykułu    
* [Używanie Azure Security Center do odpowiedzi na zdarzenie](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatyzacja odpowiedzi przy użyciu element PlayBook zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Zaawansowana ochrona w chmurze

Maszyny wirtualne platformy Azure mogą korzystać z zaawansowanych możliwości obrony w chmurze w Security Center. Te możliwości obejmują dostęp do maszyn wirtualnych just-in-Time i adaptacyjnych kontroli aplikacji.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak korzystać z tych funkcji w Security Center.

Wideo  
* [Azure Security Center — dostęp just in Time do maszyny wirtualnej](https://youtu.be/UOQb2FcdQnU)
* [Kontrolki aplikacji Azure Security Center](https://youtu.be/wWWekI1Y9ck)

Artykułu    
* [Zarządzanie dostępem do maszyny wirtualnej przy użyciu funkcji just in Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Adaptacyjne kontrolki aplikacji w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Działania praktyczne

* [Security Center laboratorium praktyczne](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Zalecenie dotyczące zapory aplikacji sieci Web (WAF) element PlayBook w Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center element PlayBook: alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Strona dokumentacji Security Center](https://docs.microsoft.com/azure/security-center/)
* [Strona dokumentacji interfejsu API REST Security Center](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Security Center często zadawane pytania](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Strona cennika Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Najlepsze rozwiązania z zakresu zabezpieczeń tożsamości](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [Najlepsze rozwiązania z zakresu zabezpieczeń sieci](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [Zalecenia dotyczące PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Przepisów](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [Klienci usługi log Analytics mogą teraz używać Azure Security Center do ochrony obciążeń chmury hybrydowej](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>Zasoby społeczności

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Security Center forum społeczności](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



