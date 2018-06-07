---
title: 'Synchronizacja programu Azure AD Connect: Zmiana konta usługi Azure AD Connect synchronizacji | Dokumentacja firmy Microsoft'
description: Dokument ten temat opisuje oraz klucz szyfrowania został on porzucony po zmianie hasła.
services: active-directory
keywords: Konto usługi synchronizacji programu Azure AD, hasło
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7b1a3ef9205f46f2f03b6201c72c21c8caafa405
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593320"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Zmiana hasła konta usługi synchronizacji Azure AD Connect
Jeśli zmienisz hasło konta usługi synchronizacji Azure AD Connect, usługi synchronizacji nie będzie możliwe start poprawnie dopiero po klucz szyfrowania porzucone i ponownie zainicjować hasło konta usługi synchronizacji Azure AD Connect. 

Azure AD Connect, jako część usługi synchronizacji przechowuje klucz szyfrowania hasła kont usługi AD DS i Azure AD.  Konta te są szyfrowane przed są przechowywane w bazie danych. 

Klucz szyfrowania używany jest zabezpieczone przy użyciu [ochrony danych systemu Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI chroni szyfrowania klucza przy użyciu **hasło konta usługi synchronizacji programu Azure AD Connect**. 

Jeśli musisz zmienić hasło konta usługi można użyć procedury [porzucanie klucz szyfrowania synchronizacji Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) w tym celu.  Te procedury należy również Jeśli musisz porzucić klucz szyfrowania dla dowolnej przyczyny.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemy, które wynikają z zmiany hasła
Istnieją dwie czynności, które należy wykonać, gdy zmieniasz hasło konta usługi.

Najpierw należy zmienić hasło w obszarze Menedżer sterowania usługami systemu Windows.  Dopóki ten problem zostanie rozwiązany, zostaną wyświetlone następujące błędy:


- Jeśli użytkownik próbuje uruchomić usługę synchronizacji w Menedżera sterowania usługami systemu Windows, zostanie wyświetlony błąd "**systemu Windows nie można uruchomić usługi Microsoft Azure AD Sync na komputerze lokalnym**". **Błąd 1069: Usługa nie została uruchomiona z powodu niepowodzenia logowania.** "
- W Podglądzie zdarzeń systemu Windows, w dzienniku zdarzeń systemowych zawiera błąd **7038 identyfikator zdarzenia** i komunikatem "**usługi ADSync nie może zalogować się zgodnie z aktualnie skonfigurowanym hasłem z powodu następującego błędu: użytkownik Nazwa lub hasło jest niepoprawne.** "

Drugie określonych warunkach, jeśli hasło zostanie zaktualizowane, usługa synchronizacji można już pobierać klucz szyfrowania za pośrednictwem DPAPI. Bez klucza szyfrowania usługi synchronizacji nie można odszyfrować hasła wymagane do synchronizacji z lokalnej usługi AD i Azure AD.
Zostaną wyświetlone błędy takie jak:

- W obszarze Menedżer sterowania usługami systemu Windows, jeśli użytkownik próbuje uruchomić usługę synchronizacji i nie może pobrać klucza szyfrowania go zakończy się niepowodzeniem z powodu błędu "** systemu Windows nie można uruchomić programu Microsoft Azure AD Sync na komputerze lokalnym. Aby uzyskać więcej informacji przejrzyj dziennik zdarzeń systemu. Jeśli jest to usługa firmy Microsoft, skontaktuj się z dostawcą usługi i zapoznaj się kod błędu usługi **-21451857952 ***. "
- W Podglądzie zdarzeń systemu Windows, w dzienniku zdarzeń aplikacji zawiera błąd **6028 identyfikator zdarzenia** i komunikat o błędzie *"**nie można uzyskać dostępu do klucza szyfrowania serwera.**"*

Aby upewnić się, że nie będą odbierać te błędy, wykonaj procedury przedstawione w [porzucanie klucz szyfrowania synchronizacji Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) po zmianie hasła.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Porzucanie klucz szyfrowania synchronizacji Azure AD Connect
>[!IMPORTANT]
>Poniższe procedury dotyczą tylko usługi Azure AD Connect kompilacji 1.1.443.0 lub starszy.

Użyj następujących procedur, aby porzucić klucz szyfrowania.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co zrobić, jeśli musisz porzucić klucz szyfrowania

Jeśli musisz porzucić klucz szyfrowania, w tym celu należy użyć poniższych procedur.

1. [Porzuć istniejący klucz szyfrowania](#abandon-the-existing-encryption-key)

2. [Podaj hasło do konta usług AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Zainicjuj ponownie hasło do konta synchronizacji usługi Azure AD](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Uruchom usługę synchronizacji](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Porzuć istniejący klucz szyfrowania
Porzuć istniejący klucz szyfrowania, można utworzyć tego nowego klucza szyfrowania:

1. Zaloguj się do usługi Azure AD Connect serwera jako administrator.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Uruchom polecenie: `./miiskmu.exe /a`

![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Podaj hasło do konta usług AD DS
Jak istniejące hasła przechowywane w bazie danych nie można odszyfrować, musisz podać usługi synchronizacji z hasłem konta usług AD DS. Usługa synchronizacji szyfruje hasła przy użyciu nowego klucza szyfrowania:

1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Przejdź do **łączniki** kartę.
3. Wybierz **łącznika AD** odpowiadający lokalnej usługi AD. Jeśli masz więcej niż jeden łącznik AD, powtórz następujące kroki dla każdego z nich.
4. W obszarze **akcje**, wybierz pozycję **właściwości**.
5. W oknie podręcznym wybierz **Połącz z lasu usługi Active Directory**:
6. Wprowadź hasło do konta usług AD DS w **hasło** pola tekstowego. Jeśli nie znasz swojego hasła, należy ustawić go do żadnej znanej wartości, przed wykonaniem tej czynności.
7. Kliknij przycisk **OK** Aby zapisać nowe hasło i zamknąć okno podręczne.
![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Zainicjuj ponownie hasło do konta synchronizacji usługi Azure AD
Nie można bezpośrednio Podaj hasło konta usługi Azure AD do usługi synchronizacji. Zamiast tego należy użyć polecenia cmdlet **ADSyncAADServiceAccount Dodaj** może ponownie zainicjować konta usługi Azure AD. Polecenia cmdlet resetuje hasło do konta i udostępnia usługi synchronizacji:

1. Rozpocznij nową sesję programu PowerShell na serwerze programu Azure AD Connect.
2. Uruchom polecenie cmdlet `Add-ADSyncAADServiceAccount`.
3. W wyskakującym oknie dialogowym podaj poświadczenia administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD.
![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Jeśli ten zakończy się powodzeniem, zostanie wyświetlony wiersz polecenia programu PowerShell.

#### <a name="start-the-synchronization-service"></a>Uruchom usługę synchronizacji
Usługa synchronizacji uzyskuje dostęp do klucza szyfrowania i hasła, które są niezbędne, aby uruchomić usługę w Menedżerze sterowania usługami systemu Windows:


1. Przejdź do systemu Windows Menedżera sterowania usługami (usługi → START).
2. Wybierz **Microsoft Azure AD Sync** i kliknij przycisk Uruchom ponownie.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
