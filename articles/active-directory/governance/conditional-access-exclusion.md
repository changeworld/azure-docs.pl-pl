---
title: Zarządzanie użytkownikami wykluczonymi z zasad dostępu warunkowego — usługa Azure AD
description: Dowiedz się, jak zarządzać użytkownikami wykluczonymi z zasad dostępu usługi Azure Active Directory (Azure AD) za pomocą recenzje dostępu usługi Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422712"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Zarządzanie użytkownikami wykluczonymi z zasad dostępu warunkowego za pomocą przeglądów dostępu usługi Azure AD

W idealnym świecie wszyscy użytkownicy będą postępować zgodnie z zasady dostępu, aby zapewnić dostęp do zasobów organizacji. Czasami jednak zdarzają się przypadki biznesowe, które wymagają wyjątków. W tym artykule opisano kilka przykładów, w których mogą być wymagane wykluczenia i jak użytkownik, jako administrator IT, może zarządzać tym zadaniem, unikać nadzoru nad wyjątkami zasad i dostarczać audytorom dowód, że wyjątki te są regularnie sprawdzane przy użyciu platformy Azure Przeglądy dostępu usługi Active Directory (Azure AD).

> [!NOTE]
> Do korzystania z recenzji dostępu usługi Azure AD AD Access wymagana jest prawidłowa licencja azure ad premium P2, enterprise mobility + security E5 płatna lub licencja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Dlaczego chcesz wykluczyć użytkowników z zasad?

Jako administrator IT możesz użyć [dostępu warunkowego usługi Azure AD,](../conditional-access/overview.md) aby wymagać od użytkowników uwierzytelniania przy użyciu uwierzytelniania wieloskładnikowego (MFA) lub logowania się z zaufanej sieci lub urządzenia. Podczas planowania wdrożenia dowiesz się, że niektóre z tych wymagań nie mogą być spełnione przez wszystkich użytkowników. Na przykład są użytkownicy, którzy pracują z biura zdalnego, które nie jest częścią sieci wewnętrznej lub jest dyrektor, który używa starego telefonu, który nie jest obsługiwany. Firma wymaga, aby ci użytkownicy mogli się logować i wykonywać swoje zadania, w związku z czym są wykluczeni z zasad dostępu warunkowego.

Innym przykładem można użyć [nazwanych lokalizacji](../conditional-access/location-condition.md) w dostępie warunkowym do skonfigurowania zestawu powiatów i regionów, z których nie chcesz zezwalać użytkownikom na dostęp do ich dzierżawy.

![Nazwane lokalizacje w programie "Dostęp warunkowy"](./media/conditional-access-exclusion/named-locations.png)

Jednak w niektórych przypadkach użytkownicy mogą mieć uzasadniony powód, aby zalogować się z tych zablokowanych krajów/regionów. Na przykład użytkownicy mogą podróżować z powodów służbowych lub osobistych. W tym przykładzie zasady dostępu warunkowego do blokowania tych krajów/regionów może mieć dedykowaną grupę zabezpieczeń w chmurze dla użytkowników, którzy są wykluczeni z zasad. Użytkownicy, którzy potrzebują dostępu podczas podróży, mogą dodać się do grupy za pomocą [samoobsługowego zarządzania grupami usługi Azure AD](../users-groups-roles/groups-self-service-management.md).

