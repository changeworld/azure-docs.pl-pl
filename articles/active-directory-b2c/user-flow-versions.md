---
title: Użytkownik wersji przepływów w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wersjach przepływy użytkownika są dostępne w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b3750768eb7346938faec7e3c1ead232923a68bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359759"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Użytkownik wersji przepływów w usłudze Azure Active Directory B2C

>[!IMPORTANT]
> Dowolny przepływ użytkownika wymienionych w artykule uznaje się znajdować się w publicznej wersji zapoznawczej, chyba że zidentyfikowane jako **zalecane**. Przepływy użytkownika zalecane należy używać tylko dla aplikacji produkcyjnych.

Przepływy użytkownika w usłudze Azure Active Directory (Azure AD) B2C ułatwia konfigurowanie typowych [zasady](active-directory-b2c-reference-policies.md) pełni opisują środowiska tożsamości klienta. Te doświadczenia obejmują resetowania haseł rejestracji, logowania, lub edytowania profilu. W usłudze Azure AD B2C możesz wybrać z kolekcji przepływy użytkownika zalecane i przepływy użytkownika (wersja zapoznawcza). 

Nowych przepływów użytkownika są dodawane jako nowe wersje. W miarę stabilne przepływy użytkownika będzie mieć zaleceń do użycia. Przepływy użytkownika są oznaczone jako **zalecane** Jeśli już została dokładnie przetestowana. Przepływy użytkownika zostanie uwzględniony w wersji zapoznawczej, dopóki nie oznaczone zgodnie z zaleceniami. Za pomocą przepływu użytkownika zalecane dla dowolnej aplikacji w środowisku produkcyjnym, ale wybierz pozycję z innych wersji do testowania nowych funkcji po jej udostępnieniu. Nie należy używać starszych wersji przepływów użytkownika zalecane.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Yes | Umożliwia użytkownikowi wybranie nowego hasła po sprawdzeniu swojej poczty e-mail. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Edytowanie profilu | Yes | Umożliwia użytkownikowi konfigurowanie ich atrybuty użytkownika. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li></ul> |
| właściciel zasobu | Nie | Umożliwia użytkownikowi zalogowanie się bezpośrednio w aplikacji natywnych (przeglądarka nie jest wymagane) za pomocą konta lokalnego. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li></ul> |
| Logowanie | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>Blokuj logowanie</li><li>Wymuszenie zresetowania hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika za pomocą tego przepływu użytkownika. |
| Rejestrowanie | Nie | Umożliwia użytkownikowi tworzenie konta usługi. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie | Yes | Umożliwia użytkownikowi tworzenie konta usługi lub zalogowanie się do swojego konta. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła w wersji 2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po sprawdzeniu swojej poczty e-mail. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Logowanie się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wieku](basic-age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Rejestrowanie się w wersji 2 | Nie | Umożliwia użytkownikowi tworzenie konta usługi. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Okres istnienia tokenu](active-directory-b2c-reference-tokens.md)</li><li>Ustawienia zgodności tokenu</li><li>Działanie sesji</li><li>[Wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie się w wersji 2 | Nie | Umożliwia użytkownikowi tworzenie konta lub logowania się na jego koncie. Za pomocą tego przepływu użytkownika, można skonfigurować: <ul><li>[Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)</li><li>[Wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](active-directory-b2c-reference-password-complexity.md)</li></ul> |
