---
title: Monitorowanie tożsamości i dostępu w usłudze Azure Security Center | Microsoft Docs
description: Dowiedz się, jak korzystać z funkcji zarządzania tożsamościami i dostępem w usłudze Azure Security Center w celu monitorowania dostępu użytkowników i rozwiązywania problemów związanych z tożsamościami.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: v-mohabe
ms.openlocfilehash: 1ef4d8e76b54b995a09decdfaf9974f0929c8ed9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905340"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorowanie tożsamości i dostępu w Azure Security Center (wersja zapoznawcza)
Ten artykuł ułatwia korzystanie z usługi Azure Security Center w celu monitorowania tożsamości i dostępu użytkowników.

> [!NOTE]
> Link "Wyświetl *klasyczny* & dostępu" zostanie wycofany 31 lipca 2019. Kliknij [tutaj](security-center-features-retirement-july2019.md#menu_classicidentity) , aby dowiedzieć się więcej na temat alternatywnych usług.

> [!NOTE]
> Monitorowanie tożsamości i dostępu jest w wersji zapoznawczej i dostępne tylko w warstwie Standardowa Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>

Tożsamość powinna być warstwą kontroli w Twoim przedsiębiorstwie, a ochrona tożsamości powinna stanowić najwyższy priorytet. Obwód zabezpieczeń został rozwijający się od obwodu sieci do obwodu tożsamości. Zabezpieczenia są mniej dotyczące obrony sieci i więcej informacji na temat obrony danych, a także do zarządzania zabezpieczeniami aplikacji i użytkowników. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Dzięki monitorowaniu działań związanych z tożsamością możesz podejmować prewencyjne działania przed wystąpieniem zdarzenia i reagować na próby ataku. Na pulpicie nawigacyjnym dostępu & tożsamości są dostępne zalecenia, takie jak:

- Włącz usługę MFA dla kont uprzywilejowanych w ramach subskrypcji
- Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji
- Usuń uprzywilejowane konta zewnętrzne z subskrypcji

> [!NOTE]
> Jeśli subskrypcja ma więcej niż 600 kont, Security Center nie może uruchomić zaleceń dotyczących tożsamości dla subskrypcji. Zalecenia, które nie są uruchamiane, są wymienione w sekcji "niedostępne oceny", która została omówiona poniżej.
Security Center nie może uruchomić zaleceń dotyczących tożsamości dla agentów administratora dostawcy rozwiązań w chmurze (CSP).
>

Zapoznaj się z listą rekomendacji i rekomendacji dotyczących dostępu zapewnianych przez Security Center. [](security-center-identity-access.md#recommendations)

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Możesz monitorować stan zabezpieczeń zasobów na **Security Center — Przegląd** pulpitu nawigacyjnego. Sekcja Resources to wskaźnik kondycji przedstawiający serwery dla każdego typu zasobu.

Można wyświetlić listę wszystkich problemów, wybierając **zalecenia**. W obszarze **zasoby**można wyświetlić listę problemów specyficznych dla obliczeń & aplikacji, zabezpieczeń danych, sieci lub tożsamości & dostępu. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zaleceń dotyczących tożsamości i dostępu, zobacz [zalecenia](security-center-identity-access.md#recommendations).

Aby kontynuować, wybierz pozycję **tożsamość & dostęp** w obszarze **zasoby** lub Security Center menu główne.

![Pulpit nawigacyjny usługi Security Center][1]

## <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu
W obszarze **tożsamość & dostęp**istnieją dwie karty:

- **Przegląd**: zalecenia zidentyfikowane przez Security Center.
- **Subskrypcje**: Lista subskrypcji i bieżący stan zabezpieczeń każdego z nich.

![Tożsamość i dostęp][2]

### <a name="overview-section"></a>Sekcja przegląd
W obszarze **Przegląd**znajduje się lista zaleceń. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera łączną liczbę subskrypcji, których dotyczy to zalecenie. Trzecia kolumna przedstawia ważność problemu.

1. Wybierz zalecenie. Zostanie otwarte okno Rekomendacja:

   - Opis zalecenia
   - Lista nieprawidłowych i zdrowych subskrypcji
   - Lista zasobów, które są odskanowane z powodu niepowodzenia oceny lub zasób jest objęty subskrypcją w warstwie Bezpłatna i nie został oceniony

   ![Okno rekomendacji][3]

1. Wybierz subskrypcję z listy, aby uzyskać dodatkowe szczegóły.

### <a name="subscriptions-section"></a>Sekcja subskrypcje
W obszarze **subskrypcje**istnieje lista subskrypcji. W pierwszej kolumnie są wyświetlane subskrypcje. Druga kolumna zawiera łączną liczbę zaleceń dla każdej subskrypcji. Trzecia kolumna przedstawia liczbę problemów.

![Karta subskrypcji][4]

1. Wybierz subskrypcję. Otwiera widok podsumowania z trzema kartami:

   - **Zalecenia dotyczące**: oparte na ocen wykonywane przez usługę Security Center, który uległ awarii.
   - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
   - **Niedostępne oceny**: Lista ocen, których uruchomienie nie powiodło się z powodu błędu lub ponieważ subskrypcja ma więcej niż 600 kont.

   W obszarze **zalecenia** znajduje się lista zaleceń dotyczących wybranej subskrypcji i ważności poszczególnych zaleceń.

   ![Zalecenia dotyczące wybierania subskrypcji][5]

1. Wybierz zalecenie dotyczące opisu zalecenia, listę nieprawidłowych i zdrowych subskrypcji oraz listę niezeskanowanych zasobów.

   ![Opis zalecenia][6]

   W obszarze **przekazywane ocen** znajduje się lista oceny zakończone pomyślnie.  Ważność te oceny zawsze ma kolor zielony.

   ![Oceny zakończone pomyślnie][7]

1. Wybierz przekazaną ocenę z listy, aby uzyskać opis oceny i listę subskrypcji w dobrej kondycji. Istnieje karta dla subskrypcji w złej kondycji, która wyświetla listę wszystkich subskrypcji, które nie powiodły się.

   ![Oceny zakończone pomyślnie][8]

## <a name="recommendations"></a>Zalecenia
Skorzystaj z poniższej tabeli jako odniesienia, aby pomóc zrozumieć dostępną tożsamość & zaleceniami dostępu i co każdy z nich ma w przypadku zastosowania go.

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Subscription|50|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji|Włącz uwierzytelnianie wieloskładnikowe (MFA) dla wszystkich kont subskrypcji z uprawnieniami administratora, aby zapobiec naruszeniu kont lub zasobów.|
|Subscription|40|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach subskrypcji z uprawnieniami do zapisu|Włącz Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów.|
|Subscription|30|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami właściciela z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.|
|Subscription|30|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach subskrypcji z uprawnieniami do odczytu|Włącz Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów.|
|Subscription|25|Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |
|Subscription|20|Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Usuń przestarzałe konta z uprawnieniami właściciela z subskrypcji.|
|Subscription|5|Przestarzałe konta powinny zostać usunięte z subskrypcji|Usuń przestarzałe konta z subskrypcji, aby umożliwić dostęp tylko bieżącym użytkownikom. |
|Subscription|5|Do subskrypcji powinien być przypisany więcej niż jeden właściciel|Wyznacz więcej niż jednego właściciela subskrypcji, aby zapewnić nadmiarowość dostępu administratora.|
|Subscription|5|Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Wyznacz mniej niż 3 właściciele subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela.|
|Magazyn kluczy|5|Dzienniki diagnostyczne w Key Vault powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Subscription|15|Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami do odczytu z subskrypcji, aby zapobiec niemonitorowanemu dostępowi.| 

> [!NOTE]
> Jeśli zostały utworzone zasady dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego, ale mają ustawione wykluczenia, Ocena rekomendacji Security Center MFA uważa, że zasady nie są zgodne, ponieważ umożliwia ona użytkownikom logowanie się na platformie Azure bez uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

- [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
- [Ochrona usługi Azure SQL i danych w Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
