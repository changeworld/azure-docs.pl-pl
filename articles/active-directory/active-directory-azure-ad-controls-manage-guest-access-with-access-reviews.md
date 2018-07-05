---
title: Zarządzanie dostępem gości za pomocą usługi Azure AD przeglądów dostępu | Dokumentacja firmy Microsoft
description: Zarządzaj użytkowników-gości jako członkowie danej grupy lub przypisanych do aplikacji za pomocą przeglądów dostępu w usłudze Azure Active Directory
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
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 71569da662c5d5e5ef5ad6c7d9783a69e307f2fa
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441186"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem gości za pomocą usługi Azure AD przeglądów dostępu


Za pomocą usługi Azure Active Directory (Azure AD), możesz łatwo włączyć współpracy między granicami organizacji za pomocą [funkcji usługi Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Użytkowników-gości z innych dzierżaw może być [zaproszonych przez administratorów](active-directory-b2b-admin-add-users.md) lub [innym użytkownikom](active-directory-b2b-how-it-works.md). Ta funkcja dotyczy także tożsamości społecznościowych, takich jak konta Microsoft.

Możesz również można łatwo upewnij się, że użytkownicy-goście mają odpowiedni dostęp. Możesz poprosić gości, samodzielnie lub osobę podejmującą do wzięcia udziału w przeglądzie dostępu i ponowne certyfikowanie (lub potwierdzające) do dostępu gości. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i usunięcie dostępu dla gości, którzy już go nie potrzebują.

> [!NOTE]
> Ten dokument koncentruje się na przeglądu dostępu użytkowników-gości. Jeśli chcesz przeprowadzić przegląd dostępu wszystkich użytkowników, nie tylko gości, zobacz [zarządzanie dostępem użytkowników za pomocą przeglądów dostępu](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak administrator globalny, zobacz [Rozpoczynanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Wymagania wstępne 


Przeglądy dostępu są oferowane wraz z usługą Azure AD w wersji Premium P2, która jest udostępniana z pakietem Microsoft Enterprise Mobility + Security E5. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](active-directory-editions.md). Użytkownicy, którzy chcą korzystać z tej funkcji, w tym tworzyć lub wypełniać przeglądy albo potwierdzać swój dostęp, muszą mieć licencję. 

Jeśli planujesz poprosić użytkowników-gości, aby dokonać przeglądu własnego dostępu, przeczytaj dotyczącymi licencjonowania użytkownika gościa. Aby uzyskać więcej informacji, zobacz [Licencjonowanie współpracy B2B usługi Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Tworzenie i wykonywanie przeglądu dostępu dla gości

Po pierwsze Włącz wyświetlanie przeglądów dostępu na panelach dostępu recenzentów. Korzystając z uprawnień administratora globalnego lub administratora kont użytkowników, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Usługa Azure AD umożliwia kilka scenariuszy dotyczących przeglądania użytkowników-gości.

Wybierz jedną z następujących czynności:

 - Grupa w usłudze Azure AD, który ma co najmniej jeden gości, jako elementy członkowskie.
 - Aplikację połączoną z usługą Azure AD, który ma co najmniej jednego użytkownika gościa do niej przypisany. 

Następnie można zdecydować, czy poprosić każdego gościa, aby dokonać przeglądu własnego dostępu lub zadać co najmniej jednego użytkownika, aby zapoznać się z każdym gości.

 Scenariusze te zostały uwzględnione w poniższych sekcjach.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Poproś gości, aby zapoznać się z ich członkostwa w grupie

Przeglądy dostępu można użyć, aby upewnić się, że użytkownicy, którzy zostały zaproszone i dodane do grupy w dalszym ciągu muszą mieć dostęp. Możesz łatwo poprosić gości, aby zapoznać się z ich członkostwa w tej grupie.

1. Uruchamianie przeglądu dostępu dla grupy, wybierz pozycję Przegląd obejmujący tylko członków użytkowników-gości i elementy członkowskie zapoznaj się. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś każdego gościa, aby zapoznać się z ich członkostwa. Domyślnie każdy gościa, który zaakceptował zaproszenia otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do przeglądu dostępu. Usługa Azure AD ma instrukcje dla gości na temat sposobu [przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

4. Oprócz użytkowników, którzy odmowa własne potrzeby opinię dotyczącą przedłużenia dostępu można również usunąć użytkowników, którzy nie odpowiedział. Odpowiada innym użytkownikom potencjalnie już odbierać poczty e-mail.

5. Jeśli grupa nie jest używana do zarządzania dostępem, możesz również usunąć użytkowników, którzy nie zostały wybrane do wzięcia udziału w przeglądzie, ponieważ ich nie zaakceptował zaproszenia. Nie akceptuje może wskazywać, że adres e-mail zaproszony użytkownik miał błąd pisowni. Jeśli grupa jest używana jako listy dystrybucyjnej, być może niektórych użytkowników-gości nie zostały wybrane do udziału, ponieważ są one skontaktuj się z pomocą obiektów.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Poproś sponsora, aby zapoznać się z gościa członkostwa w grupie

Możesz poprosić sponsora, takich jak właścicielem grupy, aby zapoznać się z gościa potrzebę ciągłego członkostwa w grupie.

1. Uruchamianie przeglądu dostępu dla grupy, wybierz pozycję Przegląd, aby uwzględnić tylko członków użytkowników-gości. Następnie należy określić co najmniej jednego recenzenta. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Poproś gości, aby dokonać przeglądu własnego dostępu do aplikacji

Przeglądy dostępu można użyć, aby upewnić się, że użytkownicy, którzy otrzymali zaproszenie dla określonej aplikacji w dalszym ciągu muszą mieć dostęp. Możesz łatwo poprosić czy goście samodzielnie przejrzeć swoje własne muszą uzyskać dostęp do.

1. Uruchamianie przeglądu dostępu dla aplikacji, wybierz przeglądu, aby uwzględnić tylko gości i użytkownicy dokonują przeglądu własnego dostępu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś każdego gościa, aby dokonać przeglądu własnego dostępu do aplikacji. Domyślnie każdy gościa, który zaakceptował zaproszenia otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do przeglądu dostępu w panelu dostępu do Twojej organizacji. Usługa Azure AD ma instrukcje dla gości na temat sposobu [przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

4. Oprócz użytkowników, którzy odmowa własne potrzebować opinię dotyczącą przedłużenia dostępu, możesz również usunąć użytkowników-gości, którzy nie odpowiedział. Odpowiada innym użytkownikom potencjalnie już odbierać poczty e-mail. Możesz również usunąć użytkowników-gości, którzy nie zostały wybrane do udziału, zwłaszcza, jeśli ich nie zostały ostatnio zaproszenie. Ci użytkownicy nie zaakceptował zaproszenia, a tym samym nie mieć dostępu do aplikacji. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Poproś sponsora, aby przeprowadzić przegląd dostępu gości do aplikacji

Możesz poprosić sponsora, na przykład właściciela aplikacji, aby zapoznać się z konieczności gościa opinię dotyczącą przedłużenia dostępu do aplikacji.

1. Uruchamianie przeglądu dostępu dla aplikacji, wybierz przeglądu, aby uwzględnić tylko gości. Następnie należy określić co najmniej jeden użytkownik recenzentów. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Poproś gości, aby przejrzeć ich wymagań, aby uzyskać dostęp, ogólnie rzecz biorąc

W niektórych organizacjach gości może nie być świadome ich członkostwa w grupach.

> [!NOTE]
> Wcześniejszą wersję witryny Azure portal nie zezwalać na dostęp administracyjny przez użytkowników z UserType gościa. W niektórych przypadkach administrator w katalogu mogą wartość została zmieniona gościa UserType do członka za pomocą programu PowerShell. Jeśli ta zmiana wystąpił wcześniej w katalogu, poprzednie zapytanie może nie zawierać wszyscy użytkownicy-goście, którzy w przeszłości ma administracyjne prawa dostępu. W takim przypadku należy zmienić UserType gościa lub ręcznie dołączyć gościa członkostwa w grupie.

1. Utwórz grupę zabezpieczeń w usłudze Azure AD z gości jako elementy członkowskie, jeśli odpowiednia grupa nie istnieje. Można na przykład utworzyć grupę, członkostwo ręczne obsługiwanych gości. Alternatywnie można utworzyć grupę dynamiczną o nazwie takich jak "Gości Contoso" użytkowników w dzierżawie Contoso, którzy mają wartość atrybutu UserType gościa.  W celu zwiększenia wydajności upewnij się, grupa jest głównie gości — nie wybieraj grupy zawierającej użytkowników, którzy nie muszą być sprawdzone.

2. Uruchamianie przeglądu dostępu dla tej grupy, wybierz recenzentów samych należeć. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

3. Poproś każdego gościa, aby zapoznać się z ich członkostwa. Domyślnie każdy gościa, który zaakceptował zaproszenia otrzymuje wiadomość e-mail z usługi Azure AD z linkiem do przeglądu dostępu w panelu dostępu do Twojej organizacji. Usługa Azure AD ma instrukcje dla gości na temat sposobu [przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).  Te goście, którzy nie zaakceptował zaproszenia ich pojawi się w wyników przeglądu jako "Nie jest powiadamiany".

4. Po recenzentów opinii, Zatrzymaj Przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

5. Usuń dostęp gościa dla gości, którzy zostały odrzucone, nie została ukończona przeglądu lub poprzednio nie zaakceptował zaproszenia. Jeśli niektóre z gości są kontakty, które zostały wybrane do wzięcia udziału w przeglądzie, lub ich wcześniej nie zaakceptował zaproszenia, można wyłączyć kont za pomocą witryny Azure portal lub programu PowerShell. Jeśli gościa nie jest już wymagany jest dostęp, nie jest kontakt można usunąć swoich obiektów z katalogiem, za pomocą witryny Azure portal lub programu PowerShell, można usunąć obiektu użytkownika gościa.

## <a name="next-steps"></a>Kolejne kroki

[Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](active-directory-azure-ad-controls-create-access-review.md)







