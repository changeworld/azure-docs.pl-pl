---
title: 'Synchronizacja programu Azure AD Connect:  Zmiana konta usługi ADSync | Dokumentacja firmy Microsoft'
description: W tym dokumencie tematu opisano klucza szyfrowania oraz jak je porzucić, po zmianie hasła.
services: active-directory
keywords: Konto usługi synchronizacji programu Azure AD, hasło
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077671ab4e964d7641aa3a0f0b435b39117eb6aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139391"
---
# <a name="changing-the-adsync-service-account-password"></a>Zmienianie hasła konta usługi ADSync
Jeśli zmienisz hasło do konta usługi ADSync, usługa synchronizacji nie będzie możliwe start poprawnie dopiero po przeprowadzeniu zostały porzucone klucza szyfrowania i ponownie zainicjować hasło do konta usługi ADSync. 

Azure AD Connect, jako część usługi synchronizacji przechowuje klucz szyfrowania hasła konta usługi AD DS łącznika i konto usługi ADSync.  Te konta są szyfrowane, zanim są przechowywane w bazie danych. 

Klucz szyfrowania używany jest zabezpieczone przy użyciu [Windows ochrony danych (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI chroni do szyfrowania klucza przy użyciu **konta usługi ADSync**. 

Jeśli musisz zmienić hasło do konta usługi można użyć procedury opisane w [porzucenie klucz szyfrowania konta usługi ADSync](#abandoning-the-adsync-service-account-encryption-key) w tym celu.  Te procedury również należy używać, gdy musisz porzucić klucza szyfrowania dla jakiegokolwiek powodu.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemy, które wynikają z zmiana hasła
Istnieją dwie czynności, które należy wykonać, gdy zmienisz hasło do konta usługi.

Najpierw należy zmienić hasło w obszarze Menedżer sterowania usługami Windows.  Dopóki ten problem zostanie rozwiązany, zostaną wyświetlone następujące błędy:


- Jeśli spróbujesz uruchomić usługi synchronizacji w Menedżerze kontroli usług Windows, wystąpi błąd "**Windows nie można uruchomić usługi Microsoft Azure AD Sync na komputerze lokalnym**". **Błąd 1069: Usługa nie została uruchomiona z powodu niepowodzenia logowania.** "
- W obszarze podglądu zdarzeń Windows, w dzienniku zdarzeń systemu zawiera błąd **7038 identyfikator zdarzenia** i komunikatem "**usługa ADSync nie może zalogować się podobnie jak w przypadku aktualnie skonfigurowanym hasłem z powodu następującego błędu: Nazwa użytkownika lub hasło jest niepoprawne.** "

Po drugie w określonych warunkach, jeśli hasło zostanie zaktualizowane, usługa synchronizacji można już pobierać klucz szyfrowania za pośrednictwem interfejsu DPAPI. Bez klucza szyfrowania usługi synchronizacji nie może odszyfrować hasła wymagane do synchronizacji z lokalnej usługi AD i Azure AD.
Pojawią się błędy takie jak:

- W obszarze Menedżer sterowania usługami Windows Jeśli użytkownik próbuje uruchomić usługi synchronizacji i nie może pobrać klucza szyfrowania go zakończy się niepowodzeniem z powodu błędu "<strong>Windows Microsoft Azure AD Sync nie można uruchomić na komputerze lokalnym. Aby uzyskać więcej informacji przejrzyj dziennik zdarzeń systemu. Jeśli jest to usługa firmy Microsoft, skontaktuj się z dostawcą usługi i odwołać się do kodu błędu usługi-21451857952</strong>. "
- W obszarze Windows podglądu zdarzeń, w dzienniku zdarzeń aplikacji zawiera błąd przy użyciu **6028 identyfikator zdarzenia** i komunikat o błędzie *"nie można uzyskać dostępu do klucza szyfrowania serwera."*

Aby upewnić się, że nie będą odbierać te błędy, wykonaj procedury opisane w [porzucenie klucz szyfrowania konta usługi ADSync](#abandoning-the-adsync-service-account-encryption-key) podczas zmiany hasła.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Porzucanie klucz szyfrowania konta usługi ADSync
>[!IMPORTANT]
>Poniższe procedury dotyczą tylko usługi Azure AD Connect kompilacji 1.1.443.0 lub starszy.

Użyj poniższych procedur można porzucić klucza szyfrowania.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co zrobić, jeśli chcesz porzucić klucz szyfrowania

Jeśli musisz porzucić klucz szyfrowania, w tym celu należy użyć poniższych procedur.

1. [Zatrzymaj usługę synchronizacji](#stop-the-synchronization-service)

1. [Porzuć istniejący klucz szyfrowania](#abandon-the-existing-encryption-key)

2. [Podaj hasło konta usługi AD DS łącznika](#provide-the-password-of-the-ad-ds-connector-account)

3. [Zainicjuj ponownie hasło do konta usługi ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Uruchom usługę synchronizacji](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zatrzymaj usługę synchronizacji
Najpierw należy zatrzymać usługę w Menedżerze sterowania usługami Windows.  Upewnij się, że usługa nie działa podczas próby zatrzymania go.  Jeśli tak jest, poczekaj, aż zakończy, a następnie Zatrzymaj go.


1. Przejdź do Windows Menedżer sterowania usługami (usługi → START).
2. Wybierz **Microsoft Azure AD Sync** i kliknij przycisk Zatrzymaj.

#### <a name="abandon-the-existing-encryption-key"></a>Porzuć istniejący klucz szyfrowania
Porzuć istniejący klucz szyfrowania, dzięki czemu można utworzyć tego nowego klucza szyfrowania:

1. Zaloguj się do platformy Azure AD Connect serwera jako administrator.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Uruchom polecenie: `./miiskmu.exe /a`

![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Podaj hasło konta usługi AD DS łącznika
Ponieważ nie można odszyfrować istniejących haseł przechowywanych w bazie danych, musisz podać usługi synchronizacji przy użyciu hasła konta usługi AD DS łącznika. Usługa synchronizacji szyfruje hasła przy użyciu nowego klucza szyfrowania:

1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Przejdź do **łączników** kartę.
3. Wybierz **łącznika AD** odnosi się do lokalnej usługi AD. Jeśli masz więcej niż jeden łącznik usługi AD, powtórz czynności dla każdego z nich.
4. W obszarze **akcje**, wybierz opcję **właściwości**.
5. W oknie podręcznym wybierz **nawiązywanie połączenia z lasu usługi Active Directory**:
6. Wprowadź hasło do konta usługi AD DS w **hasło** pola tekstowego. Jeśli nie znasz jego hasło, należy ustawić go do żadnej znanej wartości, przed wykonaniem tego kroku.
7. Kliknij przycisk **OK** Aby zapisać nowe hasło i zamknąć okno podręczne.
![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Zainicjuj ponownie hasło do konta usługi ADSync
Nie można bezpośrednio Podaj hasło konta usługi Azure AD z usługą synchronizacji. Zamiast tego należy użyć polecenia cmdlet **ADSyncAADServiceAccount Dodaj** ponowne zainicjowanie konto usługi Azure AD. Polecenia cmdlet resetuje hasło konta i udostępnia je zadaniom usługi synchronizacji:

1. Rozpocznij nową sesję programu PowerShell na serwerze programu Azure AD Connect.
2. Uruchom polecenie cmdlet `Add-ADSyncAADServiceAccount`.
3. W podręcznym oknie dialogowym podaj poświadczenia administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD.
![Narzędzie klucza szyfrowania synchronizacji programu Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Jeśli to się powiodło, pojawi się wiersz polecenia programu PowerShell.

#### <a name="start-the-synchronization-service"></a>Uruchom usługę synchronizacji
Skoro Usługa synchronizacji ma dostęp do klucza szyfrowania i hasła, które są niezbędne, można ponownym uruchomieniu usługi w Menedżerze sterowania usługami Windows:


1. Przejdź do Windows Menedżer sterowania usługami (usługi → START).
2. Wybierz **Microsoft Azure AD Sync** i kliknij przycisk Uruchom ponownie.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
