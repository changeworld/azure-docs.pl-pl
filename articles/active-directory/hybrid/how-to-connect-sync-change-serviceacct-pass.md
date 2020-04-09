---
title: 'Synchronizacja usługi Azure AD Connect: zmienianie konta usługi ADSync | Dokumenty firmy Microsoft'
description: W tym dokumencie tematu opisano klucz szyfrowania i sposób porzucenia go po zmianie hasła.
services: active-directory
keywords: Konto usługi Azure AD sync, hasło
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
ms.openlocfilehash: 9b2a0d0b77b6db481b13785907a1359d2bbe3e9b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984508"
---
# <a name="changing-the-adsync-service-account-password"></a>Zmienianie hasła do konta usługi ADSync
Jeśli zmienisz hasło konta usługi ADSync, usługa synchronizacji nie będzie mogła poprawnie uruchomić, dopóki klucz szyfrowania nie zostanie porzucony i ponownie zostanie ponownie uruchomiony hasło konta usługi ADSync. 

Usługa Azure AD Connect w ramach usług synchronizacji używa klucza szyfrowania do przechowywania haseł konta łącznika usług AD DS i konta usługi ADSync.  Te konta są szyfrowane, zanim zostaną zapisane w bazie danych. 

Używany klucz szyfrowania jest zabezpieczony za pomocą [systemu Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI chroni klucz szyfrowania przy użyciu **konta usługi ADSync**. 

Jeśli chcesz zmienić hasło konta usługi, możesz użyć procedur w [porzuceniu klucza szyfrowania konta usługi ADSync,](#abandoning-the-adsync-service-account-encryption-key) aby to osiągnąć.  Procedury te powinny być również używane, jeśli trzeba zrezygnować z klucza szyfrowania z jakiegokolwiek powodu.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemy wynikające ze zmiany hasła
Istnieją dwie rzeczy, które należy wykonać po zmianie hasła konta usługi.

Najpierw należy zmienić hasło w Menedżerze sterowania usługami systemu Windows.  Dopóki ten problem nie zostanie rozwiązany, zobaczysz następujące błędy:


- Jeśli spróbujesz uruchomić usługę synchronizacji w Menedżerze sterowania usługami systemu Windows, zostanie wyświetlony komunikat "**System Windows nie może uruchomić usługi Microsoft Azure AD Sync na komputerze lokalnym**". **Błąd 1069: Usługa nie została uruchomię z powodu błędu logowania.**"
- W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń systemowych zawiera błąd o **identyfikatorze zdarzenia 7038** i komunikat **"Usługa ADSync nie może zalogować się tak, jak w przypadku aktualnie skonfigurowanego hasła z powodu następującego błędu: Nazwa użytkownika lub hasło jest niepoprawna.**"

Po drugie, w określonych warunkach, jeśli hasło zostanie zaktualizowane, usługa synchronizacji nie może już pobrać klucza szyfrowania za pośrednictwem dpapi. Bez klucza szyfrowania usługa synchronizacji nie może odszyfrować haseł wymaganych do synchronizacji z lokalnymi usługami AD i usługi Azure AD.
Zostaną wyświetlenie błędów, takich jak:

- W menedżerze sterowania usługami systemu Windows, jeśli spróbujesz uruchomić usługę synchronizacji i nie można pobrać klucza szyfrowania, nie powiedzie się z błędem "<strong>System Windows nie może uruchomić microsoft azure ad sync na komputerze lokalnym. Aby uzyskać więcej informacji, zapoznaj się z dziennikiem zdarzeń systemowych. Jeśli jest to usługa firmy inne niż Microsoft, skontaktuj się z dostawcą usługi i zapoznaj się z kodem błędu specyficznym dla usługi -21451857952</strong>."
- W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń aplikacji zawiera błąd o **identyfikatorze zdarzenia 6028** i komunikat o błędzie *"Nie można uzyskać dostępu do klucza szyfrowania serwera".*

Aby upewnić się, że te błędy nie są odbierane, postępuj zgodnie z procedurami [w porzuceniu klucza szyfrowania konta usługi ADSync](#abandoning-the-adsync-service-account-encryption-key) podczas zmiany hasła.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Porzucenie klucza szyfrowania konta usługi ADSync
>[!IMPORTANT]
>Poniższe procedury dotyczą tylko kompilacji usługi Azure AD Connect 1.1.443.0 lub starszej.

Użyj następujących procedur, aby porzucić klucz szyfrowania.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co zrobić, jeśli trzeba zrezygnować z klucza szyfrowania

Jeśli musisz zrezygnować z klucza szyfrowania, użyj następujących procedur, aby to osiągnąć.

1. [Zatrzymywać usługę synchronizacji](#stop-the-synchronization-service)

1. [Porzucenie istniejącego klucza szyfrowania](#abandon-the-existing-encryption-key)

2. [Podaj hasło konta łącznika usług AD DS](#provide-the-password-of-the-ad-ds-connector-account)

3. [Ponowne zainicjowanie hasła konta usługi ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Uruchamianie usługi synchronizacji](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zatrzymywać usługę synchronizacji
Najpierw można zatrzymać usługę w Menedżerze sterowania usługami systemu Windows.  Upewnij się, że usługa nie jest uruchomiona podczas próby jej zatrzymania.  Jeśli tak, poczekaj, aż się zakończy, a następnie zatrzymaj go.


1. Przejdź do Menedżera sterowania usługami systemu Windows (START → Usługi).
2. Wybierz pozycję **Microsoft Azure AD Sync** i kliknij pozycję Zatrzymaj.

#### <a name="abandon-the-existing-encryption-key"></a>Porzucenie istniejącego klucza szyfrowania
Porzuć istniejący klucz szyfrowania, aby można było utworzyć nowy klucz szyfrowania:

1. Zaloguj się do serwera Azure AD Connect Server jako administrator.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu:`'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Uruchom polecenie:`./miiskmu.exe /a`

![Narzędzie klucza szyfrowania usługi Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Podaj hasło konta łącznika usług AD DS
Ponieważ istniejące hasła przechowywane w bazie danych nie mogą być już odszyfrowywane, należy podać usługę synchronizacji z hasłem konta łącznika usług AD DS. Usługa synchronizacji szyfruje hasła przy użyciu nowego klucza szyfrowania:

1. Uruchom Menedżera usług synchronizacji (START → Usługa synchronizacji).
</br>![Menedżer usługi synchronizacji](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Przejdź do karty **Łączniki.**
3. Wybierz łącznik **usługi AD** odpowiadający lokalnej ucho. Jeśli masz więcej niż jeden łącznik usługi AD, powtórz następujące kroki dla każdego z nich.
4. W obszarze **Akcje**wybierz pozycję **Właściwości**.
5. W wyskakującym oknie dialogowym wybierz pozycję **Połącz z lasem usługi Active Directory:**
6. Wprowadź hasło konta usług AD DS w polach tekstowych **Hasło.** Jeśli nie znasz jego hasła, należy ustawić je na znaną wartość przed wykonaniem tego kroku.
7. Kliknij **przycisk OK,** aby zapisać nowe hasło i zamknąć okno podręczne.
![Narzędzie klucza szyfrowania usługi Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Ponowne zainicjowanie hasła konta usługi ADSync
Nie można bezpośrednio podać hasła konta usługi Azure AD do usługi synchronizacji. Zamiast tego należy użyć polecenia **cmdlet Add-ADSyncAADServiceAccount** do ponownego zainicjowania konta usługi Azure AD. Polecenie cmdlet resetuje hasło konta i udostępnia je usłudze synchronizacji:

1. Uruchom nową sesję programu PowerShell na serwerze usługi Azure AD Connect.
2. Uruchom polecenie `Add-ADSyncAADServiceAccount`cmdlet .
3. W oknie podręcznym podaj poświadczenia administratora usługi Azure AD Global dla dzierżawy usługi Azure AD.
![Narzędzie klucza szyfrowania usługi Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Jeśli to się powiedzie, zostanie wyświetlony wiersz polecenia programu PowerShell.

#### <a name="start-the-synchronization-service"></a>Uruchamianie usługi synchronizacji
Teraz, gdy usługa synchronizacji ma dostęp do klucza szyfrowania i wszystkich haseł, których potrzebuje, możesz ponownie uruchomić usługę w Menedżerze sterowania usługami systemu Windows:


1. Przejdź do Menedżera sterowania usługami systemu Windows (START → Usługi).
2. Wybierz pozycję **Microsoft Azure AD Sync** i kliknij przycisk Uruchom ponownie.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
