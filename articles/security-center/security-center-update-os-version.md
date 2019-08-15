---
title: Aktualizacja wersji systemu operacyjnego w Azure Security Center | Microsoft Docs
description: W tym artykule opisano sposób implementacji **wersji systemu operacyjnego aktualizacji**zalecenia Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905858"
---
# <a name="update-os-version-in-azure-security-center"></a>Zaktualizuj wersję systemu operacyjnego w Azure Security Center
W przypadku maszyn wirtualnych w usługach Cloud Services Azure Security Center zaleca się zaktualizowanie systemu operacyjnego, jeśli jest dostępna nowsza wersja.  Monitorowane są tylko role sieć Web i proces roboczy usług Cloud Services działające w gniazdach produkcyjnych.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
> 
> 

## <a name="implement-the-recommendation"></a>Implementowanie zalecenia
1. W bloku **zalecenia** wybierz pozycję **Aktualizuj wersję systemu operacyjnego**.
   ![Aktualizacja wersji systemu operacyjnego][1]
2. Spowoduje to otwarcie bloku **wersja systemu operacyjnego**. Postępuj zgodnie z instrukcjami w tym bloku, aby zaktualizować wersję systemu operacyjnego.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano, jak wdrożyć wersję systemu operacyjnego aktualizacji "Security Center". Aby dowiedzieć się więcej na temat usług w chmurze i zaktualizować wersję systemu operacyjnego dla usługi w chmurze, zobacz:

* [Przegląd Cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Jak zaktualizować usługę w chmurze](../cloud-services/cloud-services-update-azure-service.md)
* [Jak skonfigurować Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
