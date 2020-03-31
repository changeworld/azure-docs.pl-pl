---
title: Monitorowanie tożsamości i dostępu w usłudze Azure Security Center | Microsoft Docs
description: Dowiedz się, jak korzystać z funkcji zarządzania tożsamościami i dostępem w usłudze Azure Security Center w celu monitorowania dostępu użytkowników i rozwiązywania problemów związanych z tożsamościami.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481963"
---
# <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu

> [!TIP]
> Od marca 2020 r. zalecenia dotyczące tożsamości i dostępu usługi Azure Security Center są uwzględniane we wszystkich subskrypcjach w bezpłatnej warstwie cenowej. Jeśli masz subskrypcje w warstwie bezpłatnej, ich bezpieczny wynik zostanie naruszony, ponieważ nie zostały wcześniej ocenione pod kątem ich zabezpieczeń tożsamości i dostępu. 

Gdy usługa Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które prowadzą użytkownika przez proces konfigurowania potrzebnych formantów w celu wzmocnienia i ochrony zasobów.

Obwód zabezpieczeń ewoluował od obwodu sieci do obwodu tożsamości. Bezpieczeństwo staje się mniej o obronie sieci, a więcej o obronie danych, a także zarządzanie bezpieczeństwem aplikacji i użytkowników. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Monitorując działania związane z tożsamością, można podjąć proaktywne działania przed zdarzeniem lub działania reaktywne, aby zatrzymać próbę ataku. Na przykład Usługa Security Center może oznaczać przestarzałe konta (konta, które nie są już potrzebne, i zablokowana przed logowaniem się przez usługę Azure Active Directory) w celu usunięcia. 

Przykłady zaleceń, które można zobaczyć w sekcji zabezpieczeń zasobów **tożsamości i dostępu** usługi Azure Security Center:

- Usługa MFA powinna być włączona na kontach z uprawnieniami właściciela w ramach subskrypcji
- Do subskrypcji należy wyznaczyć maksymalnie 3 właścicieli
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Przestarzałe konta powinny zostać usunięte z subskrypcji

Aby uzyskać więcej informacji na temat tych zaleceń, a także pełną listę zaleceń, które można znaleźć tutaj, zobacz [zalecenia dotyczące tożsamości i dostępu](recommendations-reference.md#recs-identity).

> [!NOTE]
> Jeśli subskrypcja ma więcej niż 600 kont, usługa Security Center nie może uruchomić rekomendacji tożsamości dotyczących subskrypcji. Zalecenia, które nie są uruchamiane są wymienione w sekcji "oceny niedostępne" poniżej.
Usługa Security Center nie może uruchamiać zaleceń dotyczących tożsamości wobec agentów administracyjnych partnera dostawcy rozwiązań w chmurze (CSP).
>


Wszystkie zalecenia dotyczące tożsamości i dostępu są dostępne w ramach dwóch zabezpieczeń na stronie **Zalecenia:**

- Zarządzanie dostępem i uprawnieniami 
- Włączanie usługi MFA

![Dwa mechanizmy kontroli bezpieczeństwa wraz z zaleceniami dotyczącymi tożsamości i dostępu](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Włączanie uwierzytelniania wieloskładnikowego (MFA)

Włączenie usługi MFA wymaga [uprawnień dzierżawy usługi Azure Active Directory (AD).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Jeśli masz wersję premium usługi AD, włącz usługę MFA przy użyciu [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Użytkownicy wersji bezpłatnej usługi AD mogą włączyć **domyślne zabezpieczenia** w usłudze Azure Active Directory zgodnie z opisem w [dokumentacji usługi AD,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) ale nadal będzie wyświetlane zalecenie usługi Security Center dotyczące włączenia usługi MFA.


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

- [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
- [Ochrona usługi i danych SQL platformy Azure w usłudze Azure Security Center](security-center-sql-service-recommendations.md)