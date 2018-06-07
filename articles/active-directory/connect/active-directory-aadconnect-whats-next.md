---
title: 'Azure AD Connect: Kolejne kroki i sposobu zarządzania Azure AD Connect | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak rozszerzyć domyślnej konfiguracji i zadania operacyjne programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f8b73e70606adc2b1fa593745b3ac426c679f417
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592089"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Kolejne kroki i sposobu zarządzania Azure AD Connect
Należy użyć procedury działania w tym artykule, aby dostosować Połącz usługi Azure Active Directory (Azure AD) do zaspokojenia potrzeb i wymagań Twojej organizacji.  

## <a name="add-additional-sync-admins"></a>Dodaj administratorów dodatkowe synchronizacji
Domyślnie tylko użytkownik, który został instalacji i Administratorzy lokalni mogą zarządzać aparatem synchronizacji zainstalowane. Dodatkowe użytkownicy powinni mieć możliwość dostępu i zarządzanie aparatu synchronizacji zlokalizuj grupę o nazwie ADSyncAdmins na serwerze lokalnym i dodaj je do tej grupy.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Przypisywanie licencji do użytkowników usługi Azure AD Premium i Enterprise Mobility Suite
Teraz, gdy użytkownicy zostały zsynchronizowane w chmurze, należy przypisać im licencję, można rozpocząć korzystanie z aplikacji w chmurze takich jak Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Aby przypisać Azure AD Premium lub Enterprise Mobility Suite licencji

1. Zaloguj się do portalu Azure jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na **usługi Active Directory** kliknij dwukrotnie katalog, który ma użytkowników chcesz skonfigurować.
4. W górnej części strony katalogu wybierz pozycję **Licencje**.
5. Na **licencji** wybierz pozycję **Active Directory Premium** lub **Enterprise Mobility Suite**, a następnie kliknij przycisk **przypisać**.
6. W oknie dialogowym wybierz użytkowników, do których chcesz przypisać licencje, a następnie kliknij ikonę znacznika wyboru, aby zapisać zmiany.

## <a name="verify-the-scheduled-synchronization-task"></a>Sprawdź zadanie zaplanowanej synchronizacji
Aby sprawdzić stan synchronizacji, użyj portalu Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Można zweryfikować zadania zaplanowanej synchronizacji
1. Zaloguj się do portalu Azure jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na **usługi Active Directory** kliknij dwukrotnie katalog, który ma użytkowników chcesz skonfigurować.
4. W górnej części strony katalogu, wybierz **integracji katalogów**.
5. W obszarze **Integracja z lokalną usługą active directory**, należy pamiętać, czas ostatniej synchronizacji.

<center>![Czas synchronizacji katalogu](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Uruchom zadanie synchronizacji według harmonogramu
Jeśli chcesz uruchomić zadanie synchronizacji, można to zrobić, uruchamiając ponownie za pomocą Kreatora programu Azure AD Connect.  Musisz podać swoje poświadczenia usługi Azure AD.  W kreatorze Wybierz **Dostosuj opcje synchronizacji** zadań, a następnie kliknij przycisk **dalej** można przenieść za pomocą kreatora. Na koniec, upewnij się, że **Uruchom proces synchronizacji, gdy tylko Konfiguracja początkowa zostanie ukończona** zaznaczone pole.

<center>![Uruchom synchronizację](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Aby uzyskać więcej informacji o synchronizacji Azure AD Connect harmonogramu, zobacz [Azure AD Connect harmonogramu](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Dodatkowe zadania dostępne w programie Azure AD Connect
Po początkowej instalacji programu Azure AD Connect można zawsze uruchomić Kreator ponownie z start Azure AD Connect skrótów strony lub pulpitu.  Można zauważyć, że ponownie przejść przez kreatora zawiera niektóre nowe opcje w formie dodatkowe zadania.  

Poniższa tabela zawiera podsumowanie tych zadań i krótki opis każdego zadania.

![Lista dodatkowych zadań](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Dodatkowe zadania | Opis |
| --- | --- |
| **Wyświetl wybrany scenariusz** |Umożliwia wyświetlenie bieżącego rozwiązania Azure AD Connect.  Obejmuje to ustawienia ogólne synchronizacji katalogów i ustawień synchronizacji. |
| **Dostosuj opcje synchronizacji** |Zmiany bieżącej konfiguracji, takie jak dodanie dodatkowych lasów usługi Active Directory do konfiguracji lub włączenie opcji synchronizacji, takich jak użytkownika, grupy, urządzenia lub zapisywania zwrotnego haseł. |
| **Włącz tryb przejściowy** |Etap informacje, które nie są natychmiast zsynchronizowane i nie są eksportowane do usługi Azure AD lub lokalnej usługi Active Directory.  Ta funkcja umożliwia podgląd synchronizacje przed ich wystąpieniem. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
