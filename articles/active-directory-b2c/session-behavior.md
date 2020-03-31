---
title: Konfigurowanie zachowania sesji — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Konfigurowanie zachowania sesji w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186815"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurowanie zachowania sesji w usłudze Azure Active Directory B2C

Ta funkcja zapewnia precyzyjną kontrolę przepływu [na użytkownika:](user-flow-overview.md)

- Okres istnienia sesji aplikacji sieci web zarządzanych przez usługę Azure AD B2C.
- Zachowanie logowania jednokrotnego (Logowanie jednokrotne) w wielu aplikacjach i przepływach użytkowników w dzierżawie usługi Azure AD B2C.

Te ustawienia nie są dostępne dla przepływów użytkownika resetowania hasła.

Usługa Azure AD B2C obsługuje [protokół uwierzytelniania OpenID Connect](openid-connect.md) umożliwiający bezpieczne logowanie do aplikacji sieci Web. Do zarządzania sesjami aplikacji sieci Web można używać następujących właściwości:

## <a name="session-behavior-properties"></a>Właściwości zachowania sesji

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — okres istnienia pliku cookie sesji usługi Azure AD B2C przechowywane w przeglądarce użytkownika po pomyślnym uwierzytelnieniu.
    - Domyślnie = 1440 minut.
    - Minimum (włącznie) = 15 minut.
    - Maksymalna (włącznie) = 1440 minut.
- **Limit czasu sesji aplikacji sieci Web** — jeśli ten przełącznik jest ustawiony na **Bezwzględny,** użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie okresu określonego przez **okres istnienia sesji aplikacji sieci Web (minut).** Jeśli ten przełącznik jest ustawiony na **Rolling** (ustawienie domyślne), użytkownik pozostaje zalogowany tak długo, jak użytkownik jest stale aktywny w aplikacji sieci web.
- **Konfiguracja logowania jednokrotnego** Jeśli masz wiele aplikacji i przepływów użytkowników w dzierżawie B2C, można zarządzać interakcje użytkowników między nimi za pomocą **właściwości konfiguracji logowania jednokrotnego.** Można ustawić właściwość na jedno z następujących ustawień:
    - **Dzierżawa** — to ustawienie jest ustawieniem domyślnym. Za pomocą tego ustawienia umożliwia wiele aplikacji i przepływów użytkownika w dzierżawie B2C do udziału w tej samej sesji użytkownika. Na przykład gdy użytkownik zaloguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego, Contoso Pharmacy, po uzyskaniu do niego dostępu.
    - **Aplikacja** — to ustawienie umożliwia zachowanie sesji użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz, aby użytkownik zalogował się do Contoso Pharmacy (z tymi samymi poświadczeniami), nawet jeśli użytkownik jest już zalogowany do contoso shopping, innej aplikacji w tej samej dzierżawie B2C.
    - **Zasady** — to ustawienie umożliwia zachowanie sesji użytkownika wyłącznie dla przepływu użytkownika, niezależnie od aplikacji korzystających z niego. Na przykład jeśli użytkownik już zalogował się i ukończył krok uwierzytelniania wieloskładnikowego (MFA), użytkownik może uzyskać dostęp do części o podwyższonym poziomie zabezpieczeń wielu aplikacji, o ile sesja powiązana z przepływem użytkownika nie wygaśnie.
    - **Wyłączone** — to ustawienie wymusza na użytkowniku uruchamianie przez cały przepływ użytkownika przy każdym wykonaniu zasad.

Przy użyciu tych właściwości włączono następujące przypadki użycia:

- Spełnij wymagania twojej branży dotyczące zabezpieczeń i zgodności, ustawiając odpowiednie okresy istnienia sesji aplikacji sieci web.
- Wymuś uwierzytelnianie po określonym czasie podczas interakcji użytkownika z wysokozabezpieczaną częścią aplikacji sieci web.

## <a name="configure-the-properties"></a>Konfigurowanie właściwości

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcji katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **przepływy użytkownika (zasady)**.
5. Otwórz poprzednio utworzony przepływ użytkownika.
6. Wybierz **pozycję Właściwości**.
7. Konfigurowanie **okresu istnienia sesji aplikacji sieci Web (w minutach),** limit czasu sesji aplikacji sieci **Web,** **konfiguracja logowania jednokrotnego**i **wymaganie tokenu identyfikatora w żądaniach wylogowania zgodnie** z potrzebami.

    ![Ustawienia właściwości zachowania sesji w witrynie Azure portal](./media/session-behavior/session-behavior.png)

8. Kliknij przycisk **Zapisz**.
