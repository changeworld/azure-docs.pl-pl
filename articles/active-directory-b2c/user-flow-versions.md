---
title: Wersje przepływu użytkownika w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się więcej o wersjach przepływów użytkowników dostępnych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185625"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Wersje przepływu użytkownika w usłudze Azure Active Directory B2C

Przepływy użytkowników w usłudze Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie wspólnych [zasad,](user-flow-overview.md) które w pełni opisują środowiska tożsamości klienta. Te środowiska obejmują rejestrację, logowanie się, resetowanie hasła lub edycję profilu. W usłudze Azure AD B2C można wybrać z kolekcji zarówno zalecane przepływy użytkownika i przepływy użytkownika w wersji zapoznawczej.

Nowe przepływy użytkowników są dodawane jako nowe wersje. W miarę jak przepływy użytkowników stają się stabilne, będą one zalecane do użycia. Przepływy użytkownika są oznaczone jako **zalecane,** jeśli zostały dokładnie przetestowane. Przepływy użytkownika będą uwzględniane w wersji zapoznawczej, dopóki nie zostaną oznaczone jako zalecane. Użyj zalecanego przepływu użytkownika dla dowolnej aplikacji produkcyjnej, ale wybierz jedną z innych wersji, aby przetestować nowe funkcje, gdy staną się dostępne. Nie należy używać starszych wersji zalecanych przepływów użytkownika.

>[!IMPORTANT]
> O ile przepływ użytkownika nie zostanie zidentyfikowany jako **zalecany,** uważa się, że jest on w *wersji zapoznawczej.* Należy używać tylko zalecane przepływy użytkownika dla aplikacji produkcyjnych.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Tak | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu adresu e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenów</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Edycja profilu | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się przy użyciu ROPC | Nie | Umożliwia użytkownikowi z kontem lokalnym logowanie się bezpośrednio w aplikacjach natywnych (nie jest wymagana przeglądarka). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li></ul> |
| Logowanie | Nie | Umożliwia użytkownikowi zalogowanie się na swoje konto. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li><li>Blokowanie logowania</li><li>Wymuś zresetowanie hasła</li><li>Keep Me Signed In (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika za pomocą tego przepływu użytkownika. |
| Rejestrowanie | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie | Tak | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na swoje konto. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>Wersja 1.1.

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła w wersji 1.1 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu adresu e-mail (dostępny jest nowy układ strony). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenów</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła w wersji 2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu adresu e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenów</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Edycja profilu v2 | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się na swoje konto. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Zarejestruj się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenów</li><li>Zachowanie sesji</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Zarejestruj się i zaloguj się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na swoim koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
