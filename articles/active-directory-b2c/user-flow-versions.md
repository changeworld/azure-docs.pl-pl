---
title: Wersje przepływu użytkownika w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o wersjach przepływów użytkowników dostępnych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df411ee288b9759adbb3f8f84c28e4fed05f3c4c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258089"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Wersje przepływu użytkownika w Azure Active Directory B2C

Przepływy użytkowników w Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie typowych [zasad](active-directory-b2c-reference-policies.md) , które w pełni opisują środowiska tożsamości klientów. Te doświadczenia obejmują rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilów. W Azure AD B2C można wybrać jedną z kolekcji zalecanych przepływów użytkowników i Podgląd przepływów użytkowników.

Nowe przepływy użytkowników są dodawane jako nowe wersje. Gdy przepływy użytkowników staną się stabilne, zaleca się ich użycie. Przepływy użytkownika są oznaczane jako **zalecane** , jeśli zostały dokładnie przetestowane. Przepływy użytkowników będą uwzględniane w wersji zapoznawczej, dopóki nie zostanie oznaczone jako zalecane. Użyj zalecanego przepływu użytkownika dla dowolnej aplikacji produkcyjnej, ale wybierz z innych wersji, aby przetestować nowe funkcje, gdy staną się dostępne. Nie należy używać starszych wersji zalecanych przepływów użytkowników.

>[!IMPORTANT]
> Chyba że przepływ użytkownika zostanie zidentyfikowany jako **zalecany**, jest on traktowany jako *wersja zapoznawcza*. Należy używać tylko zalecanych przepływów użytkowników dla aplikacji produkcyjnych.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Tak | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edytowanie profilu | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się przy użyciu przepływu ROPC | Nie | Umożliwia użytkownikowi z kontem lokalnym logowanie się bezpośrednio w aplikacjach natywnych (nie jest wymagana żadna przeglądarka). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li></ul> |
| Logowanie | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>Zablokuj logowanie</li><li>Wymuś Resetowanie hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika przy użyciu tego przepływu użytkownika. |
| Zarejestruj się | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie się | Tak | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na jego koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v11"></a>WERSJA 1.1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła v 1.1 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu ich poczty e-mail (dostępny jest nowy układ strony). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła w wersji 2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edytowanie profilu w wersji 2 | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Logowanie się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Rejestrowanie się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestracja i logowanie w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się do niego. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
