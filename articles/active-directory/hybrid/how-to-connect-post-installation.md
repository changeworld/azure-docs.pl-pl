---
title: 'Usługa Azure AD Connect: Kolejne kroki i sposób zarządzania usługą Azure AD Connect | Dokumenty firmy Microsoft'
description: Dowiedz się, jak rozszerzyć domyślną konfigurację i zadania operacyjne dla usługi Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261296"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Kolejne kroki i jak zarządzać usługą Azure AD Connect
Procedury operacyjne opisane w tym artykule można dostosować usługę Azure Active Directory (Azure AD) Connect do potrzeb i wymagań organizacji.  

## <a name="add-additional-sync-admins"></a>Dodawanie dodatkowych administratorów synchronizacji
Domyślnie tylko użytkownik, który wykonał instalację i administratorzy lokalni, mogą zarządzać zainstalowanym aparatem synchronizacji. Aby dodatkowe osoby mogły uzyskać dostęp do aparatu synchronizacji i zarządzać nim, znajdź grupę o nazwie ADSyncAdmins na serwerze lokalnym i dodaj je do tej grupy.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Przypisywanie licencji użytkownikom usługi Azure AD Premium i Enterprise Mobility Suite
Teraz, gdy użytkownicy zostali zsynchronizowani z chmurą, musisz przypisać im licencję, aby mogli rozpocząć prace nad aplikacjami w chmurze, takimi jak Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Aby przypisać licencję pakietu Azure AD Premium lub Enterprise Mobility Suite

1. Zaloguj się do witryny Azure portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na stronie **Usługi Active Directory** kliknij dwukrotnie katalog zawierający użytkowników, których chcesz skonfigurować.
4. W górnej części strony katalogu wybierz pozycję **Licencje**.
5. Na stronie **Licencje** wybierz pozycję **Active Directory Premium** lub **Enterprise Mobility Suite**, a następnie kliknij pozycję **Przypisz**.
6. W oknie dialogowym wybierz użytkowników, do których chcesz przypisać licencje, a następnie kliknij ikonę znacznika wyboru, aby zapisać zmiany.

## <a name="verify-the-scheduled-synchronization-task"></a>Weryfikowanie zaplanowanego zadania synchronizacji
Użyj witryny Azure portal, aby sprawdzić stan synchronizacji.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Aby zweryfikować zaplanowane zadanie synchronizacji
1. Zaloguj się do witryny Azure portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Po lewej stronie wybierz pozycję **Azure AD Connect**
4. U góry strony zanotuj ostatnią synchronizację.

![Czas synchronizacji katalogu](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Uruchamianie zaplanowanego zadania synchronizacji
Jeśli chcesz uruchomić zadanie synchronizacji, możesz to zrobić, wykonaj:

1. Kliknij dwukrotnie skrót pulpitu usługi Azure AD Connect, aby uruchomić kreatora.
2. Kliknij **pozycję Konfiguruj**.
3. Na ekranie zadania wybierz **opcje Dostosowywania synchronizacji** i kliknij przycisk **Dalej**
4. Wprowadzanie poświadczeń usługi Azure AD
5. Kliknij przycisk **alej**. Kliknij przycisk **alej**.  Kliknij przycisk **alej**.
5.  Na ekranie **Gotowe do skonfigurowania** upewnij się, że pole **Rozpocznij proces synchronizacji po zakończeniu konfiguracji jest zaznaczone.**
6.  Kliknij **pozycję Konfiguruj**.

Aby uzyskać więcej informacji na temat harmonogramu synchronizacji usługi Azure AD Connect, zobacz [Harmonogram łączy usługi Azure AD](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Dodatkowe zadania dostępne w usłudze Azure AD Connect
Po początkowej instalacji usługi Azure AD Connect zawsze można ponownie uruchomić kreatora ze strony początkowej usługi Azure AD Connect lub skrótu pulpitu.  Można zauważyć, że przechodząc przez kreatora ponownie zapewnia kilka nowych opcji w postaci dodatkowych zadań.  

Poniższa tabela zawiera podsumowanie tych zadań i krótki opis każdego zadania.

![Lista dodatkowych zadań](./media/how-to-connect-post-installation/addtasks2.png)

| Dodatkowe zadanie | Opis |
| --- | --- |
|**Ustawienia prywatności**|Zobacz, jakie dane telemetryczne są udostępniane firmie Microsoft.|
|**Wyświetlanie bieżącej konfiguracji**|Wyświetl bieżące rozwiązanie usługi Azure AD Connect.  Obejmuje to ustawienia ogólne, zsynchronizowane katalogi i ustawienia synchronizacji. |
| **Dostosowywanie opcji synchronizacji** |Zmień bieżącą konfigurację, na przykład dodając dodatkowe lasy usługi Active Directory do konfiguracji lub włączając opcje synchronizacji, takie jak odtworzenia danych użytkownika, grupy, urządzenia lub hasła. |
|**Konfigurowanie opcji urządzenia**|Dostępne opcje urządzenia do synchronizacji|
|**Odśwież schemat katalogu**|Umożliwia dodawanie nowych lokalnych obiektów katalogu do synchronizacji|
|**Konfigurowanie trybu przemieszczania** |Informacje o stole montażowym, które nie są natychmiast synchronizowane i nie są eksportowane do usługi Azure AD lub lokalnej usługi Active Directory.  Za pomocą tej funkcji można wyświetlić podgląd synchronizacji przed ich wystąpieniem. |
|**Zmienianie logowania użytkownika**|Zmienianie metody uwierzytelniania używanej przez użytkowników do logowania|
|**Zarządzanie federacją**|Zarządzanie infrastrukturą usług AD FS, odnawianie certyfikatów i dodawanie serwerów usług AD FS|
|**Rozwiązywanie problemów**|Pomoc dotycząca rozwiązywania problemów z usługą Azure AD Connect|

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [integrując tożsamości lokalne z usługą Azure Active Directory.](whatis-hybrid-identity.md)
