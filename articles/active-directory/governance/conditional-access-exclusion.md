---
title: Korzystanie z przeglądów dostępu do zarządzania użytkownikami wykluczonymi z zasad dostępu warunkowego — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak za pomocą przeglądów dostępu w usłudze Azure Active Directory (Azure AD) zarządzać użytkownikami, którzy zostali wykluczeni z zasad dostępu warunkowego
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
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499912"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Korzystanie z przeglądów dostępu usługi Azure AD do zarządzania użytkownikami wykluczonymi z zasad dostępu warunkowego

W idealnym świecie wszyscy użytkownicy będą przestrzegać zasad dostępu w celu zabezpieczenia dostępu do zasobów organizacji. Czasami jednak istnieją przypadki biznesowe, które wymagają wprowadzenia wyjątków. W tym artykule opisano niektóre przykłady sytuacji, w których mogą być wymagane wykluczenia i jak administrator IT może zarządzać tym zadaniem, unikać nadzoru wyjątków zasad i zapewniać audytorom dowód, że te wyjątki są regularnie analizowane przy użyciu platformy Azure Przeglądy dostępu Active Directory (Azure AD).

> [!NOTE]
> Do korzystania z przeglądów dostępu do usługi Azure AD wymagana jest prawidłowa Azure AD — wersja Premium P2, Enterprise Mobility + Security E5 lub wersja próbna. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Dlaczego należy wykluczyć użytkowników z zasad?

Jako administrator IT możesz użyć [dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) , aby wymagać od użytkowników uwierzytelniania przy użyciu usługi MFA lub logowania się z zaufanej sieci lub urządzenia. Podczas planowania wdrożenia należy sprawdzić, że wszyscy użytkownicy nie mogą spełnić niektórych z tych wymagań. Na przykład istnieją użytkownicy, którzy pracują ze zdalnego biura, który nie jest częścią sieci wewnętrznej lub ma dyrektora, który używa starego telefonu, który nie jest obsługiwany. Firma wymaga, aby Ci użytkownicy mogli się logować i wykonywać swoje zadania, dlatego są wykluczeni z zasad dostępu warunkowego.

Innym przykładem może być użycie [nazwanych lokalizacji](../conditional-access/location-condition.md) w dostępie warunkowym w celu skonfigurowania zestawu liczb i regionów, z których użytkownicy nie mają mieć dostępu do swojej dzierżawy.

![Nazwane lokalizacje w dostępie warunkowym](./media/conditional-access-exclusion/named-locations.png)

Jednak w niektórych przypadkach użytkownicy mogą mieć uzasadniony powód, aby zalogować się z tych zablokowanych krajów/regionów. Na przykład użytkownicy mogą podróżować ze względów służbowych lub osobistych. W tym przykładzie zasady dostępu warunkowego, które blokują te kraje/regiony, mogą mieć dedykowaną grupę zabezpieczeń chmury dla użytkowników, którzy są wykluczeni z zasad. Użytkownicy, którzy potrzebują dostępu w podróży, mogą dodać siebie do grupy przy użyciu samoobsługowego zarządzania grupami w usłudze [Azure AD](../users-groups-roles/groups-self-service-management.md).

