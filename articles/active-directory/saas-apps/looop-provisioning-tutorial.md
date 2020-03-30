---
title: 'Samouczek: Konfigurowanie funkcji Looop do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników w looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057444"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie looop do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze Looop i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do usługi Looop.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Najemca Looop](https://www.looop.co/pricing/)
* Konto użytkownika w Looop z uprawnieniami administratora.

## <a name="assign-users-to-looop"></a>Przypisywanie użytkowników do Looop

Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi Looop. Po podjęciu decyzji, możesz przypisać tych użytkowników i / lub grupy do Looop, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Looop

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do looop do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Looop, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-looop-for-provisioning"></a>Konfigurowanie looop do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Looop do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy pobrać niektóre informacje o inicjowaniu obsługi administracyjnej z Looop.

1. Zaloguj się do [konsoli administracyjnej Looop](https://app.looop.co/#/login) i wybierz **pozycję Konto**. W obszarze **Ustawienia konta** wybierz **pozycję Uwierzytelnianie**.

    ![Looop Dodaj SCIM](media/looop-provisioning-tutorial/admin.png)

2. Wygeneruj nowy token, klikając pozycję **Resetuj token** w obszarze **Integracja SCIM**.

    ![Looop Dodaj SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Skopiuj **punkt końcowy SCIM** i **token**. Te wartości zostaną wprowadzone w adresie **URL dzierżawy** i **tajne token** pola na karcie inicjowania obsługi administracyjnej aplikacji Looop w witrynie Azure portal. 

    ![Looop Utwórz token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Dodaj Looop z galerii

Aby skonfigurować looop do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Looop z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Looop**, wybierz **Looop** w panelu wyników. 

    ![Looop na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Zarejestruj się na Looop,** który przekieruje Cię na stronę logowania Looop. 

    ![Looop OIDC Dodaj](media/looop-provisioning-tutorial/signup.png)

6. Ponieważ Looop jest aplikacją OpenIDConnect, wybierz opcję zalogowania się do Looop za pomocą konta służbowego Microsoft.

    ![Looop OIDC zaloguj się](media/looop-provisioning-tutorial/msftlogin.png)

7. Po pomyślnym uwierzytelnieniu zaakceptuj monit o zgodę na stronę zgody. Aplikacja zostanie automatycznie dodana do twojej dzierżawy i zostaniesz przekierowany na swoje konto Looop.

    ![Looop OIDc Zgoda](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w looop 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w looop na podstawie przypisania użytkownika i/lub grupy w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Looop w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Looop**.

    ![Łącze Looop na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://<organisation_domain>.looop.co/scim/v2` **adresie URL dzierżawy**. Na przykład: `https://demo.looop.co/scim/v2`. Wprowadź wartość pobraną i zapisaną wcześniej z Looop w **tajnym tokenie**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z looop. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Looop ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z looop**.

    ![Mapowania użytkowników Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Looop w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Looop dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika Looop](media/looop-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z łącznikiem sieci meta.**

    ![Mapowanie grupy Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do łącznika sieci meta w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w łączniku sieci meta do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy Looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Looop, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Looop, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć sekcji **Szczegóły synchronizacji** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w looop.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)