Innym przykładem może być zasada dostępu warunkowego, która [blokuje starsze uwierzytelnianie dla większości użytkowników.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Firma Microsoft zdecydowanie zaleca zablokowanie używania starszych protokołów w dzierżawie w celu poprawy stanu zabezpieczeń. Jeśli jednak niektórzy użytkownicy muszą bezwzględnie używać starszych metod uwierzytelniania, aby uzyskać dostęp do zasobów za pośrednictwem klientów opartych na pakiecie Office 2010 lub IMAP/SMTP/POP, możesz wykluczyć tych użytkowników z zasad blokujących starsze metody uwierzytelniania.

## <a name="why-are-exclusions-challenging"></a>Dlaczego wykluczenia są wyzwaniem?

W usłudze Azure AD można określić zakres zasad dostępu warunkowego do zestawu użytkowników. Można również wykluczyć niektórych z tych użytkowników, wybierając role usługi Azure AD, poszczególnych użytkowników lub gości użytkowników. Należy pamiętać, że po skonfigurowaniu tych wykluczeń intencji zasad nie można wymuszać dla tych użytkowników. Jeśli te wyłączenia zostały skonfigurowane jako lista poszczególnych użytkowników lub za pośrednictwem starszej lokalnej grupy zabezpieczeń, ogranicza to widoczność tej listy wykluczeń (użytkownicy mogą nie wiedzieć o jej istnieniu) i kontroli nad nim przez administratora IT (użytkownicy mogą dołączyć do tej listy wykluczeń( użytkownicy mogą nie wiedzieć o jej istnieniu) i kontroli administratora IT nad nim (użytkownicy mogą dołączyć do grupy zabezpieczeń, aby ominąć zasady). Ponadto użytkownicy, którzy zakwalifikowali się do wykluczenia w tym samym czasie, mogą już go nie potrzebować lub kwalifikować się do niego.

Na początku wykluczenia istnieje krótka lista użytkowników, którzy omijają zasady. Z biegiem czasu coraz więcej użytkowników jest wykluczonych, a lista rośnie. W pewnym momencie istnieje potrzeba przejrzenia listy i potwierdzenia, że każdy z tych użytkowników powinien być nadal wykluczony. Zarządzanie listą z technicznego punktu widzenia może być stosunkowo łatwe, ale kto podejmuje decyzje biznesowe i jak upewnić się, że wszystko podlega audytowi?

Jeśli jednak skonfigurujesz wykluczenie do zasad dostępu warunkowego przy użyciu grupy usługi Azure AD, możesz użyć przeglądów dostępu jako formantu kompensacyjnego, aby zwiększyć widoczność i zmniejszyć liczbę użytkowników, którzy mają wyjątek.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak utworzyć grupę wykluczeń w zasadach dostępu warunkowego

Wykonaj następujące kroki, aby utworzyć nową grupę usługi Azure AD i zasady dostępu warunkowego, które nie mają zastosowania do tej grupy.

### <a name="create-an-exclusion-group"></a>Tworzenie grupy wykluczeń

1. Zaloguj się do Portalu Azure.

1. W lewej nawigacji kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Grupy**.

1. W górnym menu kliknij pozycję **Nowa grupa,** aby otworzyć okienko grupy.

1. Na liście **Typ grupy** wybierz pozycję **Zabezpieczenia**. Określ nazwę i opis.

1. Upewnij się, że typ **członkostwa** jest **przypisany**.

1. Wybierz użytkowników, którzy powinni być częścią tej grupy wykluczeń, a następnie kliknij przycisk **Utwórz**.

    ![Nowe okienko grup w usłudze Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Tworzenie zasad dostępu warunkowego, które wykluczają grupę

Teraz można utworzyć zasadę dostępu warunkowego, która używa tej grupy wykluczeń.

1. W lewej nawigacji kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję Dostęp **warunkowy,** aby otworzyć blok **Zasady.**

1. Kliknij **pozycję Nowe zasady,** aby otworzyć okienko **Nowe.**

1. Określ nazwę.

1. W obszarze Przydziały kliknij **pozycję Użytkownicy i grupy**.

1. Na karcie **Dołączanie** wybierz pozycję **Wszyscy użytkownicy**.

1. Na karcie **Wykluczanie** dodaj znacznik wyboru do **użytkowników i grup,** a następnie kliknij pozycję **Wybierz wykluczonych użytkowników**.

1. Wybierz utworzoną grupę wykluczeń.

    > [!NOTE]
    > Najlepszym rozwiązaniem jest zaleca się wykluczenie co najmniej jednego konta administratora z zasad podczas testowania, aby upewnić się, że nie są zablokowane z dzierżawy.

1. Kontynuuj konfigurowanie zasad dostępu warunkowego na podstawie wymagań organizacji.

    ![Okienko Wykluczanie użytkowników w programie Dostęp warunkowy](./media/conditional-access-exclusion/select-excluded-users.png)

Omówimy dwa przykłady, w których można używać przeglądów dostępu do zarządzania wykluczeniami w zasadach dostępu warunkowego.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Przykład 1: Przegląd dostępu dla użytkowników uzyskujących dostęp z zablokowanych krajów/regionów

Załóżmy, że masz zasady dostępu warunkowego, które blokują dostęp z niektórych krajów/regionów. Obejmuje grupę, która jest wykluczona z zasad. Oto zalecany przegląd dostępu, w którym członkowie grupy są przeglądani.

> [!NOTE]
> Do tworzenia recenzji dostępu wymagana jest rola administratora globalnego lub administratora użytkownika.

1. Przegląd będzie powtarzał się co tydzień.

2. Nigdy się nie skończy, aby upewnić się, że utrzymujesz tę grupę wykluczeń najbardziej aktualną.

3. Wszyscy członkowie tej grupy będą w zakresie przeglądu.

4. Każdy użytkownik będzie musiał samodzielnie potwierdzić, że nadal musi mieć dostęp z tych zablokowanych krajów/regionów, dlatego nadal musi być członkiem grupy.

5. Jeśli użytkownik nie odpowie na żądanie recenzji, zostanie automatycznie usunięty z grupy i w związku z tym nie będzie już mógł uzyskać dostępu do dzierżawy podczas podróży do tych krajów/regionów.

6. Włącz powiadomienia poczty, aby użytkownicy otrzymywać powiadomienia o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Przykład 2: Przegląd programu Access dla użytkowników uzyskujących dostęp za pomocą uwierzytelniania starszego

Załóżmy, że masz zasadę dostępu warunkowego, która blokuje dostęp dla użytkowników korzystających ze starszego uwierzytelniania i starszych wersji klienta. Obejmuje grupę, która jest wykluczona z zasad. Oto zalecany przegląd dostępu, w którym członkowie grupy są przeglądani.

1. Przegląd ten musiałby być przeglądem cyklicznym.

2. Wszyscy członkowie grupy musieliby zostać zrewiasowani.

3. Można go skonfigurować tak, aby wymieniał właścicieli jednostek biznesowych jako wybranych recenzentów.

4. Automatyczne stosowanie wyników i usuwanie użytkowników, którzy nie zostali zatwierdzeni, aby kontynuować korzystanie ze starszych metod uwierzytelniania.

5. Może być korzystne włączenie zaleceń, aby recenzenci dużych grup mogli łatwo podejmować decyzje.

6. Włącz powiadomienia poczty, aby użytkownicy otrzymywać powiadomienia o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Porada**dla profesjonalistów: Jeśli masz wiele grup wykluczeń i dlatego musisz utworzyć wiele recenzji dostępu, mamy teraz interfejs API w punkcie końcowym wersji beta programu Microsoft Graph, który umożliwia tworzenie i zarządzanie nimi programowo. Aby rozpocząć, zobacz [odwołanie do interfejsu API dostępu usługi Azure AD i](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) przykład pobierania [przeglądów dostępu usługi Azure AD za pośrednictwem programu Microsoft Graph.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)

## <a name="access-review-results-and-audit-logs"></a>Uzyskiwanie dostępu do wyników przeglądu i dzienników inspekcji

Teraz, gdy masz wszystko na swoim miejscu, grupę, zasady dostępu warunkowego i przeglądy dostępu, nadszedł czas, aby monitorować i śledzić wyniki tych recenzji.

1. W witrynie Azure portal otwórz bloku **przeglądy programu Access.**

1. Otwórz formant i program utworzony do zarządzania grupą wykluczeń.

1. Kliknij **wyniki,** aby zobaczyć, kto został zatwierdzony do pozostania na liście, a kto został usunięty.

    ![Wyniki opinii programu Access pokazują, kto został zatwierdzony](./media/conditional-access-exclusion/access-reviews-results.png)

1. Następnie kliknij **inspekcję dzienników,** aby zobaczyć akcje, które zostały podjęte podczas tej recenzji.

    ![Dzienniki inspekcji przeglądów programu Access z listą akcji](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako administrator IT wiesz, że zarządzanie grupami wykluczeń w zasadach jest czasami nieuniknione. Jednak utrzymanie tych grup, przeglądanie ich regularnie przez właściciela firmy lub samych użytkowników i inspekcji tych zmian może ułatwić dzięki przeglądom dostępu usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
