---
title: Ponowne uruchomienie programu Azure AD Connect Kreator instalacji | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak działa przez Kreatora instalacji drugiego uruchomieniu go.
keywords: Kreatora instalacji usługi Azure AD Connect umożliwia skonfigurowanie ustawień konserwacji zostanie uruchomiony po raz drugi
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
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff2caae7cb387f4f0d88cf059d01ad28861b9ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348454"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronizacja programu Azure AD Connect: Uruchamianie kreatora instalacji po raz drugi
Podczas pierwszego uruchomienia Kreatora instalacji usługi Azure AD Connect, przeprowadzi Cię on sposób skonfigurowania instalacji. Jeśli ponownie uruchom Kreatora instalacji, oferuje opcje w celu przeprowadzenia konserwacji.

Kreator instalacji można znaleźć w menu start o nazwie **program Azure AD Connect**.

![Start menu](./media/how-to-connect-installation-wizard/startmenu.png)

Po uruchomieniu Kreatora instalacji, zostanie wyświetlona strona z tych opcji:

![Strona z listą dodatkowe zadania](./media/how-to-connect-installation-wizard/additionaltasks.png)

Po zainstalowaniu usług AD FS za pomocą usługi Azure AD Connect, masz jeszcze więcej opcji. Dodatkowe opcje, które mają dla usług AD FS są udokumentowane w artykule [zarządzania usług AD FS](how-to-connect-fed-management.md#manage-ad-fs).

Wybierz jedno z zadań, a następnie kliknij przycisk **dalej** aby kontynuować.

> [!IMPORTANT]
> Gdy masz, aby otworzyć Kreatora instalacji, wszystkie operacje w aparacie synchronizacji są wstrzymywane. Upewnij się, że możesz zamknąć kreatora instalacji tak szybko, jak zmiany konfiguracji zostały wykonane.
>
>

## <a name="view-current-configuration"></a>Wyświetlanie bieżącej konfiguracji
Ta opcja zapewnia szybki przegląd aktualnie skonfigurowanych opcji.

![Strona z listą wszystkich opcji i ich stan](./media/how-to-connect-installation-wizard/viewconfig.png)

Kliknij przycisk **Wstecz** wrócić. Jeśli wybierzesz **zakończenia**, zamknąć kreatora instalacji.

## <a name="customize-synchronization-options"></a>Dostosuj opcje synchronizacji
Ta opcja służy do wprowadzania zmian w konfiguracji synchronizacji. Zostanie wyświetlony podzestawu opcji dostępnych w ścieżce instalacji niestandardowej konfiguracji. Ta opcja jest widoczna, nawet jeśli początkowo używane instalacji ekspresowej.

* [Dodaj więcej katalogów](how-to-connect-install-custom.md#connect-your-directories). Aby usunięcie katalogu, zobacz [usunąć łącznik](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Zmień domenę i jednostkę Organizacyjną filtrowanie](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Usuń grupę filtrowania.
* [Zmień funkcje opcjonalne](how-to-connect-install-custom.md#optional-features).

Inne opcje instalacji początkowej nie można zmienić i nie są dostępne. Dostępne są następujące opcje:

* Zmiana atrybutu userPrincipalName i sourceAnchor.
* Zmień metodę przyłączany dla obiektów w innym lesie.
* Włącz filtrowanie na podstawie grupy.

## <a name="refresh-directory-schema"></a>Odśwież schemat katalogu
Ta opcja jest używana, jeśli zmieniono schemat w jednym z lokalnymi lasami usługi AD DS. Na przykład może masz zainstalowany program Exchange lub uaktualniony do schematu systemu Windows Server 2012 z obiektami urządzeń. W takim przypadku należy wydać z usługi Azure AD Connect, aby ponownie odczytać schematu z usług AD DS i aktualizowanie pamięci podręcznej. Ta akcja generuje również reguły synchronizacji. Jeśli dodasz schematu programu Exchange, na przykład reguły synchronizacji dla programu Exchange są dodawane do konfiguracji.

Po wybraniu tej opcji, są wyświetlane wszystkie katalogi w konfiguracji. Możesz pozostawić domyślne ustawienie i odświeżyć wszystkich lasów lub usuń zaznaczenie niektórych z nich.

![Strona z listą wszystkich katalogów w środowisku](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurowanie trybu przejściowego
Ta opcja umożliwia włączanie i wyłączanie trybu przejściowego na serwerze. Więcej informacji o tymczasowych tryb i sposobu ich wykorzystywania znajdują się w [operacji](how-to-connect-sync-staging-server.md).

Opcja umożliwia pokazanie Jeśli przemieszczania jest aktualnie włączone lub wyłączone:  
![Opcja, która również jest wyświetlany bieżący stan trybu przejściowego](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Aby zmienić stan, wybierz tę opcję i zaznacz lub usuń zaznaczenie pola wyboru.  
![Opcja, która również jest wyświetlany bieżący stan trybu przejściowego](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Zmiany logowania użytkownika
Ta opcja umożliwia zmianę metody logowania użytkownika, do i z synchronizacji skrótów haseł i uwierzytelniania przekazywanego i federacji. Nie można zmienić **nie należy konfigurować**.

Aby uzyskać więcej informacji na temat tej opcji, zobacz [logowania użytkownika](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o modelu konfiguracji używane przez program Azure AD Connect sync w [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
