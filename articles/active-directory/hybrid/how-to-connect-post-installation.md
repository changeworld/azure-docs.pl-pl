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
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571314"
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
3. Po lewej stronie, wybierz **program Azure AD Connect**
4. W górnej części strony należy zwrócić uwagę ostatniej synchronizacji.

![Czas synchronizacji katalogu](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Uruchom zadanie zaplanowanej synchronizacji
Jeśli potrzebujesz uruchomić zadanie synchronizacji, można to zrobić:

1. Kliknij dwukrotnie skrót program Azure AD Connect, aby uruchomić kreatora.
2. Kliknij pozycję **Konfiguruj**.
3. Na ekranie zadania wybierz **Dostosowywanie opcji synchronizacji** i kliknij przycisk **dalej**
4. Wprowadzanie poświadczeń usługi Azure AD
5. Kliknij przycisk **Dalej**. Kliknij przycisk **Dalej**.  Kliknij przycisk **Dalej**.
5.  Na **gotowe do konfiguracji** ekranu, upewnij się, że **proces synchronizacji rozpocznie się po ukończeniu konfiguracji** pole jest zaznaczone.
6.  Kliknij pozycję **Konfiguruj**.

Aby uzyskać więcej informacji na temat usługi Azure AD Connect sync harmonogramu, zobacz [usługi Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Dodatkowe zadania dostępne w programie Azure AD Connect
Po początkowej instalacji programu Azure AD Connect możesz zawsze kreatora można uruchomić ponownie za pomocą skrótu strony lub pulpitu start program Azure AD Connect.  Zauważysz, ponownie przejść za pomocą Kreatora zapewnia kilka nowych opcji w postaci dodatkowych zadań.  

Poniższa tabela zawiera podsumowanie tych zadań i krótki opis każdego zadania.

![Lista dodatkowych zadań](./media/how-to-connect-post-installation/addtasks2.png)

| Dodatkowe zadania | Opis |
| --- | --- |
|**Ustawienia prywatności**|Wyświetl dane telemetryczne, które jest udostępniane z firmą Microsoft.|
|**Wyświetlanie bieżącej konfiguracji**|Umożliwia wyświetlenie bieżącego rozwiązania Azure AD Connect.  W tym ustawienia ogólne, synchronizację katalogów i ustawień synchronizacji. |
| **Dostosuj opcje synchronizacji** |Zmień bieżącą konfigurację, takie jak dodanie dodatkowych lasów usługi Active Directory z konfiguracją lub włączanie opcji synchronizacji, takie jak użytkownika, grupy, urządzenia lub zapisywania zwrotnego haseł. |
|**Konfiguruj opcje urządzenia**|Urządzenie dostępnymi na potrzeby synchronizacji|
|**Odśwież schemat katalogu**|Umożliwia dodawanie nowych obiektów katalogów lokalnych do synchronizacji|
|**Konfigurowanie trybu przejściowego** |Informacje z etapów, który nie jest od razu synchronizowane i nie jest eksportowane do usługi Azure AD lub lokalnej usługi Active Directory.  Dzięki tej funkcji można wyświetlać podgląd synchronizacje, przed ich wystąpieniem. |
|**Zmiany logowania użytkownika**|Zmień metodę uwierzytelniania, których użytkownicy korzystają z logowania|
|**Zarządzanie Federacji**|Zarządzanie infrastruktury usług AD FS, odnowienie certyfikatów i dodać serwery usług AD FS|
|**Rozwiązywanie problemów**|Pomoc dotycząca rozwiązywania problemów w usłudze Azure AD Connect|

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
