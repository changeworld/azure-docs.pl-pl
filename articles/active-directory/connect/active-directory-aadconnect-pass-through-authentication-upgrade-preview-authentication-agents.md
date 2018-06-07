---
title: Agentów programu Azure AD Connect - uwierzytelniania przekazywanego - auth uaktualnienia | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uaktualniania konfiguracji uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: c699df83c7032dc6e3d176753d8203fec6977c1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591994"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Usługi Azure Active Directory przekazywanego uwierzytelniania: Agenci uwierzytelniania uaktualnienia wersji zapoznawczej

## <a name="overview"></a>Przegląd

W tym artykule jest przeznaczona dla klientów przy użyciu usługi Azure AD przekazywanego uwierzytelniania za pomocą podglądu. Firma Microsoft niedawno uaktualniony (i rebranded) oprogramowanie pośredniczące uwierzytelniania. Musisz _ręcznie_ uaktualnienia wersji zapoznawczej uwierzytelniania agentów zainstalowanych na serwerach lokalnych. Ręczne uaktualnienie jest tylko Akcja jednorazowa. Wszystkie przyszłe aktualizacje agentów uwierzytelniania są automatyczne. Dostępne są następujące przyczyny do uaktualnienia:

- Podgląd wersje agentów uwierzytelniania nie będą otrzymywać żadnych dalszych zabezpieczeń lub poprawki.
-   Nie można zainstalować wersji zapoznawczych uwierzytelniania agentów na dodatkowych serwerach wysokiej dostępności.

## <a name="check-versions-of-your-authentication-agents"></a>Sprawdź wersje agentów uwierzytelniania

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Sprawdź, których zainstalowano agentów uwierzytelniania

Wykonaj następujące kroki, aby sprawdzić zainstalowaną agentów uwierzytelniania:

1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** w nawigacji po lewej stronie.
3. Wybierz **programu Azure AD Connect**. 
4. Wybierz **uwierzytelniania przekazywanego**. Ten blok zawiera listę serwerów, na których zainstalowano agentów uwierzytelniania.

![Centrum administracyjne usługi Active Directory platformy Azure — blok przekazywanego uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Sprawdź wersje agentów uwierzytelniania

Aby sprawdzić wersje agentów uwierzytelniania, na każdym serwerze, które zostały zidentyfikowane w poprzednim kroku, wykonaj następujące instrukcje:

1. Przejdź do **Panel sterowania -> programy -> programy i funkcje** na serwerze lokalnym.
2. Jeśli istnieje wpis dotyczący "**agenta programu Microsoft Azure AD Connect uwierzytelniania**", nie musisz podejmować żadnych działań na tym serwerze.
3. Jeśli istnieje wpis dotyczący "**łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD**", wersji 1.5.132.0 lub wcześniej, musisz ręcznie uaktualnić na tym serwerze.

![Wersja zapoznawcza Agent uwierzytelniania](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Najlepsze rozwiązania, które należy wykonać przed rozpoczęciem uaktualniania

Przed rozpoczęciem uaktualniania upewnij się, że masz następujące elementy w miejscu:

1. **Tworzenie konta administratora globalnego tylko w chmurze**: nie uaktualniaj bez konieczności konto administratora globalnego tylko w chmurze, które będzie używane w sytuacji zagrożenia, gdzie agentów uwierzytelniania przekazywanego nie działa prawidłowo. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest krytyczna i zapewnia, że należy przed zablokowaniem dzierżawy.
2.  **Zapewni to wysoką dostępność**: Jeśli wcześniej nie ukończone, należy zainstalować autonomiczny drugi Agent uwierzytelniania, aby zapewnić wysoką dostępność dla żądań logowania, za pomocą tych [instrukcje](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uaktualnianie agenta uwierzytelniania na serwerze programu Azure AD Connect

Przed uaktualnieniem Agent uwierzytelniania na tym samym serwerze, należy uaktualnić Azure AD Connect. Na serwerze podstawowym i przemieszczania serwery usługi Azure AD Connect, wykonaj następujące kroki:

1. **Uaktualnij usługi Azure AD Connect**: po tym [artykułu](./active-directory-aadconnect-upgrade-previous-version.md) i uaktualnienia do najnowszej wersji Azure AD Connect.
2. **Odinstaluj wersję zapoznawczą Agent uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako Administrator na serwerze.
3. **Pobierz najnowszą wersję agenta uwierzytelniania (wersje 1.5.193.0 lub nowszym)**: Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego Twojej dzierżawy. Wybierz **usługi Azure Active Directory -> Azure AD Connect -> uwierzytelniania przekazywanego -> agent pobierania**. Zaakceptuj [warunków użytkowania usługi](https://aka.ms/authagenteula) i pobrać najnowszą wersję agenta uwierzytelniania. Możesz również pobrać agenta uwierzytelniania z [tutaj](https://aka.ms/getauthagent).
4. **Zainstaluj najnowszą wersję agenta uwierzytelniania**: Uruchom plik wykonywalny pobrany w kroku 3. Podaj poświadczenia administratora globalnego Twojej dzierżawy, po wyświetleniu monitu.
5. **Sprawdź, czy zainstalowana najnowsza wersja**: jak pokazano wcześniej, przejdź do **Panel sterowania -> programy -> programy i funkcje** i sprawdź, czy istnieje wpis dotyczący "**Microsoft Azure AD Connect Agent uwierzytelniania**".

>[!NOTE]
>Jeśli wybierzesz bloku uwierzytelniania przekazywanego na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) po ukończeniu poprzedniego kroków, zobaczysz dwa wpisy uwierzytelniania agenta na serwer - jeden wpis przedstawiający Agent uwierzytelniania jako **Active** i inne, **nieaktywne**. Jest to _Oczekiwano_. **Nieaktywne** wpis jest automatycznie usunięte po upływie kilku dni.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uaktualnienie agenta uwierzytelniania na innych serwerach

Wykonaj następujące kroki, aby uaktualnić agentów uwierzytelniania na innych serwerach (gdzie Azure AD Connect nie jest zainstalowany):

1. **Odinstaluj wersję zapoznawczą Agent uwierzytelniania**: Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako Administrator na serwerze.
2. **Pobierz najnowszą wersję agenta uwierzytelniania (wersje 1.5.193.0 lub nowszym)**: Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego Twojej dzierżawy. Wybierz **usługi Azure Active Directory -> Azure AD Connect -> uwierzytelniania przekazywanego -> agent pobierania**. Zaakceptuj warunki świadczenia usługi i Pobierz najnowszą wersję.
3. **Zainstaluj najnowszą wersję agenta uwierzytelniania**: Uruchom plik wykonywalny pobrany w kroku 2. Podaj poświadczenia administratora globalnego Twojej dzierżawy, po wyświetleniu monitu.
4. **Sprawdź, czy zainstalowana najnowsza wersja**: jak pokazano wcześniej, przejdź do **Panel sterowania -> programy -> programy i funkcje** i sprawdź, czy istnieje wpis o nazwie **Microsoft Azure AD Connect Agent uwierzytelniania**.

>[!NOTE]
>Jeśli wybierzesz bloku uwierzytelniania przekazywanego na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) po ukończeniu poprzedniego kroków, zobaczysz dwa wpisy uwierzytelniania agenta na serwer - jeden wpis przedstawiający Agent uwierzytelniania jako **Active** i inne, **nieaktywne**. Jest to _Oczekiwano_. **Nieaktywne** wpis jest automatycznie usunięte po upływie kilku dni.

## <a name="next-steps"></a>Kolejne kroki
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
