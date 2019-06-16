---
title: Włącz agenta maszyny Wirtualnej w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób realizacji zalecenia w usłudze Azure Security Center **Włącz agenta maszyny Wirtualnej**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 12934ad53050d16b89dd5b4175ca19a24d1ec4d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60916297"
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Włącz agenta maszyny Wirtualnej w usłudze Azure Security Center
Agent maszyny Wirtualnej musi być zainstalowany na maszynach wirtualnych (VM), w celu [Włącz zbieranie danych](security-center-enable-data-collection.md).  Usługa Azure Security Center umożliwia można sprawdzić, którą maszyny wirtualne wymagają agenta maszyny Wirtualnej i oferuje rekomendacje dotyczące włączenia agenta maszyny Wirtualnej na tych maszynach wirtualnych.

Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia. Nie jest to przewodnik krok po kroku.
>
>

## <a name="implement-the-recommendation"></a>Zaimplementuj zalecenia
1. W **blok zalecenia**, wybierz opcję **Włącz agenta maszyny Wirtualnej**.
   ![Włącz agenta maszyny wirtualnej][1]
2. Spowoduje to otwarcie bloku **VM Agent Brak lub nie odpowiada**. Ten blok zawiera listę maszyn wirtualnych, które wymagają agenta maszyny Wirtualnej. Postępuj zgodnie z instrukcjami w bloku, aby zainstalować agenta maszyny Wirtualnej.
   ![Brak agenta maszyny Wirtualnej][2]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — informacje o tym, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
