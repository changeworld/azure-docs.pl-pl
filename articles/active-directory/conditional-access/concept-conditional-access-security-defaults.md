---
title: Ustawienia domyślne zabezpieczeń Azure Active Directory
description: Domyślne zasady zabezpieczeń, które ułatwiają ochronę organizacji przed typowymi atakami
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
ms.openlocfilehash: 502fb8b49770b4933c44aa1eb447abb746943c0d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554814"
---
# <a name="what-are-security-defaults"></a>Co to są wartości domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne, gdy typowe ataki związane z tożsamościami stają się coraz bardziej popularne. Ataki te obejmują rozpylanie, odtwarzanie i wyłudzanie informacji.

Domyślne ustawienia zabezpieczeń w programie Azure Active Directory (Azure AD) ułatwiają ochronę organizacji. Wartości domyślne zabezpieczeń zawierają wstępnie skonfigurowane ustawienia zabezpieczeń dla typowych ataków. 

Firma Microsoft udostępnia wartości domyślne zabezpieczeń dla wszystkich użytkowników. Celem jest zapewnienie, że wszystkie organizacje mają podstawowy poziom zabezpieczeń włączony bez dodatkowych kosztów. Ustawienia domyślne zabezpieczeń są włączane w Azure Portal.

![Zrzut ekranu przedstawiający Azure Portal z przełącznikiem umożliwiającym włączenie ustawień domyślnych zabezpieczeń](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Następujące konfiguracje zabezpieczeń zostaną włączone w dzierżawie. 

## <a name="unified-multi-factor-authentication-registration"></a>Ujednolicona Rejestracja Multi-Factor Authentication

Wszyscy użytkownicy w dzierżawie muszą zarejestrować się w celu uwierzytelniania wieloskładnikowego (MFA) w formie usługi Azure Multi-Factor Authentication. Użytkownicy mają 14 dni na zarejestrowanie Multi-Factor Authentication przy użyciu aplikacji Microsoft Authenticator. Po upływie 14 dni użytkownik nie będzie mógł się zalogować do momentu zakończenia rejestracji Multi-Factor Authentication.

Rozumiemy, że niektórzy użytkownicy mogą znajdować się poza biurem lub nie będą logować się w ciągu 14 dni bezpośrednio po włączeniu ustawień domyślnych zabezpieczeń. Aby zapewnić, że każdy użytkownik ma dużo czasu na zarejestrowanie się w celu Multi-Factor Authentication, okres 14 dni jest unikatowy dla każdego użytkownika. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym zalogowaniu interakcyjnym po włączeniu domyślnych ustawień zabezpieczeń.

## <a name="multi-factor-authentication-enforcement"></a>Wymuszanie Multi-Factor Authentication

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy mający dostęp do kont uprzywilejowanych mają zwiększony dostęp do Twojego środowiska. Ze względu na moc tych kont należy traktować je z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w celu zalogowania się była silniejsza weryfikacja konta. W usłudze Azure AD możesz uzyskać silniejszą weryfikację konta, wymagając Multi-Factor Authentication.

Po zakończeniu rejestracji w Multi-Factor Authentication zostanie zaakceptowana następująca dziewiąta rola administratora usługi Azure AD do przeprowadzenia dodatkowego uwierzytelniania przy każdym logowaniu:

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- Administrator pomocy technicznej lub administrator haseł
- Administrator rozliczeń
- Administrator użytkowników
- Administrator uwierzytelniania

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Chcemy myśleć, że konta administratorów są jedynymi kontami, które wymagają dodatkowych warstw uwierzytelniania. Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach całej subskrypcji. Osoby atakujące mają do nich możliwość ukierunkowania na użytkowników końcowych. 

Gdy Ci osoby atakujące uzyskali dostęp, mogą zażądać dostępu do informacji uprzywilejowanych w imieniu właściciela oryginalnego konta. Mogą nawet pobrać cały katalog, aby przeprowadzić atak wyłudzania informacji w całej organizacji. 

Jedną z częstych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta, takiej jak Multi-Factor Authentication, dla wszystkich. Po zakończeniu Multi-Factor Authentication rejestracji użytkownicy będą monitowani o dodatkowe uwierzytelnianie w razie potrzeby.

### <a name="blocking-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure AD obsługuje różne protokoły uwierzytelniania, w tym starsze uwierzytelnianie. *Starsza wersja uwierzytelniania* to termin, który odwołuje się do żądania uwierzytelnienia wykonanego przez:

- Starsi klienci pakietu Office, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010).
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP, SMTP lub POP3.

