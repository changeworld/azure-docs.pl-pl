---
title: Usługa Azure AD Connect — uwierzytelnianie przekazywane — agenci uwierzytelniania uaktualnienia | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób uaktualniania konfiguracji uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: Uwierzytelnianie przekazywanie usługi Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386775"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Uwierzytelnianie przekazywane usługi Azure Active Directory: agenci uwierzytelniania w wersji zapoznawczej uaktualnienia

## <a name="overview"></a>Omówienie

Ten artykuł jest przeznaczony dla klientów korzystających z uwierzytelniania przekazywania usługi Azure AD w wersji zapoznawczej. Niedawno uaktualniliśmy (i przemianowaliśmy) oprogramowanie Agenta uwierzytelniania. Należy _ręcznie_ uaktualnić agenty uwierzytelniania w wersji zapoznawczej zainstalowane na serwerach lokalnych. To ręczne uaktualnianie jest tylko jednorazową akcją. Wszystkie przyszłe aktualizacje agentów uwierzytelniania są automatyczne. Powody uaktualnienia są następujące:

- Wersje zapoznawcze agentów uwierzytelniania nie otrzymają żadnych dalszych poprawek zabezpieczeń ani błędów.
-   Nie można zainstalować wersji zapoznawców agentów uwierzytelniania na dodatkowych serwerach, aby uzyskać wysoką dostępność.

## <a name="check-versions-of-your-authentication-agents"></a>Sprawdź wersje agentów uwierzytelniania

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Sprawdź, gdzie są zainstalowane twoje agenty uwierzytelniania

Wykonaj następujące kroki, aby sprawdzić, gdzie są zainstalowane agenci uwierzytelniania:

1. Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. Wybierz **usługę Azure Active Directory** w nawigacji po lewej stronie.
3. Wybierz **usługę Azure AD Connect**. 
4. Wybierz **pozycję Uwierzytelnianie przekazywane**. Ten blok zawiera listę serwerów, na których są zainstalowane agenci uwierzytelniania.

