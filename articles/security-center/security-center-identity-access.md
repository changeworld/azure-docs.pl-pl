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
ms.author: monhaber
ms.openlocfilehash: 16548ae75567fa3ba6f8c9135d61945bd28d2db8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428424"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Monitorowanie tożsamości i dostępu w usłudze Azure Security Center (wersja zapoznawcza)
Ten artykuł ułatwia korzystanie z usługi Azure Security Center w celu monitorowania tożsamości i dostępu użytkowników.

> [!NOTE]
> "Wyświetl *klasycznego* tożsamość i dostęp" link zostaną wycofane z dniem 31 lipca 2019 r. Kliknij przycisk [tutaj](security-center-features-retirement-july2019.md#menu_classicidentity) Aby dowiedzieć się więcej na usługach alternatywne.

> [!NOTE]
> Monitorowanie tożsamości i dostępu jest dostępna w wersji zapoznawczej i jest dostępna tylko w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>

Tożsamość powinna być warstwą kontroli w Twoim przedsiębiorstwie, a ochrona tożsamości powinna stanowić najwyższy priorytet. Zabezpieczeń obwodowych powstał z obwód sieci obwodowej tożsamości. Zabezpieczenia staje się mniej o obronie sieci i więcej o obronie dane, a także zarządzanie zabezpieczeniami aplikacji i użytkowników. Dzisiaj, w związku z przeniesieniem większej ilości danych i aplikacji do chmury, tożsamość staje się nową strefą.

Dzięki monitorowaniu działań związanych z tożsamością możesz podejmować prewencyjne działania przed wystąpieniem zdarzenia i reagować na próby ataku. Pulpit nawigacyjny tożsamości i dostępu zawiera zalecenia dotyczące takich jak:

- Włącz usługę MFA dla kont uprzywilejowanych w ramach subskrypcji
- Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji
- Usuń uprzywilejowane konta zewnętrzne z subskrypcji

> [!NOTE]
> Jeśli Twoja subskrypcja obejmuje ponad 600 kont, usługa Security Center nie może uruchamiać zalecenia dotyczące tożsamości dla Twojej subskrypcji. Zalecenia, które nie są uruchamiane są wyświetlane w obszarze "oceny niedostępne", które omówiono poniżej.
Usługa Security Center nie może uruchamiać zalecenia dotyczące tożsamości dla agentów administratora partnerem Cloud Solution Provider (CSP).
>

Zobacz [zalecenia](security-center-identity-access.md#recommendations) listę zaleceń dotyczących tożsamości i dostępu dostarczone przez usługę Security Center.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Możesz monitorować stan zabezpieczeń zasobów na **Security Center — Przegląd** pulpitu nawigacyjnego. **Zasobów** sekcja jest wskaźnik kondycji przedstawiający ważności dla każdego typu zasobu.

Można wyświetlić listę wszystkich problemów, wybierając **zalecenia**. W obszarze **zasobów**, można wyświetlić listę kwestie związane z obliczeniowe i aplikacje, bezpieczeństwo danych, sieci, lub tożsamość i dostęp. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zaleceń dotyczących tożsamości i dostępu, zobacz [zalecenia](security-center-identity-access.md#recommendations).

Aby kontynuować, wybierz **tożsamość i dostęp** w obszarze **zasobów** lub w menu głównym usługi Security Center.

![Pulpit nawigacyjny usługi Security Center][1]

## <a name="monitor-identity-and-access"></a>Monitorowanie tożsamość i dostępu
W obszarze **tożsamość i dostęp**, istnieją dwie karty:

- **Omówienie**: zaleceniami zostały zidentyfikowane przez usługę Security Center.
- **Subskrypcje**: listę subskrypcji i bieżący stan zabezpieczeń każdego z nich.

![Tożsamość i dostęp][2]

### <a name="overview-section"></a>Sekcja — Omówienie
W obszarze **Przegląd**, znajduje się lista zaleceń. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera całkowitą liczbę subskrypcji, których dotyczy tego zalecenia. Trzecia kolumna pokazuje wagę problemu.

1. Wybierz zalecenie. Zostanie wyświetlone okno dialogowe i wyświetla zalecenia:

   - Opis rekomendacji
   - Lista subskrypcji w złej kondycji i w dobrej kondycji
   - Lista zasobów, które są nieprzeskanowane ze względu na niepowodzenie oceny lub zasobu w ramach subskrypcji, uruchomiony w ramach warstwy bezpłatna i nie jest oceniany

   ![Okno w zalecenie][3]

1. Wybierz subskrypcję, na liście, aby uzyskać dodatkowe szczegóły.

### <a name="subscriptions-section"></a>Subskrypcje
W obszarze **subskrypcje**, znajduje się lista subskrypcji. W pierwszej kolumnie wyświetlane subskrypcje. Druga kolumna zawiera całkowitą liczbę zaleceń dla każdej subskrypcji. Trzecia kolumna pokazuje ważności problemów.

![Karta subskrypcji][4]

1. Wybierz subskrypcję. Otwiera widok podsumowania z trzema kartami:

   - **Zalecenia dotyczące**: oparte na ocen wykonywane przez usługę Security Center, który uległ awarii.
   - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
   - **Oceny niedostępne**: Lista ocen, których nie można uruchomić z powodu błędu lub subskrypcja zawiera ponad 600 kont.

   W obszarze **zalecenia** listę zaleceń dla wybranej subskrypcji i ważność poszczególne zalecenia.

   ![Zalecenia dotyczące Wybieranie subskrypcji][5]

1. Wybierz zalecenie opis zalecenie, lista złej kondycji, jak i dobrej kondycji i listę nieprzeskanowane zasoby.

   ![Opis rekomendacji][6]

   W obszarze **przekazywane ocen** znajduje się lista oceny zakończone pomyślnie.  Ważność te oceny zawsze ma kolor zielony.

   ![Oceny zakończone pomyślnie][7]

1. Wybierz ocenę przekazany z listy opis oceny i listę subskrypcji w dobrej kondycji. Brak karta dla subskrypcji w złej kondycji, która wyświetla wszystkie subskrypcje, które nie powiodło się.

   ![Oceny zakończone pomyślnie][8]

## <a name="recommendations"></a>Zalecenia
Używany jako odwołanie w tabeli poniżej, aby lepiej zrozumieć dostępne zalecenia dotyczące tożsamości i dostępu, a każdy z nich działanie w przypadku zastosowania.

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Subskrypcja|50|Uwierzytelnianie wieloskładnikowe powinno być włączone dla kont z uprawnieniami właściciela subskrypcji|Włączanie usługi Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami administratora w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|Subskrypcja|40|Uwierzytelnianie wieloskładnikowe powinna być włączona dla konta subskrypcji z uprawnieniami do zapisu|Włączanie usługi Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|Subskrypcja|30|Zewnętrzne konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami właściciela z subskrypcji, aby uniknąć niemonitorowanego dostępu.|
|Subskrypcja|30|Uwierzytelnianie wieloskładnikowe powinna być włączona dla konta subskrypcji z uprawnieniami do odczytu|Włączanie usługi Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów.|
|Subskrypcja|25|Zewnętrzne konta z zapisu czy uprawnienia powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji, aby uniknąć niemonitorowanego dostępu. |
|Subskrypcja|20|Przestarzałe konta z uprawnieniami właściciela, powinny zostać usunięte z subskrypcji|Usuń przestarzałe konta z uprawnieniami właściciela z subskrypcji.|
|Subskrypcja|5|Przestarzałe konta powinny zostać usunięte z subskrypcji|Usuń przestarzałe konta z subskrypcji, aby umożliwić dostęp do tylko bieżąca liczba użytkowników. |
|Subskrypcja|5|Powinna istnieć więcej niż jeden właściciel został przypisany do Twojej subskrypcji|Wyznaczenie więcej niż jednego właściciela subskrypcji w celu posiadania nadmiarowości dostępu administratora.|
|Subskrypcja|5|Maksymalnie 3 właścicieli należy wyznaczyć dla Twojej subskrypcji|Wyznaczanie mniejszej niż 3 właścicieli subskrypcji, aby zmniejszyć ryzyko naruszenia zabezpieczeń przez właściciela z naruszonymi zabezpieczeniami.|
|Magazyn kluczy|5|Dzienniki diagnostyczne w usłudze Key Vault powinno być włączone.|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Subskrypcja|15|Zewnętrzne konta z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Usuń konta zewnętrzne z uprawnieniami do odczytu z subskrypcji, aby uniknąć niemonitorowanego dostępu.| 

> [!NOTE]
> Jeśli utworzono zasady dostępu warunkowego, który wymaga uwierzytelniania Wieloskładnikowego, ale ma wykluczenia Ustaw oceny zalecenia usługi Security Center MFA uwzględnia zasady niezgodne, ponieważ umożliwia niektórzy użytkownicy zalogować się do platformy Azure bez użycia usługi MFA.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

- [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
- [Ochrona usługi Azure SQL i danych w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

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
