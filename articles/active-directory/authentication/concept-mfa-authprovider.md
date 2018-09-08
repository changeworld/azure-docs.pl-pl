---
title: Kiedy i jak używać dostawcy usługi Multi-Factor Authentication platformy Azure?
description: Kiedy należy używać dostawcy usługi Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 8e77a33667bd6794f667348958e0edb9c6a8fb0d
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094981"
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

Istnieją dwa typy dostawców uwierzytelniania, a różnica polega na całym jak jest rozliczana subskrypcji platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie).

Jeśli zakupiono wystarczającą liczbę licencji, aby objęły one wszystkich użytkowników, które są włączone dla usługi MFA, możesz usunąć dostawcę usługi MFA całkowicie.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto istniejące serwery usługi Azure MFA, należy ponownie aktywować przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w taki sposób, aby połączyć je do dostawcy usługi MFA nie ma wpływu na połączeń telefonicznych i uwierzytelniania wiadomości tekstowe, ale powiadomienia w aplikacji mobilnej przestają działać dla wszystkich użytkowników, dopóki aktywują aplikację mobilną.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)
