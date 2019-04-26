---
title: 'Synchronizacja programu Azure AD Connect: Zapobieganie przypadkowemu usuwaniu | Dokumentacja firmy Microsoft'
description: W tym temacie opisano funkcję zapobiegania przypadkowemu usuwaniu (zapobieganie przypadkowym) w programie Azure AD Connect.
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
origin.date: 07/12/2017
ms.date: 11/09/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: b1244dd460196e5882caab0d4b526850da48d084
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383393"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizacja programu Azure AD Connect: Zapobieganie przypadkowemu usuwaniu
W tym temacie opisano funkcję zapobiegania przypadkowemu usuwaniu (zapobieganie przypadkowym) w programie Azure AD Connect.

Podczas instalowania usługi Azure AD Connect, zapobieganie przypadkowemu usuwaniu domyślnie włączony i skonfigurowany do nie zezwalaj na eksport o usuwaniu więcej niż 500. Ta funkcja została zaprojektowana po to, aby zapewnić ochronę przed przypadkowymi zmianami konfiguracji, a także zmianami w katalogu lokalnym, które miałyby wpływ na wielu użytkowników i inne obiekty.

## <a name="what-is-prevent-accidental-deletes"></a>Co to jest Zapobieganie przypadkowemu usuwaniu
Typowe scenariusze, gdy pojawi się wiele usuwa obejmują:

- Zmienia się na [filtrowanie](how-to-connect-sync-configure-filtering.md) w przypadku, gdy cały [jednostki Organizacyjnej](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) lub [domeny](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nie jest zaznaczona.
- Wszystkie obiekty w jednostce organizacyjnej zostały usunięte.
- Nazwa jednostki organizacyjnej została zmieniona, więc wszystkie obiekty w tej jednostce są uznawane za znajdujące się poza zakresem synchronizacji.

Domyślna wartość 500 obiektów można zmienić za pomocą programu PowerShell przy użyciu `Enable-ADSyncExportDeletionThreshold`, który jest częścią modułu AD Sync, zainstalowane za pomocą usługi Azure Active Directory Connect. Należy skonfigurować tę wartość, aby dopasować rozmiar Twojej organizacji. Ponieważ programu sync scheduler jest uruchamiane co 30 minut, wartość jest liczbą usuwa występuje w ciągu 30 minut.

W przypadku zbyt wielu usuwa przygotowane mają zostać wyeksportowane do usługi Azure AD zatrzymywany eksportu, a otrzymasz wiadomość e-mail w następujący sposób:

![Zapobieganie przypadkowemu usuwaniu wiadomości e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Pozdrowienia (Technical Preview kontaktu). Godzinie usługi synchronizacji tożsamości wykrył, że liczba usuniętych przekroczył próg usuwania skonfigurowany (nazwa organizacji). Łączna liczba (numer) obiekty zostały wysłane do usunięcia na tego przebiegu synchronizacji tożsamości. Spełnione, lub przekroczenie wartości progowej skonfigurowanego usuwania obiektów (liczba). Musisz podać potwierdzenie, usunięcia te powinny zostać przetworzone, firma Microsoft będzie kontynuować działanie. Zobacz zapobieganie przypadkowymi usunięciami, aby uzyskać więcej informacji na temat błędów wymienione w tej wiadomości e-mail.*
>
> 

Można również wyświetlić stan `stopped-deletion-threshold-exceeded` podczas przeglądania **Menedżera usługi synchronizacji** interfejsu użytkownika dla eksportowanego profilu.
![Zapobieganie przypadkowemu usuwaniu interfejsu użytkownika Menedżera usługi synchronizacji](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Jeśli to nieoczekiwane, zbadaj i podejmuj akcje naprawcze. Aby wyświetlić obiekty, które mają zostać usunięte, należy wykonać następujące czynności:

1. Rozpocznij **usługi synchronizacji** z Start Menu.
2. Przejdź do **łączników**.
3. Wybierz łącznik usługi z typem **usługi Azure Active Directory**.
4. W obszarze **akcje** po prawej stronie, wybierz **wyszukiwania obszaru łącznika**.
5. W okienku wyskakującym w obszarze **zakres**, wybierz opcję **rozłączone, ponieważ** i wybierz godzinę w przeszłości. Kliknij polecenie **Wyszukaj**. Ta strona udostępnia widok wszystkich obiektów, które ma zostać usunięta. Przez kliknięcie każdego elementu, można uzyskać dodatkowe informacje o obiekcie. Możesz również kliknąć **ustawienie kolumny** można dodać dodatkowe atrybuty, które mają być wyświetlane w siatce.

![Wyszukiwania obszaru łącznika](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Usuwa wszystkie są potrzebne, następnie należy wykonać następujące czynności:

1. Uruchom polecenie cmdlet programu PowerShell, aby pobrać bieżący próg usuwania `Get-ADSyncExportDeletionThreshold`. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD. Wartość domyślna to 500.
2. Aby tymczasowo wyłączyć tę ochronę i umożliwić tych usuwa przejść, uruchom polecenie cmdlet programu PowerShell: `Disable-ADSyncExportDeletionThreshold`. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD.
   ![Poświadczenia](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Za pomocą usługi Azure łącznika usługi Active Directory nadal zaznaczone, wybierz akcję **Uruchom** i wybierz **wyeksportować**.
4. Aby ponownie włączyć ochronę, uruchom polecenie cmdlet programu PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Zastąp wartość, którą niezauważone podczas pobierania bieżący próg usuwania 500. Podaj nazwę i hasło konta administratora globalnego usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

- [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

