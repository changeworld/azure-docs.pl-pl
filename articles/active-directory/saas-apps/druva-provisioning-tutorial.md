---
title: 'Samouczek: Konfigurowanie druvy do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników do druvy.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3d1bb0bcbc0df98d7a884004cf96fe9810589185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058114"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie druvy do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w druva i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego aprowizowania i de-provision użytkowników i/lub grup do Druva.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca Druva](https://www.druva.com/products/pricing-plans/).
* Konto użytkownika w Druva z uprawnieniami administratora.

## <a name="assigning-users-to-druva"></a>Przypisywanie użytkowników do Druwy

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do druvy. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Druva, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Druvy

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do druvy, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Druvy należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-druva-for-provisioning"></a>Konfigurowanie druvy do inicjowania obsługi administracyjnej

Przed skonfigurowaniem druvy do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w druva.

1. Zaloguj się do [konsoli administracyjnej Druva](https://console.druva.com). Przejdź do **druwy > wsynchanie**.

    ![Konsola administracyjna Druva](media/druva-provisioning-tutorial/menubar.png)

2. Przejdź do **pozycji Zarządzaj** > **użytkownikami****wdrożeń** > .

    ![Druva Dodaj SCIM](media/druva-provisioning-tutorial/manage.png)

3.  Przejdź do **ustawień**. Kliknij **pozycję Generuj token**.

    ![Druva Dodaj SCIM](media/druva-provisioning-tutorial/settings.png)

4.  Skopiuj wartość **tokenu Eru.** Ta wartość zostanie wprowadzona w polu **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji Druva w witrynie Azure portal.
    
    ![Druva Dodaj SCIM](media/druva-provisioning-tutorial/auth.png)

## <a name="add-druva-from-the-gallery"></a>Dodaj Druvę z galerii

Aby skonfigurować druwę do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać druwę z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać druwę z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Druva**, wybierz **druvę** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja Druva na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla druvy 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w druvie na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć jednokrotne logowanie oparte na SAML dla Druvy, postępując zgodnie z instrukcjami podanymi w [samouczku druva jednokrotnego logowania.](druva-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla druvy w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Druva**.

    ![Link do aplikacji Druva na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5.  W sekcji Poświadczenia administratora `https://apis.druva.com/insync/scim` wprowadź w **adresie URL dzierżawy**. Wprowadź wartość **tokenu eru** w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z druwą. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Druva ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, a następnie wybierz pozycję **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z druwą**.

    ![Mapowania użytkowników druwy](media/druva-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Druva w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Druva dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika druwy](media/druva-provisioning-tutorial/userattribute.png)


10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla druwy, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić druwie, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

    Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w druvie.

    Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Ograniczenia złącza

* Druva wymaga **wiadomości e-mail** jako atrybutu obowiązkowego. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](../app-provisioning/check-status-user-account-provisioning.md)
