---
title: Ustawienia Azure Security Center | Microsoft Docs
description: Skonfiguruj ustawienia Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201614"
---
# <a name="security-center-settings"></a>Ustawienia usługi Security Center
Ten artykuł zawiera omówienie ustawień w Security Center.

Następujące ustawienia można uzyskać w obszarze Zasady zabezpieczeń:

- **Zbieranie danych**: Określa ustawienia aprowizacji agentów i [zbierania danych](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) .
- **Zasady zabezpieczeń**: Określa, które formanty Security Center monitory i zalecane. [Zasady zabezpieczeń](tutorial-security-policy.md) można edytować w Security Center. Można również użyć [Azure Policy](tutorial-security-policy.md) do tworzenia nowych definicji, definiowania dodatkowych zasad i przypisywania zasad w grupach zarządzania. 
- **Powiadomienia e-mail**: Określa kontakty zabezpieczeń i ustawienia [powiadomień e-mail](security-center-provide-security-contact-details.md) .
- **Warstwa cenowa**: Definiuje [wybór cen dla warstwy](security-center-pricing.md)bezpłatna lub standardowa. Wybrana warstwa określa, które funkcje usługi Security Center są dostępne dla zasobów w zakresie. Możesz określić warstwę dla subskrypcji i obszarów roboczych.

> [!NOTE]
> Wszystkie te ustawienia można ustawić na subskrypcję. W przypadku obszarów roboczych można ustawić tylko zbieranie danych i warstwę cenową.
>


## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Security Center korzysta z Access Control opartych na rolach (RBAC), które udostępnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Gdy użytkownicy otworzą Security Center, zobaczą tylko te informacje, które są związane z zasobami, do których mają dostęp. Oznacza to, że użytkownicy mają przypisaną rolę *właściciela*,współautora lub czytelnika do subskrypcji, do której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- **Czytelnik zabezpieczeń**: Mieć uprawnienia do widoku Security Center, w tym zalecenia, alerty, zasady i kondycję, ale nie mogą wprowadzać zmian.
- **Administrator zabezpieczeń**: Mają takie same uprawnienia do wyświetlania jak *czytelnik zabezpieczeń*i mogą także aktualizować zasady zabezpieczeń i odrzucać zalecenia i alerty.


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono informacje dotyczące zasad zabezpieczeń w programie Azure Security Center. Aby dowiedzieć się więcej na temat Azure Security Center, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md): Dowiedz się, w jaki sposób Security Center zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center zabezpieczenia danych](security-center-data-security.md): Dowiedz się, jak Security Center zarządza i zabezpiecza dane.
* [Azure Security Center często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/): Zapoznaj się z najnowszymi informacjami o zabezpieczeniach platformy Azure.
