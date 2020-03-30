---
title: Omówienie uwierzytelniania usługi Azure Active Directory
description: Dowiedz się więcej o różnych metodach uwierzytelniania i funkcjach zabezpieczeń dla logowania użytkowników za pomocą usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261266"
---
# <a name="what-is-azure-active-directory-authentication"></a>Co to jest uwierzytelnianie usługi Azure Active Directory?

Jedną z głównych cech platformy tożsamości jest weryfikowanie lub *uwierzytelnienie*poświadczeń, gdy użytkownik loguje się do urządzenia, aplikacji lub usługi. W usłudze Azure Active Directory (Azure AD) uwierzytelnianie obejmuje więcej niż tylko weryfikację nazwy użytkownika i hasła. Aby zwiększyć bezpieczeństwo i zmniejszyć potrzebę pomocy technicznej, uwierzytelnianie usługi Azure AD zawiera następujące składniki:

* Samoobsługowe resetowanie haseł
* Azure Multi-Factor Authentication
* Integracja hybrydowa w celu zapisania zmian hasła z powrotem do środowiska lokalnego
* Integracja hybrydowa w celu wymuszenia zasad ochrony hasłem w środowisku lokalnym
* Uwierzytelnianie bez hasła

## <a name="improve-the-end-user-experience"></a>Poprawa środowiska użytkownika końcowego

Usługa Azure AD pomaga chronić tożsamość użytkownika i uprościć jego środowisko logowania. Funkcje takie jak samoobsługowe resetowanie hasła umożliwiają użytkownikom aktualizowanie lub zmienianie haseł za pomocą przeglądarki internetowej z dowolnego urządzenia. Ta funkcja jest szczególnie przydatna, gdy użytkownik zapomniał hasła lub konto jest zablokowane. Nie czekając na pomoc techniczną lub administratora, użytkownik może odblokować się i kontynuować pracę.

Uwierzytelnianie wieloskładnikowe platformy Azure umożliwia użytkownikom wybranie dodatkowej formy uwierzytelniania podczas logowania, takiej jak połączenie telefoniczne lub powiadomienie o aplikacji mobilnej. Ta możliwość zmniejsza wymagania dla pojedynczej, stałej formy uwierzytelniania pomocniczego, takiego jak token sprzętowy. Jeśli użytkownik nie ma obecnie jednej formy dodatkowego uwierzytelniania, może wybrać inną metodę i kontynuować pracę.

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

Uwierzytelnianie bez hasła eliminuje konieczność tworzenia i zapamiętywania bezpiecznego hasła przez użytkownika. Funkcje, takie jak Windows Hello dla firm lub klucze zabezpieczeń FIDO2, umożliwiają użytkownikom logowanie się do urządzenia lub aplikacji bez hasła. Ta możliwość może zmniejszyć złożoność zarządzania hasłami w różnych środowiskach.

## <a name="self-service-password-reset"></a>Samoobsługowe resetowanie haseł

Samoobsługowe resetowanie hasła daje użytkownikom możliwość zmiany lub zresetowania hasła bez zaangażowania administratora lub pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni hasła, może wykonać monity, aby odblokować się i wrócić do pracy. Ta funkcja zmniejsza liczbę połączeń pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się do urządzenia lub aplikacji.

Samoobsługowe resetowanie hasła działa w następujących scenariuszach:

* **Zmiana hasła —** gdy użytkownik zna swoje hasło, ale chce go zmienić na coś nowego.
* **Resetowanie hasła —** gdy użytkownik nie może się zalogować, na przykład gdy zapomniał hasła i chcesz zresetować swoje hasło.
* **Odblokowanie konta -** gdy użytkownik nie może się zalogować, ponieważ jego konto jest zablokowane i chce odblokować swoje konto.

Gdy użytkownik aktualizuje lub resetuje swoje hasło przy użyciu samoobsługowego resetowania hasła, hasło to można również zapisać z powrotem w lokalnym środowisku usługi Active Directory. Stornowania hasła zapewnia, że użytkownik może natychmiast używać swoich zaktualizowanych poświadczeń z urządzeń lokalnych i aplikacji.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe to proces, w którym użytkownik jest monitowany podczas procesu logowania o dodatkową formę identyfikacji, taką jak wprowadzenie kodu na telefonie komórkowym lub dostarczenie skanowania odcisków palców.