Innym przykładem mogą być zasady dostępu warunkowego, które [blokują starsze uwierzytelnianie dla większości użytkowników](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Firma Microsoft zdecydowanie zaleca zablokowanie użycia starszych protokołów w dzierżawie w celu usprawnienia stan zabezpieczeń. Jednak w przypadku niektórych użytkowników, którzy absolutnie potrzebują używać starszych metod uwierzytelniania do uzyskiwania dostępu do zasobów za pośrednictwem klientów pakietu Office 2010 lub protokołu IMAP/wyskakujących okienek, można wykluczyć tych użytkowników z zasad, które blokują starsze metody uwierzytelniania.

## <a name="why-are-exclusions-challenging"></a>Dlaczego wyjątki są trudne?

W usłudze Azure AD można ograniczyć zakres zasad dostępu warunkowego do zestawu użytkowników. Niektórych z tych użytkowników można także wykluczyć, wybierając pozycję Role usługi Azure AD, indywidualni użytkownicy lub Goście użytkowników. Należy pamiętać, że po skonfigurowaniu tych wykluczeń nie można wymusić przeznaczenie zasad dla tych użytkowników. Jeśli te wykluczenia zostały skonfigurowane jako lista indywidualnych użytkowników lub w starszej lokalnej grupie zabezpieczeń, ograniczają widoczność tej listy wykluczeń (użytkownicy mogą nie wiedzieć o jej istnieniu) oraz kontrolę nad nią przez administratora IT (użytkownicy mogą dołączać do Grupa zabezpieczeń, do której mają zostać przekazane zasady. Ponadto użytkownicy, którzy mają uprawnienia do wykluczania w tym samym czasie, mogą już nie potrzebować lub być uprawnieni do korzystania z niego.

Na początku wykluczenia występuje krótka lista użytkowników, którzy pomijają zasady. Wraz z upływem czasu więcej niż jeden użytkownik jest wykluczony, a lista rośnie. W pewnym momencie istnieje potrzeba przejrzenia listy i potwierdzenia, że każdy z tych użytkowników nadal powinien być wykluczony. Zarządzanie listą z punktu widzenia technicznego może być stosunkowo proste, ale kto podejmuje decyzje biznesowe i jak upewnić się, że jest on dostępny do przeprowadzenia inspekcji?

Jednak w przypadku skonfigurowania wykluczenia dla zasad dostępu warunkowego przy użyciu grupy usługi Azure AD, można użyć przeglądów dostępu jako kontroli kompensowania, aby zwiększyć widoczność dysku, i zmniejszyć liczbę użytkowników, którzy mają wyjątek.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Jak utworzyć grupę wykluczeń w zasadach dostępu warunkowego

Wykonaj następujące kroki, aby utworzyć nową grupę usługi Azure AD i zasady dostępu warunkowego, które nie mają zastosowania do tej grupy.

### <a name="create-an-exclusion-group"></a>Tworzenie grupy wykluczeń

1. Zaloguj się do Portalu Azure.

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **grupy**.

1. W górnym menu kliknij pozycję **Nowa grupa** , aby otworzyć okienko grupy.

1. Z listy **Typ grupy** wybierz pozycję **zabezpieczenia**. Określ nazwę i opis.

1. Upewnij się, że typ **członkostwa** jest ustawiony na wartość **przypisane**.

1. Wybierz użytkowników, którzy powinni być częścią tej grupy wykluczeń, a następnie kliknij przycisk **Utwórz**.

    ![Okienko nowej grupy w Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Tworzenie zasad dostępu warunkowego, które nie wykluczają grupy

Teraz można utworzyć zasady dostępu warunkowego, które korzystają z tej grupy wykluczeń.

1. W lewym okienku nawigacji kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **dostęp warunkowy** , aby otworzyć blok **zasady** .

1. Kliknij pozycję **nowe zasady** , aby otworzyć **nowe** okienko.

1. Określ nazwę.

1. W obszarze przypisania kliknij pozycję **Użytkownicy i grupy**.

1. Na karcie **Dołącz** wybierz pozycję **Wszyscy użytkownicy**.

1. Na karcie **Wyklucz** , Dodaj znacznik wyboru do opcji **Użytkownicy i grupy** , a następnie kliknij pozycję **Wybierz wykluczonych użytkowników**.

1. Wybierz utworzoną grupę wykluczeń.

    > [!NOTE]
    > Najlepszym rozwiązaniem jest wykluczenie co najmniej jednego konta administratora z zasad podczas testowania, aby upewnić się, że nie masz zablokowanej dzierżawy.

1. Kontynuuj Konfigurowanie zasad dostępu warunkowego zgodnie z wymaganiami organizacji.

    ![Wybieranie okienka wykluczonych użytkowników w dostępie warunkowym](./media/conditional-access-exclusion/select-excluded-users.png)

Zapoznajmy się z dwoma przykładami, które umożliwiają zarządzanie wykluczeniami w zasadach dostępu warunkowego.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Przykład 1: Przegląd dostępu dla użytkowników uzyskujących dostęp z zablokowanych krajów/regionów

Załóżmy, że masz zasady dostępu warunkowego, które blokują dostęp z określonych krajów/regionów. Obejmuje grupę, która jest wykluczona z zasad. Poniżej znajduje się Zalecany przegląd dostępu, w którym są przeglądane członkowie grupy.

> [!NOTE]
> Aby można było tworzyć przeglądy dostępu, wymagana jest rola administratora globalnego lub administratora użytkowników.

1. Przegląd będzie się powtarzał co tydzień.

2. Nie zostanie zakończona, aby upewnić się, że ta grupa wykluczeń jest już najbardziej aktualna.

3. Wszystkie elementy członkowskie tej grupy będą znajdować się w zakresie przeglądu.

4. Każdy użytkownik będzie musiał zaświadczać, że nadal muszą mieć dostęp z tych zablokowanych krajów/regionów, dlatego nadal musi być członkiem grupy.

5. Jeśli użytkownik nie odpowie na żądanie przejrzenia, zostanie on automatycznie usunięty z grupy i w związku z tym nie będzie mógł już uzyskiwać dostępu do dzierżawy podczas podróży do tych krajów/regionów.

6. Włącz powiadomienia e-mail, aby użytkownicy byli powiadamiani o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Przykład 2: Przegląd dostępu dla użytkowników korzystających z starszego uwierzytelniania

Załóżmy, że masz zasady dostępu warunkowego, które blokują dostęp dla użytkowników przy użyciu starszych wersji uwierzytelniania i starszych. Obejmuje grupę, która jest wykluczona z zasad. Poniżej znajduje się Zalecany przegląd dostępu, w którym są przeglądane członkowie grupy.

1. Ten przegląd musi być cyklicznym przeglądem.

2. Należy przejrzeć wszystkie osoby w grupie.

3. Można ją skonfigurować tak, aby wystawiali właścicieli jednostek firmy jako wybranych recenzentów.

4. Stosuj autouwierzytelnianie i Usuń użytkowników, którzy nie zostali zatwierdzeni, aby nadal używać starszych metod uwierzytelniania.

5. Warto włączyć zalecenia, aby recenzenci dużych grup mogli w łatwy sposób podejmować decyzje.

6. Włącz powiadomienia e-mail, aby użytkownicy byli powiadamiani o rozpoczęciu i zakończeniu przeglądu dostępu.

    ![Tworzenie okienka przeglądu dostępu na przykład 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Porada pakietu Pro**: Jeśli masz wiele grup wykluczeń i w związku z tym trzeba utworzyć wiele przeglądów dostępu, w punkcie końcowym Microsoft Graph wersji beta jest teraz dostępny interfejs API, który umożliwia programowe tworzenie i zarządzanie nimi. Aby rozpocząć, zobacz informacje o [interfejsie API przeglądów usługi Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) i [przykład pobierania przeglądów dostępu do usługi Azure AD za pośrednictwem Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Wyniki przeglądu dostępu i dzienniki inspekcji

Teraz, gdy masz wszystko na bieżąco, grupy, zasady dostępu warunkowego i przeglądy dostępu, jest czas na monitorowanie i śledzenie wyników tych przeglądów.

1. W Azure Portal Otwórz blok przeglądy **dostępu** .

1. Otwórz formant i program utworzony w celu zarządzania grupą wykluczeń.

1. Kliknij pozycję **wyniki** , aby zobaczyć, kto został zatwierdzony do pozostawania na liście i kto został usunięty.

    ![Wyniki przeglądów dostępu pokazują, kto został zatwierdzony](./media/conditional-access-exclusion/access-reviews-results.png)

1. Następnie kliknij pozycję **dzienniki inspekcji** , aby wyświetlić akcje, które zostały wykonane podczas tego przeglądu.

    ![Przeglądy inspekcji dostępu Inspekcja listy akcje](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Jako administrator IT wiesz, że zarządzanie grupami wykluczeń do zasad jest czasami nieuniknione. Jednak utrzymywanie tych grup, przeglądanie ich regularnie przez właściciela firmy lub samych użytkowników, a także przeprowadzanie inspekcji tych zmian w celu ułatwienia przeglądom dostępu do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)
- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)
