---
title: Uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak włączyć uwierzytelnianie wieloskładnikowe w aplikacjach udostępnianych klientom zabezpieczonej przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014010"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C jest zintegrowany bezpośrednio z [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, aby dodać drugą warstwę zabezpieczeń do środowiska rejestracji i logowania w aplikacjach. Możesz włączyć uwierzytelnianie wieloskładnikowe, bez konieczności pisania nawet jednego wiersza kodu. Jeśli została już utworzona logowanie się i zasad logowania, można włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja pomaga obsługiwać scenariusze takie jak następujące:

- Nie wymaga uwierzytelniania wieloskładnikowego, dostępu do jednej aplikacji, ale potrzebujesz dostępu do innego. Na przykład klient może zalogować się do aplikacji ubezpieczenia automatycznie za pomocą kont społecznościowych lub lokalnym, ale należy zweryfikować numer telefonu, zanim uzyskujących dostęp do głównego aplikacji ubezpieczeniowe zarejestrowane w tym samym katalogu.
- Nie wymaga uwierzytelniania wieloskładnikowego, uzyskać dostęp do aplikacji, ogólnie rzecz biorąc, ale potrzebujesz dostępu do poufnych fragmenty w nim. Na przykład klient zalogować się do aplikacji bankowości z ubezpieczenia lub konta lokalnego i sprawdź saldo, ale należy zweryfikować numer telefonu przed podjęciem próby wykonania polecenie przelewu bankowego.

## <a name="set-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe zestawu

Po utworzeniu zasad, masz opcję, aby włączyć uwierzytelnianie wieloskładnikowe.

![Uwierzytelnianie wieloskładnikowe zestawu](./media/active-directory-b2c-reference-mfa/add-policy.png)

Ustaw **stanu** do **na**.

Możesz użyć **Uruchom teraz** zasady Aby sprawdzić środowisko. Upewnij się, następujący scenariusz:

Konto klienta jest tworzony w Twojej dzierżawie, przed występuje w kroku uwierzytelniania wieloskładnikowego. Podczas wykonywania kroku klienta jest proszony o podanie numeru telefonu i weryfikować ją. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączony do konta w celu późniejszego użycia. Nawet jeśli klient anuluje lub umieszcza, klient może pojawić się monit Sprawdź numer telefonu ponownie podczas następnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.

## <a name="add-multi-factor-authentication"></a>Dodawanie usługi Multi-Factor authentication

Istnieje możliwość włączyć uwierzytelnianie wieloskładnikowe na zasadach, która została wcześniej utworzona. 

Aby włączyć uwierzytelnianie wieloskładnikowe:

1. Otwórz zasady a następnie wybierz pozycję **Edytuj**. 
2. Wybierz **uwierzytelnianie wieloskładnikowe**
3. Ustaw **stanu** do **na**.
4. Kliknij przycisk **Zapisz** w górnej części strony.