Jeśli używasz tylko hasła do uwierzytelniania użytkownika, pozostawia niezabezpieczony wektor do ataku. Jeśli hasło jest słabe lub zostało ujawnione w innym miejscu, czy rzeczywiście użytkownik loguje się przy użyciu nazwy użytkownika i hasła, czy też jest to osoba atakująca? Jeśli potrzebujesz drugiej formy uwierzytelniania, zwiększa się bezpieczeństwo, ponieważ ten dodatkowy czynnik nie jest łatwym dla osoby atakującej w uzyskaniu lub zduplikowaniu.

![Obraz koncepcyjny różnych form uwierzytelniania wieloskładnikowego](./media/concept-mfa-howitworks/methods.png)

Uwierzytelnianie wieloskładnikowe platformy Azure działa, wymagając co najmniej dwóch z następujących metod uwierzytelniania:

* Coś, co wiesz, zazwyczaj hasło.
* Coś, co masz, takie jak zaufane urządzenie, które nie jest łatwo powielane, takie jak telefon lub klucz sprzętowy.
* Coś, czym jesteś - biometria, taka jak odcisk palca lub skanowanie twarzy.

Użytkownicy mogą zarejestrować się w celu resetowania hasła samoobsługowego i uwierzytelniania wieloskładnikowego platformy Azure w jednym kroku, aby uprościć środowisko przy wejściu na pokład. Administratorzy mogą definiować, jakie formy uwierzytelniania pomocniczego mogą być używane. Uwierzytelnianie wieloskładnikowe platformy Azure może być również wymagane, gdy użytkownicy wykonują samoobsługowe resetowanie hasła w celu dalszego zabezpieczenia tego procesu.

## <a name="password-protection"></a>Ochrona hasłem

Domyślnie usługa Azure AD blokuje słabe hasła, takie jak *Password1*. Globalna lista zakazanych haseł jest automatycznie aktualizowana i wymuszana, która zawiera znane słabe hasła. Jeśli użytkownik usługi Azure AD próbuje ustawić swoje hasło do jednego z tych słabych haseł, otrzymuje powiadomienie, aby wybrać bardziej bezpieczne hasło.

Aby zwiększyć bezpieczeństwo, można zdefiniować niestandardowe zasady ochrony hasłem. Te zasady mogą używać filtrów do blokowania dowolnej odmiany hasła zawierającego nazwę, taką jak *Contoso* lub lokalizacji, takiej jak *Londyn,* na przykład.

Aby uzyskać zabezpieczenia hybrydowe, można zintegrować ochronę hasłem usługi Azure AD z lokalnym środowiskiem usługi Active Directory. Składnik zainstalowany w środowisku prem odbiera globalną listę zakazanych haseł i niestandardowe zasady ochrony hasłem z usługi Azure AD, a kontrolery domeny używają ich do przetwarzania zdarzeń zmiany hasła. To podejście hybrydowe zapewnia, że niezależnie od tego, jak i gdzie użytkownik zmienia swoje poświadczenia, można wymusić użycie silnych haseł.

## <a name="passwordless-authentication"></a>Uwierzytelnianie bez hasła

Celem końcowym dla wielu środowisk jest usunięcie użycia haseł w ramach zdarzeń logowania. Funkcje, takie jak ochrona hasłem platformy Azure lub uwierzytelnianie wieloskładnikowe platformy Azure pomagają zwiększyć bezpieczeństwo, ale nazwa użytkownika i hasło pozostają słabą formą uwierzytelniania, która może być narażona lub zaatakowana przez brutalną siłę.

![Bezpieczeństwo a wygoda dzięki procesowi uwierzytelniania, który prowadzi do bez hasła](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Po zalogowaniu się przy użyciu metody bez hasła poświadczenia są dostarczane za pomocą metod, takich jak biometria za pomocą funkcji Windows Hello dla firm lub klucza zabezpieczeń FIDO2. Tych metod uwierzytelniania nie można łatwo zduplikować przez osobę atakującą.

Usługa Azure AD udostępnia sposoby natywnego uwierzytelniania przy użyciu metod bez hasła, aby uprościć środowisko logowania dla użytkowników i zmniejszyć ryzyko ataków.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz [szybki start dla samoobsługowego resetowania hasła][quickstart-sspr] i [samouczek uwierzytelniania wieloskładnikowego platformy Azure][tutorial-mfa-applications].

Aby dowiedzieć się więcej o samoobsługowych pojęciach resetowania haseł, zobacz [Jak działa samoobsługowe resetowanie haseł usługi Azure AD][concept-sspr].

Aby dowiedzieć się więcej o pojęciach dotyczących uwierzytelniania wieloskładnikowego, zobacz [Jak działa uwierzytelnianie wieloskładnikowe platformy Azure.][concept-mfa]

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
