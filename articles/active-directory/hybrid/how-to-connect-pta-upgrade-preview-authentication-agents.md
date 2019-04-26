---
title: Uwierzytelnianie uaktualnienia agentów programu Azure AD Connect — uwierzytelnianie przekazujących — | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uaktualniania konfiguracji uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386775"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: Uaktualnianie agentów uwierzytelniania w wersji zapoznawczej

## <a name="overview"></a>Omówienie

Ten artykuł jest dla klientów korzystających z usługi Azure AD przekazywanego uwierzytelniania za pomocą wersji zapoznawczej. Firma Microsoft niedawno uaktualniony (i przemianowane) oprogramowanie agenta uwierzytelniania. Musisz _ręcznie_ Podgląd uaktualniania agentów uwierzytelniania zainstalowanych na serwerach lokalnych. Ręczne uaktualnienie jest tylko Akcja jednorazowa. Wszystkie przyszłe aktualizacje agentów uwierzytelniania są automatyczne. Dostępne są następujące przyczyny do uaktualnienia:

- Wersje zapoznawcze agentów uwierzytelniania nie będą otrzymywać żadnych dalszych zabezpieczeń lub poprawki.
-   Wersje zapoznawcze agentów uwierzytelniania nie można zainstalować na dodatkowych serwerach w celu zapewnienia wysokiej dostępności.

## <a name="check-versions-of-your-authentication-agents"></a>Sprawdź wersje agentów uwierzytelniania

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Sprawdź, w których zainstalowano agentów uwierzytelniania

Wykonaj następujące kroki, aby sprawdzić, w których zainstalowano agentów uwierzytelniania:

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w okienku nawigacji po lewej stronie.
3. Wybierz **programu Azure AD Connect**. 
4. Wybierz **uwierzytelniania przekazywanego**. Ten blok zawiera listę serwerów, na których zainstalowano agentów uwierzytelniania.

![Centrum administracyjne usługi Active Directory systemu Azure — bloku uwierzytelniania przekazywanego](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Sprawdź wersje agentów uwierzytelniania

Aby sprawdzić wersje agentów uwierzytelniania, na każdym serwerze zidentyfikowany w poprzednim kroku, postępuj zgodnie z tymi instrukcjami:

1. Przejdź do **Panel sterowania -> programy -> programy i funkcje** na serwerze lokalnym.
2. Jeśli istnieje wpis dla "**agenta: Microsoft Azure AD Connect uwierzytelniania**", nie trzeba podejmować żadnych działań na tym serwerze.
3. Jeśli istnieje wpis dla "**łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD**", należy ręcznie uaktualnić na tym serwerze.

![Wersję zapoznawczą agenta uwierzytelniania](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Przed rozpoczęciem uaktualniania najlepszych rozwiązań

Przed rozpoczęciem uaktualniania upewnij się, że masz następujące elementy w miejscu:

1. **Tworzenie konta administratora globalnego tylko w chmurze**: Nie uaktualniaj bez konta administratora globalnego tylko w chmurze do użycia w sytuacjach awaryjnych, gdzie agentów uwierzytelniania przekazywanego nie działa prawidłowo. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Ten krok ma kluczowe znaczenie i gwarantuje, że możesz zablokowaniu dzierżawy.
2.  **Zapewni to wysoką dostępność**: Jeśli wcześniej nie zakończona, należy zainstalować autonomiczny drugi Agent uwierzytelniania w celu zapewnienia wysokiej dostępności dla żądań logowania, za pomocą tych [instrukcje](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uaktualnianie agenta uwierzytelniania na serwerze programu Azure AD Connect

Przed uaktualnieniem Agent uwierzytelniania na tym samym serwerze, należy uaktualnić program Azure AD Connect. Podstawowa i przemieszczania serwery programu Azure AD Connect, wykonaj następujące kroki:

1. **Uaktualnij program Azure AD Connect**: Postępuj zgodnie z tym [artykułu](how-to-upgrade-previous-version.md) i przeprowadź uaktualnienie do najnowszej wersji Azure AD Connect.
2. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako Administrator na serwerze.
3. **Pobierz najnowszą wersję agenta uwierzytelniania (wersje 1.5.389.0 lub nowszej)**: Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz **usługi Azure Active Directory -> Azure AD Connect -> uwierzytelniania przekazywanego -> Pobierz agenta**. Zaakceptuj [warunki użytkowania usługi](https://aka.ms/authagenteula) i Pobierz najnowszą wersję agenta uwierzytelniania. Możesz również pobrać agenta uwierzytelniania z [tutaj](https://aka.ms/getauthagent).
4. **Zainstaluj najnowszą wersję agenta uwierzytelniania programu**: Uruchom plik wykonywalny pobrany w kroku 3. Podaj poświadczenia administratora globalnego swojej dzierżawy, po wyświetleniu monitu.
5. **Sprawdź, czy zainstalowano najnowszą wersję**: Jak pokazano wcześniej, przejdź do **Panel sterowania -> programy -> programy i funkcje** i sprawdź, czy istnieje wpis dla "**agenta: Microsoft Azure AD Connect uwierzytelniania**".

>[!NOTE]
>Jeśli zaznaczysz bloku uwierzytelniania przekazywanego na [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) po Kończenie poprzednie kroki, zostaną wyświetlone dwa wpisy agenta uwierzytelniania na serwer - jeden wpis przedstawiający uwierzytelniania Agenta jako **Active** i innych **nieaktywne**. Jest to _Oczekiwano_. **Nieaktywny** wpis jest automatycznie usuwane po upływie kilku dni.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uaktualnianie agenta uwierzytelniania, na innych serwerach

Wykonaj następujące kroki, aby uaktualnić agentów uwierzytelniania na innych serwerach (gdzie program Azure AD Connect nie jest zainstalowany):

1. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako Administrator na serwerze.
2. **Pobierz najnowszą wersję agenta uwierzytelniania (wersje 1.5.389.0 lub nowszej)**: Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz **usługi Azure Active Directory -> Azure AD Connect -> uwierzytelniania przekazywanego -> Pobierz agenta**. Zaakceptuj warunki użytkowania usługi i Pobierz najnowszą wersję.
3. **Zainstaluj najnowszą wersję agenta uwierzytelniania programu**: Uruchom plik wykonywalny pobrany w kroku 2. Podaj poświadczenia administratora globalnego swojej dzierżawy, po wyświetleniu monitu.
4. **Sprawdź, czy zainstalowano najnowszą wersję**: Jak pokazano wcześniej, przejdź do **Panel sterowania -> programy -> programy i funkcje** i sprawdź, czy wpis o nazwie **agenta: Microsoft Azure AD Connect uwierzytelniania**.

>[!NOTE]
>Jeśli zaznaczysz bloku uwierzytelniania przekazywanego na [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) po Kończenie poprzednie kroki, zostaną wyświetlone dwa wpisy agenta uwierzytelniania na serwer - jeden wpis przedstawiający uwierzytelniania Agenta jako **Active** i innych **nieaktywne**. Jest to _Oczekiwano_. **Nieaktywny** wpis jest automatycznie usuwane po upływie kilku dni.

## <a name="next-steps"></a>Kolejne kroki
- [**Rozwiązywanie problemów z** ](tshoot-connect-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
