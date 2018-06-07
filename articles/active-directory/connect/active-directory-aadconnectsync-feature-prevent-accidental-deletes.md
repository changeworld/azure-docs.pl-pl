---
title: 'Synchronizacja programu Azure AD Connect: Zapobieganie przypadkowemu usuwaniu | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję zapobiegania przypadkowemu usuwaniu (zapobieganie przypadkowym usunięciu) w programie Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ecec08dcfa251b94ccfd09e87a8499dc03164ea9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595074"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizacja programu Azure AD Connect: zapobieganie przypadkowemu usuwaniu
W tym temacie opisano funkcję zapobiegania przypadkowemu usuwaniu (zapobieganie przypadkowym usunięciu) w programie Azure AD Connect.

Po zainstalowaniu programu Azure AD Connect, zapobieganie przypadkowemu usuwaniu jest domyślnie włączone i skonfigurowany tak, aby nie zezwalaj na eksport z więcej niż 500 usuwa. Ta funkcja została zaprojektowana w celu zabezpieczenia przed przypadkowym konfiguracji zmiany i do katalogu lokalnego, które będą wpływać na liczbę użytkowników i innych obiektów.

## <a name="what-is-prevent-accidental-deletes"></a>Co to jest Zapobieganie przypadkowemu usuwaniu
Typowe scenariusze Zobacz wiele usuwa obejmują:

* Zmienia się na [filtrowania](active-directory-aadconnectsync-configure-filtering.md) w przypadku, gdy cały [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) lub [domeny](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) nie jest zaznaczona.
* Wszystkie obiekty w jednostce Organizacyjnej zostaną usunięte.
* Jednostka Organizacyjna zostanie zmieniona, więc wszystkie obiekty w nim są traktowane jako poza zakresem synchronizacji.

Wartość domyślna 500 obiektów można zmienić przy użyciu programu PowerShell przy użyciu `Enable-ADSyncExportDeletionThreshold`, która jest częścią tego modułu AD Sync zainstalowane za pomocą usługi Azure Active Directory Connect. Należy skonfigurować tę wartość do rozmiaru Twojej organizacji. Ponieważ harmonogramu synchronizacji jest uruchamiane co 30 minut, wartość jest liczba usuwa występuje w ciągu 30 minut.

W przypadku zbyt wielu usunięcia umieszczone mają być eksportowane do usługi Azure AD zatrzymywany eksportu i otrzymasz wiadomość e-mail w następujący sposób:

![Zapobieganie przypadkowemu usuwaniu poczty e-mail](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Witaj (technicznych skontaktuj się z). Godzinie usługi synchronizacji tożsamości wykrył, że liczba operacji usunięcia przekracza próg usuwania skonfigurowany (nazwa organizacji). Całkowita liczba (numer) obiektów zostały wysłane do usunięcia w tej synchronizacji tożsamości, uruchom. To osiągnięto lub przekroczono wartość progową skonfigurowanych usuwania obiektów (numer). Musisz podać potwierdzenie czy usunięcia tych powinna zostać przetworzona, aby firma Microsoft będzie kontynuowana. Zobacz zapobiega przypadkowym usunięciu, aby uzyskać więcej informacji o błędzie wymienione w tej wiadomości e-mail.*
>
> 

Można również wyświetlić stan `stopped-deletion-threshold-exceeded` podczas przeglądania **Menedżera usługi synchronizacji** interfejsu użytkownika dla profilu eksportu.
![Zapobieganie przypadkowemu usuwaniu interfejsu użytkownika Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Jeśli to nieoczekiwany, badanie i podjąć działania naprawcze. Aby wyświetlić obiekty, które mają zostać usunięte, wykonaj następujące czynności:

1. Uruchom **usługi synchronizacji** z Start Menu.
2. Przejdź do **łączniki**.
3. Wybierz łącznik z typem **usługi Azure Active Directory**.
4. W obszarze **akcje** po prawej stronie, wybierz **przestrzeni łącznika wyszukiwania**.
5. W oknie podręcznym w obszarze **zakres**, wybierz pozycję **rozłączona, ponieważ** i wybierz czas w przeszłości. Kliknij przycisk **wyszukiwania**. Ta strona zawiera widok wszystkich obiektów, które ma zostać usunięta. Klikając każdego elementu, można uzyskać dodatkowe informacje o obiekcie. Możesz również kliknąć **ustawienie kolumny** można dodawać dodatkowe atrybuty być widocznych w siatce.

![Obszar łączników wyszukiwania](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Jeśli wszystkie usuwa są potrzebne, wykonaj następujące czynności:

1. Aby pobrać bieżący próg usuwania, uruchom polecenie cmdlet programu PowerShell `Get-ADSyncExportDeletionThreshold`. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD. Wartość domyślna to 500.
2. Aby tymczasowo wyłączyć tę ochronę i pozwolić tych usuwa przejść, uruchom polecenie cmdlet programu PowerShell: `Disable-ADSyncExportDeletionThreshold`. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD.
   ![Poświadczenia](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Z Azure łącznika usługi Active Directory nadal wybrana, wybierz akcję **Uruchom** i wybierz **wyeksportować**.
4. Aby ponownie włączyć ochronę, uruchom polecenie cmdlet programu PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Zastąp wartość, którą można zauważyć podczas pobierania bieżący próg usuwania 500. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
