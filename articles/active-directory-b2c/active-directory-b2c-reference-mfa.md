---
title: Uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak włączyć uwierzytelnianie wieloskładnikowe w aplikacjach udostępnianych klientom zabezpieczonej przez usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3d18e1b2e45aba4e83989e29c533cfc7bf5033fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442712"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Usługa Azure Active Directory B2C: Włączanie uwierzytelniania wieloskładnikowego w aplikacjach przeznaczonych dla konsumentów
Usługa Azure Active Directory (Azure AD) B2C jest zintegrowany bezpośrednio z [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) tak, aby dodać drugą warstwę zabezpieczeń do środowiska rejestracji i logowania w aplikacjach udostępnianych klientom. I można to zrobić bez konieczności pisania nawet jednego wiersza kodu. Obecnie obsługujemy połączeń telefonicznych i tekst Weryfikacja komunikatu. Jeśli już utworzono zasady rejestracji i logowania, nadal można włączyć uwierzytelnianie wieloskładnikowe.

> [!NOTE]
> Uwierzytelnianie wieloskładnikowe można włączyć również podczas tworzenia zasad rejestracji i logowania, nie wystarczy edytowania istniejących zasad.
> 
> 

Ta funkcja pomaga obsługiwać scenariusze takie jak następujące:

* Nie wymaga uwierzytelniania wieloskładnikowego, dostępu do jednej aplikacji, ale potrzebujesz dostępu do innej. Na przykład użytkownik może zalogować się do aplikacji ubezpieczenia automatycznie za pomocą kont społecznościowych lub lokalnym, ale należy zweryfikować numer telefonu, zanim uzyskujących dostęp do głównego aplikacji ubezpieczeniowe zarejestrowane w tym samym katalogu.
* Nie wymaga uwierzytelniania wieloskładnikowego, uzyskać dostęp do aplikacji, ogólnie rzecz biorąc, ale potrzebujesz dostępu do poufnych fragmenty w nim. Na przykład konsumenta zalogować się do aplikacji bankowe za pomocą kont społecznościowych lub lokalnym i sprawdź saldo konta, ale należy sprawdzić numer telefonu przed podjęciem próby wykonania polecenie przelewu bankowego.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Zmodyfikuj zasady tworzenia konta, aby włączyć uwierzytelnianie wieloskładnikowe
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w witrynie Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **Zasady tworzenia konta**.
3. Kliknij zasady tworzenia konta (na przykład "B2C_1_SiUp"), aby go otworzyć.
4. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** i włączyć **stanu** do **ON**. Kliknij przycisk **OK**.
5. Kliknij przycisk **Zapisz** w górnej części bloku.

Funkcja "Uruchom teraz" w zasadach umożliwia Sprawdź środowiska klienta. Upewnij się, że:

Konto konsumenta tworzony w katalogu, zanim nastąpi w kroku uwierzytelniania wieloskładnikowego. Podczas wykonywania kroku użytkownik jest proszony o Podaj numer telefonu i weryfikować ją. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączony do konta klienta w celu późniejszego użycia. Nawet jeśli użytkownik anuluje lub umieszcza, użytkownik może otrzymać monit o zweryfikowanie numeru telefonu ponownie podczas następnego logowania (z włączonym uwierzytelnianiem wieloskładnikowym).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Zmodyfikuj zasady logowania, aby włączyć uwierzytelnianie wieloskładnikowe
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w witrynie Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **zasady logowania**.
3. Kliknij przycisk logowania zasady (na przykład "B2C_1_SiIn"), aby go otworzyć. Kliknij przycisk **Edytuj** w górnej części bloku.
4. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** i włączyć **stanu** do **ON**. Kliknij przycisk **OK**.
5. Kliknij przycisk **Zapisz** w górnej części bloku.

Funkcja "Uruchom teraz" w zasadach umożliwia Sprawdź środowiska klienta. Upewnij się, że:

Po użytkownik loguje się (przy użyciu kont społecznościowych lub lokalne), jeśli numer telefonu zweryfikowanych jest dołączony do konta klienta, użytkownik jest proszony o weryfikacji. Jeśli jest dołączony bez numeru telefonu, użytkownik jest proszony o podać jedną i weryfikować ją. Pomyślnie włączono weryfikację numer telefonu jest dołączony do konta klienta w celu późniejszego użycia.

## <a name="multi-factor-authentication-on-other-policies"></a>Uwierzytelnianie wieloskładnikowe na inne zasady
Zgodnie z opisem powyżej zasady rejestracji i logowania, użytkownik może również włączyć uwierzytelnianie wieloskładnikowe na rejestracji lub zasady resetowania zasady logowania i hasło. Będzie on dostępny wkrótce na zasady edytowania profilu.