![Centrum administracyjne usługi Azure Active Directory — narzędzie Uwierzytelniania przekazywanego](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Sprawdź wersje agentów uwierzytelniania

Aby sprawdzić wersje agentów uwierzytelniania, na każdym serwerze określonym w poprzednim kroku wykonaj następujące instrukcje:

1. Przejdź do **panelu sterowania -> Programy -> programy i funkcje** na serwerze lokalnym.
2. Jeśli istnieje wpis dla "**Agent uwierzytelniania usługi Microsoft Azure AD Connect**", nie trzeba podejmować żadnych działań na tym serwerze.
3. Jeśli istnieje wpis dla "**Łącznik serwera proxy aplikacji usługi Microsoft Azure AD**— należy ręcznie uaktualnić na tym serwerze.

![Wersja zapoznawcza agenta uwierzytelniania](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Najważniejsze wskazówki, które należy wykonać przed rozpoczęciem uaktualnienia

Przed uaktualnieniem upewnij się, że masz na miejscu następujące elementy:

1. **Utwórz konto administratora globalnego tylko w chmurze:** nie uaktualniaj bez konta administratora globalnego tylko w chmurze, które będzie używane w sytuacjach awaryjnych, w których agenci uwierzytelniania przekazowego nie działają poprawnie. Dowiedz się więcej o [dodawaniu konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku jest krytyczne i zapewnia, że nie zostaniesz zablokowany z dzierżawy.
2.  **Zapewnij wysoką dostępność:** Jeśli wcześniej nie została ukończona, zainstaluj drugiego autonomicznego agenta uwierzytelniania, aby zapewnić wysoką dostępność żądań logowania, korzystając z tych [instrukcji.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uaktualnianie agenta uwierzytelniania na serwerze usługi Azure AD Connect

Musisz uaktualnić usługę Azure AD Connect przed uaktualnieniem agenta uwierzytelniania na tym samym serwerze. Wykonaj następujące kroki zarówno na podstawowych, jak i przemieszczania serwerów usługi Azure AD Connect:

1. **Uaktualnienie usługi Azure AD Connect:** Wykonaj ten [artykuł](how-to-upgrade-previous-version.md) i uaktualnij do najnowszej wersji usługi Azure AD Connect.
2. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania:** Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako administrator na serwerze.
3. **Pobierz najnowszą wersję agenta uwierzytelniania (wersja 1.5.389.0 lub nowsza)**: Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz **agenta pobierania usługi Azure Active Directory -> Azure AD Connect -> -> Download Agent**. Zaakceptuj [warunki korzystania z usługi](https://aka.ms/authagenteula) i pobierz najnowszą wersję agenta uwierzytelniania. Możesz również pobrać agenta uwierzytelniania [tutaj](https://aka.ms/getauthagent).
4. **Zainstaluj najnowszą wersję agenta uwierzytelniania:** Uruchom plik wykonywalny pobrany w kroku 3. Po wyświetleniu monitu podaj poświadczenia administratora globalnego dzierżawy.
5. **Sprawdź, czy zainstalowano najnowszą wersję:** Jak pokazano wcześniej, przejdź do **Panelu sterowania -> Programy -> programy i funkcje** i sprawdź, czy istnieje wpis dla "**Agent uwierzytelniania Usługi Microsoft Azure AD Connect**".

>[!NOTE]
>Jeśli po wykonaniu powyższych kroków sprawdzisz blok uwierzytelniania przekazywanego w [centrum administracyjnym usługi Azure Active Directory,](https://aad.portal.azure.com) zobaczysz dwa wpisy Agenta uwierzytelniania na serwer — jeden wpis przedstawiający Agenta uwierzytelniania jako **aktywnego,** a drugi jako **nieaktywny.** Jest to _oczekiwane_. Wpis **Nieaktywny** jest automatycznie usuwany po kilku dniach.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uaktualnianie agenta uwierzytelniania na innych serwerach

Wykonaj następujące kroki, aby uaktualnić agentów uwierzytelniania na innych serwerach (gdzie usługa Azure AD Connect nie jest zainstalowana):

1. **Odinstaluj wersję zapoznawczą agenta uwierzytelniania:** Pobierz [ten skrypt programu PowerShell](https://aka.ms/rmpreviewagent) i uruchom go jako administrator na serwerze.
2. **Pobierz najnowszą wersję agenta uwierzytelniania (wersja 1.5.389.0 lub nowsza)**: Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dzierżawy. Wybierz **agenta pobierania usługi Azure Active Directory -> Azure AD Connect -> -> Download Agent**. Zaakceptuj warunki korzystania z usługi i pobierz najnowszą wersję.
3. **Zainstaluj najnowszą wersję agenta uwierzytelniania:** Uruchom plik wykonywalny pobrany w kroku 2. Po wyświetleniu monitu podaj poświadczenia administratora globalnego dzierżawy.
4. **Sprawdź, czy zainstalowano najnowszą wersję**: Jak pokazano wcześniej, przejdź do **Panelu sterowania -> Programy -> programy i funkcje** i sprawdź, czy istnieje wpis o nazwie **Agent uwierzytelniania Usługi Microsoft Azure AD Connect**.

>[!NOTE]
>Jeśli po wykonaniu powyższych kroków sprawdzisz blok uwierzytelniania przekazywanego w [centrum administracyjnym usługi Azure Active Directory,](https://aad.portal.azure.com) zobaczysz dwa wpisy Agenta uwierzytelniania na serwer — jeden wpis przedstawiający Agenta uwierzytelniania jako **aktywnego,** a drugi jako **nieaktywny.** Jest to _oczekiwane_. Wpis **Nieaktywny** jest automatycznie usuwany po kilku dniach.

## <a name="next-steps"></a>Następne kroki
- [**Rozwiązywanie problemów**](tshoot-connect-pass-through-authentication.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
