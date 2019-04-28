---
title: Wersja aktualizacji systemu operacyjnego w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **aktualizowanie wersji systemu operacyjnego**.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905858"
---
# <a name="update-os-version-in-azure-security-center"></a>Zaktualizuj wersję systemu operacyjnego w usłudze Azure Security Center
W przypadku maszyn wirtualnych (VM) w usługach w chmurze usługi Azure Security Center zaleci, czy systemu operacyjnego (OS) można zaktualizować, jeśli jest dostępna nowsza wersja.  Jedyna chmura usług ról sieć web i procesów roboczych działających w środowisku produkcyjnym, które mają być monitorowane przez miejsc.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Nie jest to przewodnik krok po kroku.
> 
> 

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **zalecenia** bloku wybierz **aktualizowanie wersji systemu operacyjnego**.
   ![Aktualizacja wersji systemu operacyjnego][1]
2. Spowoduje to otwarcie bloku **aktualizowanie wersji systemu operacyjnego**. Wykonaj kroki opisane w tym bloku, aby zaktualizować wersję systemu operacyjnego.

## <a name="see-also"></a>Zobacz także
W tym artykule pokazano sposób implementacji zalecenia usługi Security Center "Wersja aktualizacji systemu operacyjnego". Aby dowiedzieć się więcej na temat usług w chmurze i aktualizowanie wersji systemu operacyjnego dla usługi w chmurze, zobacz:

* [Cloud Services — omówienie](../cloud-services/cloud-services-choose-me.md)
* [Jak zaktualizować usługę w chmurze](../cloud-services/cloud-services-update-azure-service.md)
* [Jak skonfigurować usługi w chmurze](../cloud-services/cloud-services-how-to-configure-portal.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje o sposobie monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) — Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
