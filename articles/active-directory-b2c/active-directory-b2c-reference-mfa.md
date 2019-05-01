---
title: Uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak włączyć uwierzytelnianie wieloskładnikowe w aplikacjach udostępnianych klientom zabezpieczonej przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6cd9cdd2a63b91d0b72a73ba65f4fff87d2a3922
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715754"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory B2C

Usługa Azure Active Directory (Azure AD) B2C jest zintegrowany bezpośrednio z [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, aby dodać drugą warstwę zabezpieczeń do środowiska rejestracji i logowania w aplikacjach. Możesz włączyć uwierzytelnianie wieloskładnikowe, bez konieczności pisania nawet jednego wiersza kodu. Jeśli utworzono już logowanie się i przepływy użytkownika do logowania, nadal można włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja pomaga obsługiwać scenariusze takie jak następujące:

- Nie wymaga uwierzytelniania wieloskładnikowego, dostępu do jednej aplikacji, ale potrzebujesz dostępu do innego. Na przykład klient może zalogować się do aplikacji ubezpieczenia automatycznie za pomocą kont społecznościowych lub lokalnym, ale należy zweryfikować numer telefonu, zanim uzyskujących dostęp do głównego aplikacji ubezpieczeniowe zarejestrowane w tym samym katalogu.
- Nie wymaga uwierzytelniania wieloskładnikowego, uzyskać dostęp do aplikacji, ogólnie rzecz biorąc, ale potrzebujesz dostępu do poufnych fragmenty w nim. Na przykład klient zalogować się do aplikacji bankowości z ubezpieczenia lub konta lokalnego i sprawdź saldo, ale należy zweryfikować numer telefonu przed podjęciem próby wykonania polecenie przelewu bankowego.

## <a name="set-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe zestawu

Po utworzeniu przepływu użytkownika, masz opcję, aby włączyć uwierzytelnianie wieloskładnikowe.

![Uwierzytelnianie wieloskładnikowe zestawu](./media/active-directory-b2c-reference-mfa/add-policy.png)

Ustaw **uwierzytelnianie wieloskładnikowe** do **włączone**.

Możesz użyć **uruchomić przepływ użytkownika** Aby sprawdzić środowisko. Upewnij się, następujący scenariusz:

Konto klienta jest tworzony w Twojej dzierżawie, przed występuje w kroku uwierzytelniania wieloskładnikowego. Podczas wykonywania kroku klienta jest proszony o podanie numeru telefonu i weryfikować ją. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączony do konta w celu późniejszego użycia. Nawet jeśli klient anuluje lub umieszcza, klient może pojawić się monit Sprawdź numer telefonu ponownie podczas następnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.

## <a name="add-multi-factor-authentication"></a>Dodawanie usługi Multi-Factor authentication

Istnieje możliwość włączyć uwierzytelnianie wieloskładnikowe na przepływ użytkownika, która została wcześniej utworzona. 

Aby włączyć uwierzytelnianie wieloskładnikowe:

1. Otwórz przepływ użytkownika, a następnie wybierz pozycję **właściwości**. 
2. Obok pozycji **uwierzytelnianie wieloskładnikowe**, wybierz opcję **włączone**.
3. Kliknij przycisk **Zapisz** w górnej części strony.


