---
title: Wersje przepływu użytkownika w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o wersjach przepływów użytkowników dostępnych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7b3ce6a4e72b90c6fd642b92d4a92eb02f4c92c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063199"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Wersje przepływu użytkownika w Azure Active Directory B2C

>[!IMPORTANT]
> Każdy przepływ użytkownika wymieniony w artykule jest traktowany jako publiczna wersja zapoznawcza, chyba że zostanie określony jako **zalecany**. Zalecanych przepływów użytkowników należy używać tylko dla aplikacji produkcyjnych.

Przepływy użytkowników w Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie typowych [zasad](active-directory-b2c-reference-policies.md) , które w pełni opisują środowiska tożsamości klientów. Te doświadczenia obejmują rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilów. W Azure AD B2C można wybrać jedną z kolekcji zalecanych przepływów użytkowników i Podgląd przepływów użytkowników.

Nowe przepływy użytkowników są dodawane jako nowe wersje. Gdy przepływy użytkowników staną się stabilne, zaleca się ich użycie. Przepływy użytkownika są oznaczane jako **zalecane** , jeśli zostały dokładnie przetestowane. Przepływy użytkowników będą uwzględniane w wersji zapoznawczej, dopóki nie zostanie oznaczone jako zalecane. Użyj zalecanego przepływu użytkownika dla dowolnej aplikacji produkcyjnej, ale wybierz z innych wersji, aby przetestować nowe funkcje, gdy staną się dostępne. Nie należy używać starszych wersji zalecanych przepływów użytkowników.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Tak | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edytowanie profilu | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li></ul> |
| Właściciel zasobu | Nie | Umożliwia użytkownikowi z kontem lokalnym logowanie się bezpośrednio w aplikacjach natywnych (nie jest wymagana żadna przeglądarka). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li></ul> |
| Logowanie | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>Zablokuj logowanie</li><li>Wymuś Resetowanie hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika przy użyciu tego przepływu użytkownika. |
| Zarejestruj się | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie się | Tak | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na jego koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła w wersji 2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Logowanie się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Rejestrowanie się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestracja i logowanie w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się do niego. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Kontroli wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
