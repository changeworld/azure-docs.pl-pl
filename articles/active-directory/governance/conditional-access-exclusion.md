---
title: Za pomocą przeglądów dostępu można zarządzać użytkowników wykluczone z zasad dostępu warunkowego — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać użytkownikami, które zostały wykluczone z zasad dostępu warunkowego za pomocą przeglądów dostępu w usłudze Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7675441316e42c7f0a220abe77bc8c62158ef918
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351602"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Przeglądy dostępu usługa Azure AD do zarządzania użytkownikami wykluczone z zasad dostępu warunkowego

W świecie idealne, wszyscy użytkownicy wykonać dostępu zasady, aby zabezpieczyć dostęp do zasobów organizacji. Jednak czasami istnieją przypadków biznesowych, które wymagają wprowadzenia wyjątków. W tym artykule opisano kilka przykładów, w którym wykluczenia mogą być wymagane, jak administrator IT, zarządzanie tego zadania, uniknąć nadzoru wyjątki od zasad i zapewnić audytorów dowód tych wyjątków są przeglądane, regularnie korzystanie z platformy Azure Przeglądy dostępu w usłudze Active Directory (Azure AD).

> [!NOTE]
> Nieprawidłowa P2 usługi Azure AD Premium, pakietu Enterprise Mobility + Security E5, płatną lub próbną wersję licencja jest wymagana, aby używać usługi Azure AD przeglądów dostępu. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Dlaczego czy można wykluczyć użytkowników z zasad?

Jako IT administrator może użyć [dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) aby wymagać od użytkowników uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego (MFA) lub logowanie z zaufanej sieci lub urządzenia. Podczas planowania wdrożenia należy Dowiedz się, że niektóre z tych wymagań nie można spełnić przez wszystkich użytkowników. Na przykład znajdują się użytkownicy, którzy pracują w biurze oddziału, który nie jest częścią sieci wewnętrznej lub jest kierownikiem używający starego telefonu, który nie jest obsługiwany. Firma wymaga, ci użytkownicy zezwolenia Zaloguj się do wykonywania swojej pracy, dlatego są wykluczone z zasad dostępu warunkowego.

Inny przykład można użyć [lokalizacje z nazwą](../conditional-access/location-condition.md) dostępu warunkowego, aby skonfigurować zestaw powiatach i regionów, z których nie chcesz umożliwić użytkownikom dostęp do swojej dzierżawy.

![Lokalizacje nazwane](./media/conditional-access-exclusion/named-locations.png)

Jednak w niektórych przypadkach użytkownicy mogą mieć uzasadniona Przyczyna, aby zalogować się z tych krajów zablokowane. Na przykład użytkownicy mogą podróżować do pracy lub osobistych powodów. W tym przykładzie zasady dostępu warunkowego do blokowania tych krajów może mieć chmura dedykowana grupy zabezpieczeń dla użytkowników, którzy są wykluczeni z zasad. Użytkownicy, którzy muszą mieć dostęp podczas podróży, można samodzielnie dodać do grupy przy użyciu [Zarządzanie grupami samoobsługi usługi Azure AD](../users-groups-roles/groups-self-service-management.md).

