---
title: Kiedy i jak używać dostawcy usługi Multi-Factor Authentication platformy Azure? — Usługa azure Active Directory
description: Kiedy należy używać dostawcy usługi Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b1461999679935587370f66349a440d588465cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052538"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kiedy należy używać dostawcę usługi Azure Multi-Factor Authentication

Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

Dostawcy usługi Multi-Factor Authentication platformy Azure umożliwia korzystanie z zalet funkcji oferowanych przez usługę Azure Multi-Factor Authentication dla użytkowników, którzy **nie masz licencji**.

> [!NOTE]
> Od 1 września 2018 r nowych dostawców uwierzytelniania już nie może zostać utworzony. Istniejące dostawców uwierzytelniania mogą nadal są używane, a następnie aktualizować, ale migracja nie jest już możliwe. Uwierzytelnianie wieloskładnikowe, będą w dalszym ciągu być dostępna jako funkcja w licencji usługi Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Zastrzeżenia związane z zestawem SDK usługi Azure MFA

Należy pamiętać, zestaw SDK jest przestarzała i tylko będą nadal działać do 14 listopada 2018 r. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

Istnieją dwa typy dostawców uwierzytelniania, a różnica polega na całym jak jest rozliczana subskrypcji platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie).

Jeśli zakupiono wystarczającą liczbę licencji, aby objęły one wszystkich użytkowników, które są włączone dla usługi MFA, możesz usunąć dostawcę usługi MFA całkowicie.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto istniejące serwery usługi Azure MFA, należy ponownie aktywować przy użyciu poświadczeń aktywacji wygenerowanych za pośrednictwem dostawcy usługi MFA. Ponowne aktywowanie serwerów usługi MFA w taki sposób, aby połączyć je do dostawcy usługi MFA nie ma wpływu na połączeń telefonicznych i uwierzytelniania wiadomości tekstowe, ale powiadomienia w aplikacji mobilnej przestają działać dla wszystkich użytkowników, dopóki aktywują aplikację mobilną.

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)
