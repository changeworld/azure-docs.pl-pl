---
title: 'Samouczek: Konfigurowanie dialpada do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekprymprowizowania kont użytkowników na platformie Dialpad.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058370"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie dialpada do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w dialpad i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego aprowizowania i de-provision użytkowników i/lub grup do dialpad.

> [!NOTE]
>  W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawca dialpada](https://www.dialpad.com/pricing/).
* Konto użytkownika w konsoli Dialpad z uprawnieniami administratora.

## <a name="assign-users-to-dialpad"></a>Przypisywanie użytkowników do klawiatury dialejcznej
Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do tabletu Dialpad. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Dialpad, postępując zgodnie z instrukcjami tutaj:
 
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Ważne wskazówki dotyczące przypisywania użytkowników do dialpada

 * Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do dialpad do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do klawiatury dialpad należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-dialpad-for-provisioning"></a>Konfigurowanie klawiatury dialisty do inicjowania obsługi administracyjnej

Przed skonfigurowaniem dialpad do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy pobrać niektóre informacje o inicjowaniu obsługi administracyjnej z dialpad.

1. Sign in to your [Dialpad Admin Console](https://dialpadbeta.com/login) and select **Admin settings**. Upewnij się, że **moja firma** jest wybrana z listy rozwijanej. Przejdź do **pozycji Uwierzytelnianie > klucze interfejsu API**.

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Wygeneruj nowy klucz, klikając **pozycję Dodaj klucz** i konfigurując właściwości tajnego tokenu.

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kliknij przycisk **Kliknij, aby wyświetlić wartość** dla ostatnio utworzonego klucza interfejsu API i skopiuj wyświetlaną wartość. Ta wartość zostanie wprowadzona w polu **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji dialpad w witrynie Azure portal. 

    ![Tworzenie tokenu na klawiaturze wybierania numerów](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dodawanie klawiatury dialisty z galerii

Aby skonfigurować program Dialpad do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać klawiaturę wybierania numerów z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać klawiaturę dializą z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **pozycję Dialpad**, wybierz **pozycję Dialpad** w panelu wyników.
    ![Klawiatura dialistyczny na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżnionego poniżej w osobnej przeglądarce. 

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. W prawym górnym rogu wybierz pozycję **Zaloguj się > Użyj dialpada w trybie online**.

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Ponieważ Dialpad jest aplikacją OpenIDConnect, wybierz opcję logowania do dialpada przy użyciu konta służbowego Microsoft.

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po pomyślnym uwierzytelnieniu zaakceptuj monit o zgodę na stronę zgody. Aplikacja zostanie automatycznie dodana do twojej dzierżawy i zostaniesz przekierowany na swoje konto Dialpad.

    ![Dialpad Dodaj SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej na klawiaturze Dialpad

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w dialpadie na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla tabletu Dialpad w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Dialpad**.

    ![Łącze Dialpad na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://dialpad.com/scim` **adresie URL dzierżawy**. Wprowadź wartość pobraną i zapisaną wcześniej z dialpada w **tajnym tokenie**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z tabletem Dialpad. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto dialpad ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z dialpadem**.

    ![Mapowania użytkowników klawiatury dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do dialpad w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w dialpad do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika klawiatury dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla tabletu Dialpad, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić programowi Dialpad, wybierając żądane wartości w **obszarze** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD na klawiaturze dialpad.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Ograniczenia złącza
* Dialpad nie obsługuje zmiany nazw grup dzisiaj. Oznacza to, że wszelkie zmiany **w displayName** grupy w usłudze Azure AD nie zostaną zaktualizowane i odzwierciedlone w dialpad.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