Dzisiaj większość nieżądanych prób logowania pochodzi ze starszego uwierzytelniania. Starsza wersja uwierzytelniania nie obsługuje Multi-Factor Authentication. Nawet jeśli masz zasady Multi-Factor Authentication włączone w katalogu, osoba atakująca może się uwierzytelnić przy użyciu starszego protokołu i Multi-Factor Authentication obejścia. 

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie zostaną zablokowane wszystkie żądania uwierzytelnienia podejmowane przez starszy protokół. Ustawienia domyślne zabezpieczeń nie blokują programu Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Ochrona uprzywilejowanych akcji

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pomocą interfejsu API Azure Resource Manager, w tym:

- Azure Portal 
- Program Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami to wysoce uprzywilejowana akcja. Azure Resource Manager mogą zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie wieloskładnikowe jest podatne na różne ataki, takie jak phishing i rozpylanie haseł. 

Ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do Azure Resource Manager i konfiguracji aktualizacji. Tożsamość należy zweryfikować, wymagając dodatkowego uwierzytelniania przed zezwoleniem na dostęp.

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie każdy użytkownik, który uzyskuje dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, będzie musiał wykonać dodatkowe uwierzytelnianie. Te zasady mają zastosowanie do wszystkich użytkowników, którzy uzyskują dostęp do Azure Resource Manager, niezależnie od tego, czy są oni administratorem czy użytkownikiem. 

Jeśli użytkownik nie jest zarejestrowany do Multi-Factor Authentication, użytkownik będzie musiał zarejestrować się przy użyciu aplikacji Microsoft Authenticator, aby można było wykonać tę operację. Nie zostanie podany 14-dniowy okres rejestracji Multi-Factor Authentication.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Poniższe zagadnienia dodatkowe są związane z wdrażaniem ustawień domyślnych zabezpieczeń dla dzierżawy.

### <a name="older-protocols"></a>Starsze protokoły

Klienci poczty używają starszych protokołów uwierzytelniania (takich jak IMAP, SMTP i POP3), aby wykonywać żądania uwierzytelniania. Te protokoły nie obsługują Multi-Factor Authentication. Większość zabezpieczeń konta, które firma Microsoft widzi przed atakami na starsze protokoły, które próbują obejść Multi-Factor Authentication. 

Aby upewnić się, że Multi-Factor Authentication jest wymagane do logowania się do konta administracyjnego i że osoby atakujące nie mogą go obejść, domyślnie są blokowane wszystkie żądania uwierzytelnienia skierowane do kont administratorów ze starszych protokołów.

> [!WARNING]
> Przed włączeniem tego ustawienia upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [jak przenieść się z starszego uwierzytelniania](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Dostęp warunkowy

Za pomocą dostępu warunkowego można skonfigurować zasady zapewniające takie samo zachowanie, które są włączone przez domyślne ustawienia zabezpieczeń. Jeśli używasz dostępu warunkowego i w środowisku włączono zasady dostępu warunkowego, nie będą dostępne żadne ustawienia domyślne zabezpieczeń. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie masz włączonych zasad dostępu warunkowego w danym środowisku, możesz użyć domyślnych ustawień zabezpieczeń do momentu włączenia zasad dostępu warunkowego.

![Komunikat ostrzegawczy, który może mieć wartości domyślne zabezpieczeń lub dostęp warunkowy nie zarówno](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Poniżej przedstawiono wskazówki krok po kroku dotyczące konfigurowania równoważnych zasad przy użyciu dostępu warunkowego:

- [Wymagaj uwierzytelniania wieloskładnikowego dla administratorów](howto-conditional-access-policy-admin-mfa.md)
- [Wymagaj uwierzytelniania wieloskładnikowego na potrzeby zarządzania na platformie Azure](howto-conditional-access-policy-azure-management.md)
- [Blokuj starsze uwierzytelnianie](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Włączanie ustawień domyślnych zabezpieczeń

Aby włączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)  as administratorem zabezpieczeń, administratorem dostępu warunkowego lub administratorem globalnym.
1. Przejdź do **Azure Active Directory**   > **Właściwości**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na **wartość tak**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Co to jest dostęp warunkowy?](overview.md)
