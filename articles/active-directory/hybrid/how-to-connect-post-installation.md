---
title: 'Program Azure AD Connect: Kolejne kroki i jak zarządzać program Azure AD Connect | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak rozszerzyć domyślnej konfiguracji i zadania operacyjne programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291b3d506993cfea89be072684835c0d4efe75f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60243057"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Kolejne kroki i jak zarządzać program Azure AD Connect
Użyj procedur operacyjnych w tym artykule dostosować usługi Azure Active Directory (Azure AD) Connect do zaspokojenia potrzeb i wymagań swojej organizacji.  

## <a name="add-additional-sync-admins"></a>Dodaj administratorów dodatkowe synchronizacji
Domyślnie tylko użytkownika, który wykonał instalacji i administratorami lokalnymi mogą zarządzać aparatem synchronizacji zainstalowane. Dodatkowe użytkownicy powinni mieć możliwość dostępu i zarządzania aparatu synchronizacji Znajdź grupę o nazwie ADSyncAdmins na serwerze lokalnym, a następnie dodać je do tej grupy.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Przypisywanie licencji do użytkowników usługi Azure AD Premium i Enterprise Mobility Suite
Skoro użytkowników zostały zsynchronizowane z chmurą, musisz przypisz mu licencję, dzięki czemu można rozpocząć pracę z aplikacji w chmurze takich jak Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Aby przypisać usługi Azure AD Premium lub Enterprise Mobility Suite licencji

1. Zaloguj się do witryny Azure portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na **usługi Active Directory** stronie, kliknij dwukrotnie katalog zawierający użytkowników, o których chcesz skonfigurować.
4. W górnej części strony katalogu wybierz pozycję **Licencje**.
5. Na **licencji** wybierz opcję **Active Directory Premium** lub **pakietu Enterprise Mobility Suite**, a następnie kliknij przycisk **przypisać**.
6. W oknie dialogowym wybierz użytkowników, do których chcesz przypisać licencje, a następnie kliknij ikonę znacznika wyboru, aby zapisać zmiany.

## <a name="verify-the-scheduled-synchronization-task"></a>Sprawdź zadanie zaplanowanej synchronizacji
Aby sprawdzić stan synchronizacji, należy użyć witryny Azure portal.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Można zweryfikować zadania zaplanowanej synchronizacji
1. Zaloguj się do witryny Azure portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na **usługi Active Directory** stronie, kliknij dwukrotnie katalog zawierający użytkowników, o których chcesz skonfigurować.
4. W górnej części strony katalogu wybierz **integracji katalogu**.
5. W obszarze **Integracja z lokalną usługą active directory**, należy pamiętać, czas ostatniej synchronizacji.

<center>

![Czas synchronizacji katalogu](./media/how-to-connect-post-installation/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Uruchom zadanie zaplanowanej synchronizacji
Jeśli potrzebujesz uruchomić zadanie synchronizacji, można to zrobić przez ponowne uruchomienie przy użyciu Kreatora programu Azure AD Connect.  Musisz podać swoje poświadczenia usługi Azure AD.  W kreatorze Wybierz **Dostosowywanie opcji synchronizacji** zadań, a następnie kliknij przycisk **dalej** można przenieść za pomocą kreatora. Na koniec, upewnij się, że **proces synchronizacji rozpocznie się zaraz po zakończeniu początkowej konfiguracji** pole jest zaznaczone.

<center>

![Uruchom synchronizację](./media/how-to-connect-post-installation/startsynch.png)</center>

Aby uzyskać więcej informacji na temat usługi Azure AD Connect sync harmonogramu, zobacz [usługi Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Dodatkowe zadania dostępne w programie Azure AD Connect
Po początkowej instalacji programu Azure AD Connect możesz zawsze kreatora można uruchomić ponownie za pomocą skrótu strony lub pulpitu start program Azure AD Connect.  Zauważysz, ponownie przejść za pomocą Kreatora zapewnia kilka nowych opcji w postaci dodatkowych zadań.  

Poniższa tabela zawiera podsumowanie tych zadań i krótki opis każdego zadania.

![Lista dodatkowych zadań](./media/how-to-connect-post-installation/addtasks.png)

| Dodatkowe zadania | Opis |
| --- | --- |
| **Wyświetl wybrany scenariusz** |Umożliwia wyświetlenie bieżącego rozwiązania Azure AD Connect.  W tym ustawienia ogólne, synchronizację katalogów i ustawień synchronizacji. |
| **Dostosuj opcje synchronizacji** |Zmień bieżącą konfigurację, takie jak dodanie dodatkowych lasów usługi Active Directory z konfiguracją lub włączanie opcji synchronizacji, takie jak użytkownika, grupy, urządzenia lub zapisywania zwrotnego haseł. |
| **Włącz tryb przejściowy** |Informacje z etapów, który nie jest od razu synchronizowane i nie jest eksportowane do usługi Azure AD lub lokalnej usługi Active Directory.  Dzięki tej funkcji można wyświetlać podgląd synchronizacje, przed ich wystąpieniem. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
