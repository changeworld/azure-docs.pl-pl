---
title: Ponowne uruchamianie kreatora instalacji usługi Azure AD Connect | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak działa kreator instalacji przy drugim jego uruchomieniu.
keywords: Kreator instalacji usługi Azure AD Connect umożliwia skonfigurowanie ustawień konserwacji przy drugim uruchomieniu
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261335"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizacja usługi Azure AD Connect: uruchamianie kreatora instalacji po raz drugi
Przy pierwszym uruchomieniu kreatora instalacji usługi Azure AD Connect można przeprowadzić proces konfigurowania instalacji. Jeśli kreator instalacji zostanie ponownie uruchomiony, oferuje on opcje konserwacji.

>[!IMPORTANT]
>Należy pamiętać, że nie można uruchomić kreatora instalacji, gdy synchronizacja jest w toku.  Przed uruchomieniem kreatora sprawdź, czy synchronizacja nie jest uruchomiona.

Kreator instalacji można znaleźć w menu start o nazwie **Azure AD Connect**.

![Menu Start](./media/how-to-connect-installation-wizard/startmenu.png)

Po uruchomieniu kreatora instalacji zostanie wyświetlona strona z tymi opcjami:

![Strona z listą dodatkowych zadań](./media/how-to-connect-installation-wizard/additionaltasks.png)

Jeśli zainstalowano usługę ADFS w usłudze Azure AD Connect, masz jeszcze więcej opcji. Dodatkowe opcje dostępne dla usługi ADFS są udokumentowane w [zarządzaniu usługą ADFS](how-to-connect-fed-management.md#manage-ad-fs).

Wybierz jedno z zadań i kliknij przycisk **Dalej,** aby kontynuować.

> [!IMPORTANT]
> Po otwarciu kreatora instalacji wszystkie operacje w silniku synchronizacji są zawieszone. Upewnij się, że kreator instalacji został zamknięty natychmiast po zakończeniu zmian konfiguracji.
>
>

## <a name="view-current-configuration"></a>Wyświetlanie bieżącej konfiguracji
Ta opcja umożliwia szybki podgląd aktualnie skonfigurowanych opcji.

![Strona z listą wszystkich opcji i ich stanem](./media/how-to-connect-installation-wizard/viewconfig.png)

Kliknij **przycisk Poprzedni,** aby wrócić. W przypadku **wybrania**opcji Zakończ kreator instalacji zostanie zamknięty.

## <a name="customize-synchronization-options"></a>Dostosowywanie opcji synchronizacji
Ta opcja służy do wprowadzania zmian w konfiguracji synchronizacji. Zobaczysz podzbiór opcji ze ścieżki instalacji konfiguracji niestandardowej. Ta opcja jest widoczna nawet wtedy, gdy początkowo była używana instalacja ekspresowa.

* [Dodaj więcej katalogów](how-to-connect-install-custom.md#connect-your-directories). Aby usunąć katalog, zobacz [Usuwanie łącznika](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Zmień filtrowanie domeny i usługi organizacyjnej](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Usuń filtrowanie grupy.
* [Zmień funkcje opcjonalne](how-to-connect-install-custom.md#optional-features).

Innych opcji z instalacji początkowej nie można zmienić i nie są dostępne. Te opcje to:

* Zmień atrybut do użycia dla userPrincipalName i sourceAnchor.
* Zmień metodę łączenia obiektów z innego lasu.
* Włącz filtrowanie oparte na grupach.

## <a name="refresh-directory-schema"></a>Odśwież schemat katalogu
Ta opcja jest używana, jeśli schemat został zmieniony w jednym z lokalnych lasów usług AD DS. Na przykład być może zainstalowano program Exchange lub uaktualniono do schematu systemu Windows Server 2012 z obiektami urządzenia. W takim przypadku należy poinstruować usługę Azure AD Connect, aby ponownie odczytała schemat z usług AD DS i zaktualizowała jego pamięć podręczną. Ta akcja również ponownie generuje reguły synchronizacji. Jeśli dodasz schemat programu Exchange, na przykład reguły synchronizacji dla programu Exchange zostaną dodane do konfiguracji.

Po wybraniu tej opcji zostaną wyświetlone wszystkie katalogi w konfiguracji. Ustawienie domyślne można zachować i odświeżyć wszystkie lasy lub usunąć zaznaczenie niektórych z nich.

![Strona z listą wszystkich katalogów w środowisku](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurowanie trybu przemieszczania
Ta opcja umożliwia włączenie i wyłączenie trybu przejściowego na serwerze. Więcej informacji na temat trybu przemieszczania i sposobu jego użycia można znaleźć w [folderze Operacje](how-to-connect-sync-staging-server.md).

Opcja pokazuje, czy przemieszczania jest aktualnie włączone lub wyłączone:  
![Opcja pokazująca również bieżący stan trybu przemieszczania](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Aby zmienić stan, zaznacz tę opcję i zaznacz lub usuń zaznaczenie pola wyboru.  
![Opcja pokazująca również bieżący stan trybu przemieszczania](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Zmienianie logowania użytkownika
Ta opcja umożliwia zmianę metody logowania użytkownika na i z synchronizacji skrótów haseł, uwierzytelniania przekazowego lub federacji. Nie można zmienić, aby **nie konfigurować**.

Aby uzyskać więcej informacji na temat tej opcji, zobacz [logowanie użytkownika](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o modelu konfiguracji używanym przez synchronizację usługi Azure AD Connect w [opisie aprowizacji deklaratywnej.](concept-azure-ad-connect-sync-declarative-provisioning.md)

**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
