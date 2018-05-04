---
title: 'Azure Active Directory B2C: Uwierzytelnianie wieloskładnikowe | Dokumentacja firmy Microsoft'
description: Jak włączyć uwierzytelnianie wieloskładnikowe w aplikacjach użytkownika zabezpieczonej przez usługi Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f439c65d9b4fbeb78623312c426767f89dfd9fab
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Usługa Azure Active Directory B2C: Włączanie uwierzytelniania wieloskładnikowego w aplikacjach dla użytkownika
Usługa Azure Active Directory (Azure AD) B2C integruje się bezpośrednio z [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) , aby mogli dodawać drugą warstwę zabezpieczeń do rejestracji i logowania w aplikacjach dla użytkownika. I można to zrobić bez pisania pojedynczy wiersz kodu. Obecnie firma Microsoft obsługuje połączenia telefonicznego i tekst komunikatu weryfikacji. Jeśli utworzono już zasad rejestracji i logowania, nadal można włączyć usługę Multi-Factor Authentication.

> [!NOTE]
> Uwierzytelnianie wieloskładnikowe można włączyć również podczas tworzenia zasad rejestracji i logowania, nie tylko przez edytowania istniejących zasad.
> 
> 

Ta funkcja umożliwia aplikacji obsługi scenariuszy, takich jak następujące:

* Nie wymaga uwierzytelniania wieloskładnikowego, dostępu do jednej aplikacji, ale potrzebna dostęp do innego. Na przykład konsumenta można zalogować się do aplikacji ubezpieczenia automatycznie z kontem społecznościowych, ani w lokalnym, ale należy zweryfikować numer telefonu, przed zarejestrowaniem dostęp macierzystego ubezpieczenia aplikacji w tym samym katalogu.
* Nie wymaga uwierzytelniania wieloskładnikowego, dostępu do aplikacji ogólnie rzecz biorąc, ale potrzebna dostęp do poufnych fragmentów w niej. Na przykład konsumenta można zalogować się do aplikacji bankowości społecznościowych lub lokalne konto i sprawdź stan konta, ale należy zweryfikować numer telefonu przed podjęciem próby przeniesienia danych przesyłanych w sieci.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Zmodyfikuj zasady rejestracji, aby włączyć uwierzytelnianie wieloskładnikowe
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w portalu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **Zasady tworzenia konta**.
3. Kliknij zasady rejestracji (na przykład "B2C_1_SiUp"), aby go otworzyć.
4. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** i włączyć **stanu** do **ON**. Kliknij przycisk **OK**.
5. Kliknij przycisk **zapisać** w górnej części bloku.

Za pomocą funkcji "Uruchom teraz" na zasady można sprawdzić w środowisku użytkownika. Upewnij się, że:

Konto użytkownika pobiera tworzone w katalogu przed wystąpieniem tego kroku usługa Multi-Factor Authentication. Podczas wykonywania kroku użytkownik jest proszony o Podaj numer telefonu i jego potwierdzenie. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączany do konta użytkownika do późniejszego użycia. Nawet wtedy, gdy użytkownik anuluje lub spadnie, użytkownik może otrzymać monit o zweryfikowanie numeru telefonu ponownie podczas następnego logowania (przy użyciu uwierzytelniania wieloskładnikowego włączona).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Zmodyfikuj zasady logowania, aby włączyć uwierzytelnianie wieloskładnikowe
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w portalu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **zasad logowania**.
3. Kliknij przycisk logowania zasady (na przykład "B2C_1_SiIn"), aby go otworzyć. Kliknij przycisk **Edytuj** w górnej części bloku.
4. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** i włączyć **stanu** do **ON**. Kliknij przycisk **OK**.
5. Kliknij przycisk **zapisać** w górnej części bloku.

Za pomocą funkcji "Uruchom teraz" na zasady można sprawdzić w środowisku użytkownika. Upewnij się, że:

Gdy konsumenta loguje się (przy użyciu konta społecznościowych lub lokalną), jeśli numer telefonu zweryfikowano jest dołączany do konta użytkownika, użytkownik jest monitowany o ją zweryfikować. Jeśli bez numeru telefonu nie jest podłączone, użytkownik jest proszony o Podaj jeden i jego potwierdzenie. Pomyślnie weryfikację numer telefonu jest dołączony do konta użytkownika do późniejszego użycia.

## <a name="multi-factor-authentication-on-other-policies"></a>Uwierzytelnianie wieloskładnikowe na inne zasady
Zgodnie z opisem zasad rejestracji i logowania powyżej, jest również można włączyć uwierzytelnianie wieloskładnikowe na rejestracji lub zasad logowania i hasło zresetowanie zasad. Będzie dostępna wkrótce w profilu edytowanie zasad.

