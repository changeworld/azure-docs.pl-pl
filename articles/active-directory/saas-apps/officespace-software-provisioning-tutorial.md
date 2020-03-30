---
title: 'Samouczek: Konfigurowanie oprogramowania OfficeSpace do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i wyrównywalizacji kont użytkowników do oprogramowania OfficeSpace.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f832a0a6-ad0a-453f-a747-9cd717e11181
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: 3d472b300400cf230773ba01f3f4362988c34e81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063428"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie oprogramowania OfficeSpace do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w oprogramowaniu OfficeSpace i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do oprogramowania OfficeSpace.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Dzierżawa [oprogramowania OfficeSpace](https://www.officespacesoftware.com/)
* Konto użytkownika w oprogramowaniu OfficeSpace z uprawnieniami administratora.

## <a name="assigning-users-to-officespace-software"></a>Przypisywanie użytkowników do oprogramowania OfficeSpace

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do oprogramowania OfficeSpace. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Oprogramowania OfficeSpace, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Ważne wskazówki dotyczące przypisywania użytkowników do oprogramowania OfficeSpace

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do oprogramowania OfficeSpace w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do oprogramowania OfficeSpace należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-officespace-software-for-provisioning"></a>Konfigurowanie oprogramowania OfficeSpace do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej oprogramowania officespace](https://support.officespacesoftware.com/hc). Przejdź do **ustawień > łączników**.

    ![Konsola administracyjna oprogramowania OfficeSpace](media/officespace-software-provisioning-tutorial/settings.png)

2.  Przejdź do funkcji **Synchronizacja katalogów > scim**.

    ![Oprogramowanie OfficeSpace Dodaj scim](media/officespace-software-provisioning-tutorial/scim.png)

3.  Skopiuj **token uwierzytelniania SCIM**. Ta wartość zostanie wprowadzona w polu Token tajny na karcie Inicjowanie obsługi administracyjnej aplikacji oprogramowania OfficeSpace w portalu Azure.

    ![Token tworzenia oprogramowania officespace](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Dodawanie oprogramowania OfficeSpace z galerii

Przed skonfigurowaniem oprogramowania OfficeSpace do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać oprogramowanie OfficeSpace z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie OfficeSpace z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź oprogramowanie **OfficeSpace**, wybierz pozycję **OfficeSpace Software** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Oprogramowanie OfficeSpace na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w oprogramowaniu OfficeSpace 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w oprogramowaniu OfficeSpace na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na saml dla oprogramowania OfficeSpace, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego oprogramowania OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla oprogramowania OfficeSpace w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Oprogramowanie OfficeSpace**.

    ![Łącze Oprogramowanie OfficeSpace na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wejściowy `https://<subdomain>.officespacesoftware.com/api/scim/v2/` format adresu URL w **adresie URL dzierżawy**. Na przykład: `https://contoso.officespacesoftware.com/api/scim/v2/`. Wprowadź wartość **tokenu uwierzytelniania SCIM** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z oprogramowaniem OfficeSpace. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Oprogramowania OfficeSpace ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z oprogramowaniem OfficeSpace**.

    ![Mapowania użytkowników oprogramowania OfficeSpace](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do oprogramowania OfficeSpace w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w programie OfficeSpace dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika oprogramowania officespace](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla oprogramowania OfficeSpace, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić oprogramowaniu OfficeSpace, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

14. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w oprogramowaniu OfficeSpace.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

