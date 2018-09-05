---
title: Kiedy i jak używać dostawcy usługi Multi-Factor Authentication platformy Azure?
description: Kiedy należy używać dostawcy usługi Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: b601a3d23b23faa16925881a54e2ceba85c800f8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669069"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kiedy należy używać dostawcę usługi Azure Multi-Factor Authentication

Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

Dostawcy usługi Multi-Factor Authentication platformy Azure umożliwia korzystanie z zalet funkcji oferowanych przez usługę Azure Multi-Factor Authentication dla użytkowników, którzy **nie masz licencji**. 

Jeśli masz licencje, które obejmuje wszystkich użytkowników w Twojej organizacji, nie potrzebujesz dostawcy usługi Multi-Factor Authentication platformy Azure. Utwórz dostawcę usługi Azure Multi-Factor Authentication, tylko wtedy, gdy należy również podać weryfikację dwuetapową dla niektórych użytkowników, którzy nie mają licencji.

> [!NOTE]
> Od 1 września 2018 r nowych dostawców uwierzytelniania już nie może zostać utworzony. Istniejące dostawców uwierzytelniania mogą nadal może być używany i zaktualizowane. Uwierzytelnianie wieloskładnikowe będzie dostępna funkcja licencje usługi Azure AD Premium w.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Zastrzeżenia związane z zestawem SDK usługi Azure MFA

Dostawca usługi Azure Multi-Factor Authentication jest wymagany do pobrania zestawu SDK. Należy pamiętać, zestaw SDK jest przestarzała i nie jest już obsługiwana dla nowych klientów i tylko będą nadal działać do 14 listopada 2018 r. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

Aby pobrać zestaw SDK, utworzyć dostawcy usługi Multi-Factor Authentication platformy Azure, nawet jeśli masz usługi Azure MFA, AAD Premium lub inne powiązane licencje. Jeśli tworzysz dostawcę usługi Azure Multi-Factor Authentication w tym celu i masz już licencje, pamiętaj o utworzeniu go przy użyciu modelu **Za włączonego użytkownika**. Następnie połącz dostawcę z katalogu, który zawiera usługi Azure MFA, Azure AD Premium lub inne powiązane licencje. Dzięki tej konfiguracji będziesz ponosić dodatkowe opłaty jedynie w przypadku, gdy liczba unikatowych użytkowników korzystających z weryfikacji dwuetapowej przekroczy liczbę posiadanych przez Ciebie licencji.

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

Jeśli nie masz licencji dla usługi Azure Multi-Factor Authentication, możesz utworzyć dostawcę uwierzytelniania, które wymuszają weryfikację dwuetapową dla użytkowników.

Istnieją dwa typy dostawców uwierzytelniania, a różnica polega na całym jak jest rozliczana subskrypcji platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="create-an-mfa-provider"></a>Tworzenie dostawcy usługi MFA

Aby utworzyć dostawcę usługi Azure Multi-Factor Authentication w witrynie Azure Portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Wybierz pozycję **Azure Active Directory** > **Serwer MFA** > **Dostawcy**.

   ![Dostawcy][Providers]

3. Wybierz pozycję **Dodaj**.
4. Wypełnij poniższe pola i wybierz pozycję **Dodaj**:
   - **Nazwa** — nazwa dostawcy.
   - **Model użycia** – wybierz jedną z dwóch opcji:
      * Za uwierzytelnienie — model zakupu z opłatami za każde uwierzytelnienie. Zazwyczaj używany w scenariuszach, w których usługa Azure Multi-Factor Authentication jest używana w aplikacjach udostępnianych klientom.
      * Za włączonego użytkownika – model zakupu z opłatami za każdego włączonego użytkownika. Zazwyczaj używany w scenariuszach obejmujących korzystanie przez pracowników z aplikacji, takich jak Office 365. Wybierz tę opcję, jeśli niektórzy z Twoich użytkowników mają już licencje usługi Azure MFA.
   - **Subskrypcja** — subskrypcja platformy Azure, która jest rozliczana za działanie weryfikacji dwuetapowej za pośrednictwem dostawcy.
   - **Katalog** — dzierżawa usługi Azure Active Directory, z którą jest skojarzony dostawca.
      * Do utworzenia dostawcy nie jest potrzebny katalog usługi Azure AD. Jeśli zamierzasz tylko pobrać serwer usługi Azure Multi-Factor Authentication, pozostaw to pole puste.
      * Aby móc korzystać z zaawansowanych funkcji, dostawca musi być skojarzony z katalogiem usługi Azure AD.
      * Z każdym katalogiem usługi Azure AD może być skojarzony tylko jeden dostawca.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie). Możesz jednak usunąć dostawcę usługi MFA, a następnie utworzyć dostawcę z innym modelem użycia.

Jeśli bieżący dostawca usługi Multi-Factor Auth jest skojarzony z katalogiem usługi Azure AD (znanym również jako dzierżawa usługi Azure AD), możesz bezpiecznie usunąć dostawcę usługi MFA, a następnie utworzyć dostawcę połączonego z tą samą dzierżawą usługi Azure AD. Alternatywnie Jeśli zakupiono wystarczającą liczbę licencji, aby objęły one wszystkich użytkowników, które są włączone dla usługi MFA, można usunąć dostawcę usługi MFA całkowicie.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w taki sposób, aby dołączyć je do nowego dostawcę usługi MFA nie ma wpływu na połączeń telefonicznych i uwierzytelniania wiadomości tekstowe, ale powiadomienia w aplikacji mobilnej przestają działać dla wszystkich użytkowników, dopóki aktywują aplikację mobilną.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Dodaj dostawców MFA"
