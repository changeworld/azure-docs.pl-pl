---
title: 'Samouczek: Konfigurowanie usługi Flock do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z aprowizowania kont użytkowników do Flock.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: cd7aae05b064657c7b9072402f4bc4d4d7fef7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057893"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie stada do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w flock i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej użytkowników i/lub grup do Flock.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawca stada](https://flock.com/pricing/)
* Konto użytkownika w Flock z uprawnieniami administratora.

## <a name="assigning-users-to-flock"></a>Przypisywanie użytkowników do stada 

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do flock. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Flock, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Flock 

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Flock do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Flock, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-flock--for-provisioning"></a>Flock instalatora do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Flock do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy włączyć inicjowanie obsługi administracyjnej scim na Flock.

1. Zaloguj się do [Flock](https://web.flock.com/?). Kliknij **pozycję Ikona** > ustawień**Zarządzaj zespołem**.

    ![Flock](media/flock-provisioning-tutorial/icon.png)

2. Wybierz **pozycję Yuth i Inicjowanie obsługi administracyjnej**.

    ![Flock](media/Flock-provisioning-tutorial/auth.png)

3. Skopiuj **token interfejsu API**. Te wartości zostaną wprowadzone w polu **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji Flock w witrynie Azure portal.

    ![Flock](media/Flock-provisioning-tutorial/provisioning.png)


## <a name="add-flock--from-the-gallery"></a>Dodaj Stado z galerii

Aby skonfigurować flock do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Flock z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać flock z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Stado**, wybierz **pozycję Stado** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Stado na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla flock  

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Flock na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne na podstawie SAML dla Flock, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Flock](Flock-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Flock w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Flock**.

    ![Łącze Stado na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora `https://api.flock-staging.com/v2/scim` wprowadź wartości tokenu i **tokenu interfejsu API** pobrane wcześniej odpowiednio w **adresie URL dzierżawy** i **tokenie tajnym.** Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z Flock. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Flock ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do zgromad.**

    ![Mapowania użytkowników stada](media/flock-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do flock w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Flock dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika stada](media/flock-provisioning-tutorial/userattribute.png)

11. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla flock, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Flock, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

14. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników i/lub grup w celu udostępnienia, zobacz [Jak długo potrwa aprowizję użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Za pomocą sekcji **Bieżący stan** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w flock. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)