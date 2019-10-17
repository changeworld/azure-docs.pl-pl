---
title: Ustawienia domyślne zabezpieczeń Azure Active Directory
description: Zasady domyślne zabezpieczeń przeznaczone do ochrony organizacji przed typowymi atakami
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453045"
---
# <a name="what-are-security-defaults"></a>Co to są wartości domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne, gdy typowe ataki związane z tożsamościami, takie jak rozpylanie hasła, odtwarzanie i phishing, stają się coraz bardziej popularne. Tworzenie prostszej metody zabezpieczania organizacji przed tymi typowymi atakami jest celem ustawień domyślnych zabezpieczeń w Azure Active Directory (AD). Domyślne ustawienia zabezpieczeń ułatwiają ochronę organizacji przed typowymi atakami. Wartości domyślne zabezpieczeń zawierają wstępnie skonfigurowane ustawienia zabezpieczeń dla tych typowych ataków. Firma Microsoft udostępnia wartości domyślne zabezpieczeń dla wszystkich użytkowników. Celem jest zapewnienie, że wszystkie organizacje mają podstawowy poziom zabezpieczeń włączony bez dodatkowych kosztów.

![Zrzut ekranu przedstawiający nowe środowisko użytkownika ustawień domyślnych zabezpieczeń](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Następujące konfiguracje zabezpieczeń zostaną włączone w dzierżawie. 

## <a name="unified-mfa-registration"></a>Ujednolicona Rejestracja usługi MFA

Wszyscy użytkownicy w dzierżawie będą zobowiązani do rejestracji w usłudze Azure Multi-Factor Authentication (MFA). Użytkownicy będą mieli 14 dni na zarejestrowanie usługi Azure MFA przy użyciu aplikacji Microsoft Authenticator. Po upływie 14 dni użytkownik nie będzie mógł się zalogować do momentu zakończenia rejestracji usługi MFA.

Firma Microsoft zdaje sobie sprawę, że niektórzy użytkownicy mogą znajdować się poza biurem i/lub nie będą logować się w ciągu 14 dni bezpośrednio po włączeniu domyślnych ustawień zabezpieczeń. Aby zapewnić, że każdy użytkownik ma dużo czasu na zarejestrowanie usługi MFA, 14-dniowy okres jest unikatowy dla każdego użytkownika. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym zalogowaniu interakcyjnym po włączeniu wartości domyślnych zabezpieczeń.

## <a name="mfa-enforcement"></a>Wymuszanie usługi MFA

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy mający dostęp do kont uprzywilejowanych mają zwiększony dostęp do Twojego środowiska. Ze względu na moc tych kont należy traktować je z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w przypadku logowania się do nich była wymagana silniejsza weryfikacja konta. W Azure Active Directory można uzyskać silniejszą weryfikację konta przez wymaganie uwierzytelniania wieloskładnikowego.

Po zakończeniu rejestracji usługi MFA do przeprowadzenia uwierzytelniania wieloskładnikowego wymagane są następujące dziewięć ról administratorów usługi Azure AD.

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- Administrator pomocy technicznej/administrator haseł
- Administrator rozliczeń
- Administrator użytkowników
- Administrator uwierzytelniania

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Chcemy myśleć, że konta administratorów są jedynymi kontami, które wymagają ochrony przy użyciu uwierzytelniania wieloskładnikowego (MFA). Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach całej subskrypcji. Niemniej jednak w przypadku nieprawidłowych uczestników chcemy kierować użytkowników końcowych. Po uzyskaniu dostępu te złe osoby mogą zażądać dostępu do informacji uprzywilejowanych w imieniu oryginalnego właściciela konta lub pobrać cały katalog w celu przeprowadzenia ataku phishingowego w całej organizacji. Jedną z typowych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej weryfikacji konta, takiej jak uwierzytelnianie wieloskładnikowe (MFA) dla każdej z nich. Po zakończeniu rejestracji usługi MFA użytkownicy będą monitowani o uwierzytelnianie wieloskładnikowe w razie potrzeby.

### <a name="blocking-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Starsza wersja uwierzytelniania to termin, który odwołuje się do żądania uwierzytelnienia wykonanego przez:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klient pakietu Office 2010)
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP/SMTP/POP3

Dzisiaj większość wszelkich niezwiązanych z logowaniem nie pochodzi ze starszej wersji uwierzytelniania. Starsza wersja uwierzytelniania nie obsługuje uwierzytelniania wieloskładnikowego (MFA). Nawet jeśli w Twoim katalogu włączono zasady MFA, nieprawidłowy aktor może być uwierzytelniany przy użyciu starszego protokołu i pomijania usługi MFA. Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie zostaną zablokowane wszystkie żądania uwierzytelnienia podejmowane przez starszy protokół. Ustawienia domyślne zabezpieczeń nie blokują programu Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Ochrona uprzywilejowanych akcji

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pomocą interfejsu API Azure Resource Manager, w tym:

- Azure Portal 
- Program Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami to wysoce uprzywilejowana akcja. Azure Resource Manager mogą zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie wieloskładnikowe jest podatne na różne ataki, takie jak phishing i rozpylanie haseł. W związku z tym ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do konfiguracji Azure Resource Manager i aktualizacji, przez wymaganie uwierzytelniania wieloskładnikowego przed zezwoleniem na dostęp.

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie dowolny użytkownik uzyskujący dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure będzie wymagany do ukończenia uwierzytelniania wieloskładnikowego. Te zasady mają zastosowanie do wszystkich użytkowników, którzy uzyskują dostęp do Azure Resource Manager niezależnie od tego, czy jesteś administratorem, czy użytkownikiem. Jeśli użytkownik nie jest zarejestrowany na potrzeby uwierzytelniania wieloskładnikowego, użytkownik będzie musiał zarejestrować się przy użyciu aplikacji Microsoft Authenticator, aby można było wykonać tę operację. Nie zostanie podany 14-dniowy okres rejestracji usługi MFA.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Poniżej przedstawiono kilka dodatkowych zagadnień związanych z wdrażaniem ustawień domyślnych zabezpieczeń dla dzierżawy.

### <a name="legacy-protocols"></a>Starsze protokoły

Starsze protokoły uwierzytelniania (IMAP, SMTP, POP3 itp.) są używane przez klientów poczty do przesyłania żądań uwierzytelniania. Te protokoły nie obsługują usługi MFA. Większość naruszeń kont widzianych przez firmę Microsoft jest spowodowana przez niewłaściwym uczestnikom ataków na starsze protokoły próbujących obejść usługę MFA. Aby zapewnić, że uwierzytelnianie wieloskładnikowe jest wymagane w przypadku logowania się do konta administracyjnego, a niewłaściwe podmioty nie mogą obejść usługi MFA, domyślnie są blokowane wszystkie żądania uwierzytelniania skierowane do kont administratorów ze starszych protokołów.

> [!WARNING]
> Przed włączeniem tego ustawienia upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz artykuł [jak przejść od starszego uwierzytelniania](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Dostęp warunkowy

Dostępu warunkowego można użyć do skonfigurowania zasad, które zapewniają takie samo zachowanie, jak domyślne ustawienia zabezpieczeń. Jeśli używasz dostępu warunkowego i masz włączone zasady dostępu warunkowego w środowisku, ustawienia domyślne zabezpieczeń nie będą dostępne dla Ciebie. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie masz włączonych zasad dostępu warunkowego w danym środowisku, możesz użyć domyślnych ustawień zabezpieczeń do momentu włączenia zasad urzędu certyfikacji.

![Ustawienia domyślne zabezpieczeń lub dostęp warunkowy nie są jednocześnie](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Poniżej przedstawiono wskazówki krok po kroku dotyczące sposobu korzystania z dostępu warunkowego w celu konfigurowania równoważnych zasad:

- [Wymagaj uwierzytelniania wieloskładnikowego dla administratorów](howto-conditional-access-policy-admin-mfa.md)
- [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania na platformie Azure](howto-conditional-access-policy-azure-management.md)
- [Blokuj starsze uwierzytelnianie](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Włączanie ustawień domyślnych zabezpieczeń

Aby włączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)@no__t — 1AS administrator zabezpieczeń, administrator dostępu warunkowego lub Administrator globalny.
1. Przejdź do **Azure Active Directory**  @ no__t-2 **Właściwości**
1. Wybierz pozycję **Zarządzaj wartościami domyślnymi zabezpieczeń**
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na **wartość tak**.
1. Kliknij przycisk Zapisz.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Co to jest dostęp warunkowy?](overview.md)
