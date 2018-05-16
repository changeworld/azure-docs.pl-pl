---
title: Zarządzaj dostępem gościa za pomocą usługi Azure AD dostęp przeglądami | Dokumentacja firmy Microsoft
description: Zarządzaj jako członków grupy Goście lub przypisane do aplikacji przy użyciu usługi Azure Active Directory dostępu przeglądy
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 2ac28c278b9ac832f05cdb19a48ea576526104b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Zarządzaj dostępem gościa za pomocą usługi Azure AD dostęp do przeglądu


Usłudze Azure Active Directory (Azure AD), można łatwo włączyć współpracy między granicami organizacji za pomocą [funkcji B2B usługi Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Goście od pozostałych dzierżawców może być [zaproszeni przez administratorów](active-directory-b2b-admin-add-users.md) lub [innym użytkownikom](active-directory-b2b-how-it-works.md). Ta funkcja ma również zastosowanie do tożsamości społecznościowych, takich jak konta Microsoft.

Możesz również można łatwo upewnij się, że goście mają odpowiedni dostęp. Poproś gości właścicielami ani podejmującą brać udziału w Przegląd dostępu i recertify (lub potwierdzające) do dostępu gości. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu działania przeglądu dostępu można wprowadzić zmiany i spowodować usunięcie dostępu dla gości, którzy nie jest już potrzebny.

> [!NOTE]
> Ten dokument koncentruje się na weryfikacji dostępu użytkowników gościa. Jeśli chcesz przejrzeć dostęp do wszystkich użytkowników, nie tylko gości, zobacz [zarządzanie dostępem użytkowników z przeglądami dostępu](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Jeśli chcesz przejrzeć użytkowników członkostwem ról administracyjnych, takich jak administrator globalny, zobacz [rozpocząć Przegląd dostępu w usłudze Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Wymagania wstępne 

Przeglądy dostępu są oferowane wraz z usługą Azure AD w wersji Premium P2, która jest udostępniana z pakietem Microsoft Enterprise Mobility + Security E5. Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](active-directory-whatis.md). Użytkownicy, który chcą korzystać z tej funkcji w celu tworzenia lub stosowania przeglądu albo uzyskania dostępu do przeglądu, muszą mieć licencję.

Jeśli planujesz poproś gości, aby przejrzeć swoje własne dostępu, przeczytaj informacje o licencji użytkownika gościa. Aby uzyskać więcej informacji, zobacz [licencjonowania współpracy B2B usługi Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Tworzenie i wykonywanie przeglądu dostępu dla gości

Najpierw włączyć przeglądami dostępu na panele dostępu osoba dokonująca przeglądu. Korzystając z uprawnień administratora globalnego, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Usługi Azure AD umożliwia kilka scenariuszy weryfikacji gości.

Wybierz jedną z następujących czynności:

 - Grupy w usłudze Azure AD, która ma co najmniej jeden gości, jako elementy członkowskie.
 - Aplikacja jest połączone z usługą Azure AD, który ma przypisane do niej co najmniej jednego użytkownika gościa. 

Następnie można zdecydować, czy poprosić każdego gościa, aby przejrzeć swoje własne dostępu lub zadać co najmniej jednego użytkownika, aby przejrzeć co gości.

 W poniższych sekcjach opisano te scenariusze.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Poproś gości, aby sprawdzić członkostwa w grupie

Przeglądami dostępu służy do sprawdzenia, czy użytkownicy, którzy zostały zaproszenie i dodawane do grupy w dalszym ciągu będą potrzebować dostępu. Możesz łatwo poprosić gości, aby sprawdzić członkostwa w grupie.

1. Aby rozpocząć Przegląd dostępu dla grupy, wybierz przeglądu tylko członkami użytkownika gościa i elementów członkowskich zapoznaj się. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś każdego gościa, aby przejrzeć ich członkostwa. Domyślnie każdy gościa, który zaakceptował zaproszenie otrzymuje wiadomość e-mail z usługi Azure AD z łączem do przeglądu dostępu. Usługa Azure AD ma instrukcje dla gości na temat [weryfikować ich dostęp](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

4. Oprócz użytkowników, którzy odmowa własnych potrzebę nieprzerwanego dostępu należy usunąć użytkowników, którzy nie odpowiedział. Odpowiada użytkownicy inni niż użytkownicy potencjalnie już odbierać poczty e-mail.

5. Jeśli grupa nie jest używana do zarządzania dostępem, możesz również usunąć użytkowników, którzy nie zostały wybrane do udziału w przeglądzie, ponieważ one nie zaakceptował ich zaproszenia. Nie akceptuje może wskazywać, że adres e-mail użytkownika zaproszonych miał Literówka. Jeśli grupa jest używana jako listę dystrybucyjną, prawdopodobnie w przypadku niektórych użytkowników gościa nie zostały wybrane do udziału, ponieważ są one skontaktuj się z pomocą obiektów.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Poproś sponsor, aby przejrzeć gościa członkostwo w grupie

Poproś sponsor, takich jak właściciel grupy, aby przejrzeć gościa na potrzeby ciągłego członkostwo w grupie.

1. Aby rozpocząć Przegląd dostępu dla grupy, wybierz przeglądu, aby uwzględnić tylko członkami użytkownika gościa. Następnie określ jeden lub więcej recenzentów. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Poproś gości, aby przejrzeć dostępu do aplikacji

Przeglądy dostępu umożliwia upewnij się, że użytkownicy, którzy zostali zaproszeni dla określonej aplikacji w dalszym ciągu będą potrzebować dostępu. Można łatwo poprosić gości się do przeglądania ich własnych potrzebował dostępu.

1. Aby rozpocząć Przegląd dostępu dla aplikacji, wybierz przeglądu tylko gości i że użytkownicy przeglądu własnych dostępu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś każdego gościa, aby przejrzeć dostępu do aplikacji. Domyślnie każdy gościa, który zaakceptował zaproszenie otrzymuje wiadomość e-mail z usługi Azure AD z łączem do przeglądu dostępu w panelu dostępu w organizacji. Usługa Azure AD ma instrukcje dla gości na temat [weryfikować ich dostęp](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

4. Oprócz użytkowników, którzy odmowa własne potrzeby nieprzerwanego dostępu, można również usunąć gościa użytkowników, którzy nie odpowiedział. Odpowiada użytkownicy inni niż użytkownicy potencjalnie już odbierać poczty e-mail. Można usunąć również gości, które nie zostały wybrane do udziału, zwłaszcza, jeśli nie zostały one ostatnio zaproszenie. Użytkowników nie zaakceptować zaproszenie ich i dlatego nie mają dostępu do aplikacji. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Poproś sponsor, aby przejrzeć gościa dostęp do aplikacji

Poproś sponsor, takich jak właściciel aplikacji, aby przejrzeć gościa potrzebę nieprzerwanego dostępu do aplikacji.

1. Aby rozpocząć Przegląd dostępu dla aplikacji, wybierz przeglądu, aby uwzględnić tylko gości. Następnie należy określić co najmniej jednego użytkownika jako osoby dokonujące przeglądu. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie otrzymają oni wiadomość e-mail z usługi Azure AD z linkiem do panelu dostępu, który umożliwia [przeprowadzenie przeglądu dostępu](active-directory-azure-ad-controls-perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Poproś gości, aby przejrzeć ich potrzebę dostępu, ogólnie

W niektórych organizacjach goście mogą nie być świadomy ich członkostwa w grupach.

> [!NOTE]
> Wcześniejszych wersji portalu Azure nie zezwala na dostęp administracyjny przez użytkowników z UserType gościa. W niektórych przypadkach administrator w katalogu może zmienić wartość UserType gościa do elementu członkowskiego przy użyciu programu PowerShell. Jeśli ta zmiana wystąpił wcześniej w katalogu, poprzednie zapytanie może nie zawierać wszystkich użytkowników gościa, którzy w przeszłości miał uprawnień administratora. W takim przypadku należy zmienić UserType gościa albo ręcznie dołączyć Gość w członkostwie grupy.

1. Utwórz grupę zabezpieczeń w usłudze Azure AD z gości, jako elementy członkowskie, jeśli odpowiednie grupy już nie istnieje. Na przykład można utworzyć grupę z członkostwem ręcznie utrzymywana gości. Alternatywnie można utworzyć grupę dynamicznego o nazwie takich jak "Gości Contoso" dla użytkowników w dzierżawie Contoso, którzy mają wartość atrybut UserType gościa.  W celu zwiększenia wydajności upewnij się, grupa jest głównie gości — nie wybierz grupy, która ma użytkowników, którzy nie wymagają przejrzenia.

2. Aby rozpocząć Przegląd dostępu dla tej grupy, wybierz recenzentów należeć do siebie. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu](active-directory-azure-ad-controls-create-access-review.md).

3. Poproś każdego gościa, aby przejrzeć ich członkostwa. Domyślnie każdy gościa, który zaakceptował zaproszenie otrzymuje wiadomość e-mail z usługi Azure AD z łączem do przeglądu dostępu w panelu dostępu w organizacji. Usługa Azure AD ma instrukcje dla gości na temat [weryfikować ich dostęp](active-directory-azure-ad-controls-perform-access-review.md).  Te gości, którzy nie zaakceptował ich zaproszenia pojawi się w wynikach przeglądu "Nie jest powiadamiany".

4. Po recenzentów dać danych wejściowych, Zatrzymaj Przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Kończenie przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md).

5. Usuń gościa dostępu dla gości, które zostały odrzucone, nie została ukończona przeglądu lub wcześniej nie zaakceptował ich zaproszenia. Jeśli niektóre gości są kontakty, które zostały wybrane do udziału w przeglądzie lub wcześniej zaakceptował zaproszenia, ich kont można wyłączyć za pomocą portalu Azure lub programu PowerShell. Gość nie musi mieć dostęp, nie jest kontakt można usunąć ich obiektu użytkownika z katalogu przy użyciu portalu Azure lub programu PowerShell, aby usunąć obiekt użytkownika gościa.

## <a name="next-steps"></a>Kolejne kroki

[Create an access review for members of a group or access to an application (Tworzenie przeglądu dostępu dla członków grupy lub na potrzeby dostępu do aplikacji)](active-directory-azure-ad-controls-create-access-review.md)







