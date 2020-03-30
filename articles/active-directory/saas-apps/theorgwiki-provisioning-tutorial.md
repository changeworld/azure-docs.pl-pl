---
title: 'Samouczek: Konfigurowanie TheOrgWiki do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do theOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063151"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie TheOrgWiki do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w TheOrgWiki i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i de-provision użytkowników i/lub grup do TheOrgWiki.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca OrgWiki](https://www.theorgwiki.com/welcome/).
* Konto użytkownika w TheOrgWiki z uprawnieniami administratora.

## <a name="assign-users-to-theorgwiki"></a>Przypisywanie użytkowników do TheOrgWiki

Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do TheOrgWiki. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do TheOrgWiki, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Ważne wskazówki dotyczące przypisywania użytkowników do TheOrgWiki

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do TheOrgWiki do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do TheOrgWiki należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-theorgwiki-for-provisioning"></a>Konfigurowanie TheOrgWiki do inicjowania obsługi administracyjnej

Przed skonfigurowaniem TheOrgWiki do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy włączyć inicjowanie obsługi administracyjnej scim na TheOrgWiki.

1. Zaloguj się do [konsoli administracyjnej TheOrgWiki](https://www.theorgwiki.com/login/). Kliknij **konsolę administracyjną**.

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. W konsoli administracyjnej kliknij **kartę Ustawienia**. 

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Przejdź do **pozycji Konta usług**.

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Kliknij **+Konto usługi**. W obszarze **Typ konta usługi**wybierz pozycję Token **Based**. Kliknij przycisk **Zapisz**.

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Skopiuj **aktywne tokeny**. Ta wartość zostanie wprowadzona w polu Token tajny w karcie Inicjowanie obsługi administracyjnej aplikacji TheOrgWiki w witrynie Azure portal.
     
    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Dodaj TheOrgWiki z galerii

Aby skonfigurować TheOrgWiki do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać TheOrgWiki z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TheOrgWiki**, wybierz **TheOrgWiki** w panelu wyników. 

    ![TheOrgWiki na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Zarejestruj się dla TheOrgWiki,** który przekieruje Cię na stronę logowania TheOrgWiki. 

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  W prawym górnym rogu wybierz pozycję **Zaloguj**.

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Ponieważ TheOrgWiki jest aplikacją OpenIDConnect, wybierz opcję zalogowania się do OrgWiki za pomocą konta służbowego Microsoft.

    ![TheOrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Po pomyślnym uwierzytelnieniu aplikacja zostanie automatycznie dodana do twojej dzierżawy i zostaniesz przekierowany na swoje konto TheOrgWiki.

    ![OrgWiki Dodaj SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej theOrgWiki 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w TheOrgWiki na podstawie przypisania użytkownika i/lub grupy w usłudze Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla TheOrgWiki w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **theOrgWiki**.

    ![Link OrgWiki na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **adresie URL dzierżawy**. 

    Przykład: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Wartość poddomeny** można ustawić tylko podczas początkowego procesu rejestracji dla TheOrgWiki.
 
6. Wprowadź wartość tokenu w polu **Tajny token,** który został pobrany wcześniej z TheOrgWiki. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z TheOrgWiki. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto TheOrgWiki ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z TheOrgWiki**.

    ![Mapowania użytkowników TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do TheOrgWiki w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w TheOrgWiki dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla TheOrgWiki, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić OrgWiki, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

14. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników i/lub grup w celu udostępnienia, zobacz [Jak długo potrwa aprowizję użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć **sekcji Bieżący stan** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w theOrgWiki. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](../app-provisioning/check-status-user-account-provisioning.md)