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
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552988"
---
# <a name="monitor-identity-and-access-preview"></a>Monitoruj tożsamość i dostęp (wersja zapoznawcza)
Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule objaśniono stronę informacje o **tożsamości i dostępie** w sekcji zabezpieczenia zasobów Azure Security Center.

Aby zapoznać się z pełną listą zaleceń, które mogą zostać wyświetlone na tej stronie, zobacz [zalecenia dotyczące tożsamości i dostępu](recommendations-reference.md#recs-identity).

> [!NOTE]
> Monitorowanie tożsamości i dostępu jest w wersji zapoznawczej i dostępne tylko w warstwie Standardowa Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>

Tożsamość powinna być płaszczyzną kontroli dla przedsiębiorstwa, a Ochrona tożsamości powinna stanowić najwyższy priorytet. Obwód zabezpieczeń został rozwijający się od obwodu sieci do obwodu tożsamości. Zabezpieczenia są mniej dotyczące obrony sieci i więcej informacji na temat obrony danych, a także do zarządzania zabezpieczeniami aplikacji i użytkowników. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Dzięki monitorowaniu działań związanych z tożsamością możesz podejmować prewencyjne działania przed wystąpieniem zdarzenia i reagować na próby ataku. Na pulpicie nawigacyjnym dostępu & tożsamości są dostępne zalecenia, takie jak:

- Włącz usługę MFA dla kont uprzywilejowanych w ramach subskrypcji
- Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji
- Usuń uprzywilejowane konta zewnętrzne z subskrypcji

> [!NOTE]
> Jeśli subskrypcja ma więcej niż 600 kont, Security Center nie może uruchomić zaleceń dotyczących tożsamości dla subskrypcji. Zalecenia, które nie są uruchamiane, znajdują się w obszarze "oceny niedostępne" poniżej.
Security Center nie może uruchomić zaleceń dotyczących tożsamości dla agentów administratora dostawcy rozwiązań w chmurze (CSP).
>

## <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu

Otwórz listę zidentyfikowanych problemów dotyczących tożsamości i dostępu, wybierając pozycję **tożsamość & dostęp** z paska bocznego Security Center (w obszarze **zasoby**) lub na stronie Przegląd. 

W obszarze **tożsamość & dostęp**istnieją dwie karty:

- **Przegląd**: zalecenia zidentyfikowane przez Security Center.
- **Subskrypcje**: Lista subskrypcji i bieżący stan zabezpieczeń każdego z nich.

[![& dostępu do tożsamości](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Sekcja Omówienie
W obszarze **Przegląd**znajduje się lista zaleceń. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera łączną liczbę subskrypcji, których dotyczy to zalecenie. Trzecia kolumna przedstawia ważność problemu.

1. Wybierz zalecenie. Zostanie otwarte okno rekomendacje zawierające następujące elementy:

   - Opis zalecenia
   - Lista nieprawidłowych i zdrowych subskrypcji
   - Lista zasobów, które są odskanowane z powodu niepowodzenia oceny lub zasób jest objęty subskrypcją w warstwie Bezpłatna i nie został oceniony

    [okno rekomendacje ![](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Wybierz subskrypcję z listy, aby uzyskać dodatkowe szczegóły.

### <a name="subscriptions-section"></a>Sekcja subskrypcje
W obszarze **subskrypcje**istnieje lista subskrypcji. W pierwszej kolumnie są wyświetlane subskrypcje. Druga kolumna zawiera łączną liczbę zaleceń dla każdej subskrypcji. Trzecia kolumna przedstawia liczbę problemów.

[Karta subskrypcje ![](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Wybierz subskrypcję. Otwiera widok podsumowania z trzema kartami:

   - **Zalecenia dotyczące**: oparte na ocen wykonywane przez usługę Security Center, który uległ awarii.
   - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
   - **Niedostępne oceny**: Lista ocen, których uruchomienie nie powiodło się z powodu błędu lub ponieważ subskrypcja ma więcej niż 600 kont.

   W obszarze **zalecenia** znajduje się lista zaleceń dotyczących wybranej subskrypcji i ważności poszczególnych zaleceń.

   [Zalecenia dotyczące ![wyboru subskrypcji](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Wybierz zalecenie dotyczące opisu zalecenia, listę nieprawidłowych i zdrowych subskrypcji oraz listę niezeskanowanych zasobów.

   [![opis rekomendacji](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   W obszarze **przekazywane ocen** znajduje się lista oceny zakończone pomyślnie.  Ważność te oceny zawsze ma kolor zielony.

   [![przekazywać oceny](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Wybierz przekazaną ocenę z listy, aby uzyskać opis oceny i listę subskrypcji w dobrej kondycji. Istnieje karta dla subskrypcji w złej kondycji, która wyświetla listę wszystkich subskrypcji, które nie powiodły się.

   [![przekazywać oceny](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Jeśli zostały utworzone zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego, ale mają ustawione wykluczenia, Ocena rekomendacji Security Center MFA uważa, że zasady nie są zgodne, ponieważ umożliwia ona użytkownikom logowanie się na platformie Azure bez uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

- [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
- [Ochrona usługi Azure SQL i danych w Azure Security Center](security-center-sql-service-recommendations.md)