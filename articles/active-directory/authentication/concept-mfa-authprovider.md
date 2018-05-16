---
title: Wprowadzenie do dostawcy usługi Azure Multi-Factor Authentication | Microsoft Docs
description: Dowiedz się, jak utworzyć dostawcę usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 453b8cc399c78ddb26ae601abf64626d2a6bf36f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Wprowadzenie do dostawcy usługi Azure Multi-Factor Authentication
Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

Funkcje oferowane w pełnej wersji usługi Azure MFA są dostępne za pośrednictwem dostawcy usługi Azure Multi-Factor Authentication. Jest on przeznaczony dla użytkowników, którzy **nie mają licencji usługi Azure MFA lub Azure AD w wersji Premium ani licencji pakietu Enterprise Mobility + Security (EMS)**.  Usługa Azure MFA, usługa Azure AD w wersja Premium oraz pakiet EMS domyślnie zawierają pełną wersję usługi Azure MFA. Jeśli masz te licencje, nie potrzebujesz dostawcy usługi Azure Multi-Factor Authentication.

Dostawca usługi Azure Multi-Factor Authentication jest wymagany do pobrania zestawu SDK.

> [!IMPORTANT]
> Ogłoszono już zakończenie obsługi zestawu SDK usługi Azure Multi-Factor Authentication. Ta funkcja nie jest już obsługiwana dla nowych klientów. Obecni klienci mogą używać zestawu SDK do 14 listopada 2018 r. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

> [!IMPORTANT]
>Aby pobrać zestaw SDK, musisz utworzyć dostawcę usługi Azure Multi-Factor Authentication, nawet jeśli masz licencje usług Azure MFA, AAD Premium lub EMS.  Jeśli tworzysz dostawcę usługi Azure Multi-Factor Authentication w tym celu i masz już licencje, pamiętaj o utworzeniu go przy użyciu modelu **Za włączonego użytkownika**. Następnie połącz dostawcę z katalogiem zawierającym licencje usług Azure MFA, Azure AD Premium lub EMS. Dzięki tej konfiguracji będziesz ponosić dodatkowe opłaty jedynie w przypadku, gdy liczba unikatowych użytkowników korzystających z weryfikacji dwuetapowej przekroczy liczbę posiadanych przez Ciebie licencji. 

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

Jeśli nie masz licencji na korzystanie z usługi Azure Multi-Factor Authentication, możesz utworzyć dostawcę uwierzytelniania w celu wdrożenia weryfikacji dwuetapowej użytkowników.

Istnieją dwa typy dostawców uwierzytelniania, które różnią się sposobem naliczania opłat za korzystanie z subskrypcji platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

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

Jeśli bieżący dostawca usługi Multi-Factor Auth jest skojarzony z katalogiem usługi Azure AD (znanym również jako dzierżawa usługi Azure AD), możesz bezpiecznie usunąć dostawcę usługi MFA, a następnie utworzyć dostawcę połączonego z tą samą dzierżawą usługi Azure AD. Alternatywnie, jeśli masz zakupionych dostatecznie dużo licencji MFA, Azure AD Premium lub Enterprise Mobility + Security (EMS), aby objęły one wszystkich użytkowników włączonych do usługi MFA, możesz zupełnie usunąć dostawcę usługi MFA.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto należy ponownie aktywować istniejące serwery usługi Azure MFA przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem nowego dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w celu ich połączenia z nowym dostawcą usługi MFA nie wpływa na uwierzytelnianie za pośrednictwem połączeń telefonicznych i wiadomości SMS, ale powiadomienia w aplikacji mobilnej przestaną działać dla wszystkich użytkowników do momentu, w którym ponownie aktywują aplikację mobilną.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Dodaj dostawców MFA"
