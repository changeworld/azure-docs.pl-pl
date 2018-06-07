---
title: Kreator instalacji ponownie uruchomienie programu Azure AD Connect | Dokumentacja firmy Microsoft
description: Wyjaśniono sposób działania przez Kreator instalacji drugiego czas uruchomienia.
keywords: Kreator instalacji Azure AD Connect umożliwia skonfigurowanie ustawień konserwacji uruchomienia po raz drugi
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a33301904cff9a5a396eb6f641b7e3bc6b3e9801
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592538"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizacja programu Azure AD Connect: uruchomienie Kreatora instalacji po raz drugi
Podczas pierwszego uruchomienia Kreatora instalacji usługi Azure AD Connect przeprowadza użytkownika przez konfigurowanie instalacji. Jeśli ponownie uruchom Kreatora instalacji, oferuje opcje konserwacji.

Kreator instalacji można znaleźć w menu start o nazwie **Azure AD Connect**.

![Start menu](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Podczas uruchamiania Kreatora instalacji zostanie wyświetlona strona z tych opcji:

![Strona z listą dodatkowe zadania](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Po zainstalowaniu usług AD FS z programem Azure AD Connect, masz jeszcze więcej opcji. Dodatkowe opcje masz dla usług AD FS są udokumentowane w artykule [zarządzania usług AD FS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Wybierz jedno z zadań, a następnie kliknij przycisk **dalej** aby kontynuować.

> [!IMPORTANT]
> Podczas otwierania Kreatora instalacji, wszystkie operacje w aparacie synchronizacji są wstrzymane. Upewnij się, że zamkniesz Kreatora instalacji, jak zmiany konfiguracji zostały ukończone.
>
>

## <a name="view-current-configuration"></a>Wyświetlanie bieżącej konfiguracji
Ta opcja umożliwia szybki przegląd aktualnie skonfigurowanych opcji.

![Strona z listą wszystkich opcji i ich stan](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Kliknij przycisk **Wstecz** aby wrócić do poprzedniej strony. W przypadku wybrania **zakończenia**, zamknąć kreatora instalacji.

## <a name="customize-synchronization-options"></a>Dostosuj opcje synchronizacji
Ta opcja służy do wprowadzania zmian w konfiguracji synchronizacji. Zostanie wyświetlony podzbiór opcje ze ścieżki instalacji konfiguracji niestandardowej. Ta opcja jest widoczna, nawet jeśli początkowo użyć instalacji ekspresowej.

* [Dodaj więcej katalogów](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Aby usunięcie katalogu, zobacz [usunąć łącznik](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Zmień domenę i jednostkę Organizacyjną filtrowania](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Usuń grupę filtrowania.
* [Zmień funkcje opcjonalne](active-directory-aadconnect-get-started-custom.md#optional-features).

Inne opcje instalacji początkowej nie można zmienić i nie są dostępne. Dostępne są następujące opcje:

* Zmienianie atrybutu userPrincipalName i sourceAnchor.
* Zmień metodę łącząca dla obiektów z innego lasu.
* Włącz filtrowanie na podstawie grupy.

## <a name="refresh-directory-schema"></a>Odśwież schemat katalogu
Ta opcja jest używana, jeśli schemat zostały zmienione w jednym z lokalnymi lasami usługi AD DS. Na przykład użytkownik może instalację programu Exchange lub uaktualnienie do schematu systemu Windows Server 2012 z obiektami urządzenia. W takim przypadku należy poinstruować Azure AD Connect, aby ponownie odczytać schematu z usług AD DS i zaktualizować pamięci podręcznej. Ta akcja generuje również reguły synchronizacji. Jeśli dodasz schematu programu Exchange, na przykład reguły synchronizacji programu Exchange są dodawane do konfiguracji.

Po wybraniu tej opcji wszystkie katalogi w konfiguracji są wyświetlane. Można pozostawić domyślne ustawienie i Odśwież wszystkich lasach lub usuń zaznaczenie niektórych z nich.

![Strona z listą wszystkich katalogów w środowisku](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurowanie trybu przejściowego
Ta opcja umożliwia włączanie i wyłączanie trybu przejściowego na serwerze. Więcej informacji o tymczasowych trybu i sposobie ich użycia można znaleźć w [operacji](active-directory-aadconnectsync-operations.md#staging-mode).

Opcja pokazuje, czy przemieszczania jest obecnie włączona lub wyłączona:  
![Opcja, która również jest wyświetlany bieżący stan trybu przejściowego](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Aby zmienić stan, wybierz tę opcję i zaznacz lub usuń zaznaczenie pola wyboru.  
![Opcja, która również jest wyświetlany bieżący stan trybu przejściowego](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Zmień dane logowania użytkownika
Ta opcja umożliwia zmianę metody logowania użytkownika do i z synchronizacji skrótów haseł, uwierzytelnianie przekazywane lub federacji. Nie można zmienić **nieskonfigurowane**.

Aby uzyskać więcej informacji na temat tej opcji, zobacz [logowania użytkownika](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o model konfiguracji używane przez synchronizacja programu Azure AD Connect w [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