Innym przykładem mogą być, że masz zasad dostępu warunkowego, [bloki uwierzytelnianie starszych większość użytkowników](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Firma Microsoft zaleca, zablokować użycie elementu starszych protokołów w dzierżawie, aby zwiększyć poziom bezpieczeństwa. Jednak w przypadku niektórych użytkowników, którzy absolutnie należy użyć metody uwierzytelniania w starszej wersji, dostęp do zasobów za pomocą pakietu Office 2010 lub IMAP/SMTP/POP oparty na klientach, następnie można wykluczyć użytkowników z zasad blokowania metod uwierzytelniania w starszej wersji.

## <a name="why-are-exclusions-challenging"></a>Wykluczenia są trudne

W usłudze Azure AD można określić zakres zasad dostępu warunkowego dla zestawu użytkowników. Można również wykluczyć niektóre z tych użytkowników, wybierając ról, poszczególnych użytkowników lub gości użytkowników usługi Azure AD. Należy pamiętać, że po skonfigurowaniu tych wyłączeń zdefiniowanie założeń zasad nie można wymusić dla tych użytkowników. Wyjątki te zostały skonfigurowane jako lista indywidualnych użytkowników lub za pośrednictwem starsze lokalne grupy zabezpieczeń, a następnie go ogranicza ich widoczność tej listy wykluczeń (użytkownicy mogą nie wiedzą, jego istnienia) i nad nim kontroli administratora IT (użytkownicy mogą dołączać Grupa zabezpieczeń do obejścia zasad). Ponadto użytkownicy, którzy kwalifikowana do wykluczenia, w tym samym czasie może nie jest już potrzebny lub kwalifikować się do jej.

Na początku wykluczenie ma krótką listę użytkowników, którzy obejście zasad. Wraz z upływem czasu coraz więcej użytkowników są wyłączone, a lista rozwoju. W pewnym momencie istnieje potrzeba Przejrzyj listę i upewnij się, że każda z tych użytkowników powinny być nadal wyłączone. Zarządzanie listy z technicznego punktu widzenia może być stosunkowo proste, ale który sprawia, że decyzje biznesowe i jak zrobić się, że wszystkie inspekcji?

Jednak jeśli skonfigurujesz wykluczenie do zasad dostępu warunkowego przy użyciu grupy usługi Azure AD, następnie umożliwia przeglądów dostępu jako kontrolkę wyrównującej dysk widoczności i Zmniejsz liczbę użytkowników, którzy mają wyjątku.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak utworzyć grupę wykluczeń w zasadach dostępu warunkowego

Wykonaj następujące kroki, aby utworzyć nową grupę usługi Azure AD i zasady dostępu warunkowego, które nie ma zastosowania do tej grupy.

### <a name="create-an-exclusion-group"></a>Utwórz grupę wykluczania

1. Zaloguj się do Portalu Azure.

1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **grup**.

1. W górnym menu, kliknij przycisk **Nowa grupa** aby otworzyć okienko grupy.

1. W **typ grupy** listy wybierz **zabezpieczeń**. Określ nazwę i opis.

1. Upewnij się ustawić **członkostwa** typ **przypisane**.

1. Wybierz użytkowników, którzy powinny należeć do tej grupy do wykluczenia, a następnie kliknij przycisk **Utwórz**.

    ![Nowe okienko grupy](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Tworzenie zasad dostępu warunkowego, która nie obejmuje grupy

Teraz możesz utworzyć zasady dostępu warunkowego, która używa tej grupy do wykluczenia.

1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory** a następnie kliknij przycisk **dostępu warunkowego** otworzyć **zasady** bloku.

1. Kliknij przycisk **nowe zasady** otworzyć **New** okienka.

1. Określ nazwę.

1. W obszarze przypisania kliknij **użytkowników i grup**.

1. Na **Include** zaznacz **wszyscy użytkownicy**.

1. Na **wykluczyć** kartę, zaznacz pole wyboru **użytkowników i grup** a następnie kliknij przycisk **wybierz wykluczonych użytkowników**.

1. Wybierz utworzoną grupę wykluczania.

    > [!NOTE]
    > Najlepszym rozwiązaniem jest zalecane jest wykluczone z zasad co najmniej jedno konto administratora, podczas testowania do upewnij się, że nie są zablokowane spoza Twojej dzierżawy.

1. Przejdź do konfigurowania zasad dostępu warunkowego, w zależności od wymagań organizacji.

    ![Wybierz wykluczonych użytkowników](./media/conditional-access-exclusion/select-excluded-users.png)

Teraz obejmuje dwa przykłady, których można użyć przeglądów dostępu do wykluczenia w zasadach dostępu warunkowego do zarządzania.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>Przykład 1: Przegląd dostępu dla użytkowników uzyskujących dostęp do z krajów zablokowane

Załóżmy, że zasady dostępu warunkowego które blokuje dostęp z niektórych krajów. Zawiera grupy, który jest wykluczony z zasad. Poniżej przedstawiono przegląd dostępu zalecane gdzie członkowie grupy są przeglądane.

> [!NOTE]
> Administrator globalny lub roli administratora użytkownika jest wymagana do utworzenia przeglądów dostępu.

1. Przegląd będzie powtarzał co tydzień.

2. Nigdy nie zakończy się w celu upewnij się, że ta grupa wykluczeń jest zachowywana najbardziej aktualne.

3. Wszyscy członkowie tej grupy będą uwzględnione w zakresie przeglądu.

4. Każdy użytkownik będzie musiał własnym potwierdzają, że nadal potrzebują może korzystać z tych krajów zablokowanych, w związku z tym nadal muszą należeć do grupy.

5. Jeśli użytkownik nie odpowie na żądanie przeglądu, będzie zostaną automatycznie usunięte z grupy, a w związku z tym, można nie uzyskać dostępu do dzierżawy podczas podróży w tych krajach.

6. Włącz powiadomienia pocztowe, dzięki czemu użytkownicy są powiadamiani o rozpoczęcia i zakończenia Przegląd dostępu.

    ![Tworzenie przeglądu dostępu](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Przykład 2: Przegląd dostępu dla użytkowników uzyskujących dostęp do przy użyciu starszej wersji uwierzytelniania

Załóżmy, że mają zasady dostępu warunkowego, które blokuje dostęp użytkowników przy użyciu starszej wersji uwierzytelniania i starsze wersje klienta. Zawiera grupy, który jest wykluczony z zasad. Poniżej przedstawiono przegląd dostępu zalecane gdzie członkowie grupy są przeglądane.

1. Przegląd ten musi być cykliczne przeglądu.

2. Wszyscy w grupie musi być analizowane.

3. Może on zostać skonfigurowany do tworzenia listy właścicieli jednostki biznesowej jako wybranych recenzentów.

4. Automatycznie Zastosuj wyniki i usuwania użytkowników, którzy nie zostały zatwierdzone aby kontynuować korzystanie z metod uwierzytelniania w starszej wersji.

5. Może być korzystne włączyć zalecenia dotyczące, dzięki czemu recenzenci dużych grup może łatwo decyzje dotyczące ich.

6. Włącz powiadomienia pocztowe, dzięki czemu użytkownicy są powiadamiani o rozpoczęcia i zakończenia Przegląd dostępu.

    ![Tworzenie przeglądu dostępu](./media/conditional-access-exclusion/create-access-review-2.png)

**Porada Pro**: Jeśli masz wiele grup wykluczenia i w związku z tym należy utworzyć wiele przeglądów dostępu, w efekcie powstał interfejsu API w punkcie końcowym w wersji beta programu Microsoft Graph, która pozwala na tworzenie i zarządzanie nimi programistycznie. Aby rozpocząć pracę, zobacz [przeglądy dostępu w usłudze Azure AD, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) i [przykład pobierania dostępu usługi Azure AD, przeglądy za pomocą programu Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Dzienniki inspekcji i wyników przeglądu dostępu

Teraz, gdy masz wszystko, co w miejscu, grupy, zasady dostępu warunkowego i przeglądy dostępu, nadszedł czas na monitorowanie i śledzenie wyniki tych przeglądów.

1. W witrynie Azure portal Otwórz **przeglądów dostępu** bloku.

1. Otwórz kontroli i program, który został utworzony do zarządzania w grupie wykluczenia.

1. Kliknij przycisk **wyniki** aby zobaczyć, kto został zatwierdzony, aby pozostać na liście i który został usunięty.

    ![Przeglądy dostępu dotyczące wyników](./media/conditional-access-exclusion/access-reviews-results.png)

1. Następnie kliknij przycisk **dzienniki inspekcji** się akcji, które zostały wykonane w trakcie przeglądu.

    ![Przeglądy dostępu dotyczące dzienników inspekcji](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako IT administrator możesz wiedzieć, że zarządzanie grupami wykluczenie do swoich zasad czasami jest nieuniknione. Jednak obsługa tych grup, przeglądanie ich w regularnych odstępach czasu przez właściciela firmy lub użytkowników, samodzielnie i inspekcji można te zmiany ułatwiono dzięki usłudze Azure AD access dokonuje przeglądu.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
