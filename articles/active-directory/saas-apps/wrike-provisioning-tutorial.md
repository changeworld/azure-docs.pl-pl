---
title: 'Samouczek: Konfigurowanie usługi Wrike do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania kont użytkowników usługi Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064195"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Wrike do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które można wykonać w w ramach usługi Wrike i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego inicjowania obsługi administracyjnej i usuwania użytkowników lub grup aprovision do usługi Wrike.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawca Wrike](https://www.wrike.com/price/)
* Konto użytkownika w wrike z uprawnieniami administratora

## <a name="assign-users-to-wrike"></a>Przypisywanie użytkowników do wrike
Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownika, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Wrike. Następnie przypisz tych użytkowników lub grupy do Wrike, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Wrike

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do usługi Wrike w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowych użytkowników lub grup można przypisać później.

* Podczas przypisywania użytkownika do programu Wrike w oknie dialogowym przypisywania należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna). Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-wrike-for-provisioning"></a>Konfigurowanie wrike do inicjowania obsługi administracyjnej

Przed skonfigurowaniem programu Wrike do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej usługi System for Cross-domain Identity Management (SCIM) w programie Wrike.

1. Zaloguj się do [konsoli administracyjnej Wrike](https://www.Wrike.com/login/). Przejdź do identyfikatora dzierżawy. Wybierz **aplikacje & integracje**.

    ![Aplikacje & integracje](media/Wrike-provisioning-tutorial/admin.png)

2.  Przejdź do **usługi Azure AD** i wybierz ją.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Wybierz scim. Skopiuj **podstawowy adres URL**.

    ![Podstawowy adres URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Wybierz **interfejs API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Zostanie otwarte okno podręczne. Wprowadź to samo hasło, które zostało utworzone wcześniej, aby utworzyć konto.

    ![Wrike Utwórz token](media/Wrike-provisioning-tutorial/password.png)

6.  Skopiuj **token tajny**i wklej go w usłudze Azure AD. Wybierz **pozycję Zapisz,** aby zakończyć konfigurację inicjowania obsługi administracyjnej w programie Wrike.

    ![Token stałego dostępu](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Dodaj Wrike z galerii

Przed skonfigurowaniem programu Wrike do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę Wrike z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać Wrike z galerii aplikacji usługi Azure AD, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Wrike**, wybierz pozycję **Wrike** w panelu wyników, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![Wrike na liście wyników](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w udrowieniu Wrike 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie Wrike na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Aby włączyć logowanie jednokrotne oparte na saml dla Wrike, postępuj zgodnie z instrukcjami w [samouczku logowania jednokrotnego Wrike](wrike-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla usługi Wrike w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Wszystkie aplikacje](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Wrike**.

    ![Łącze Wrike na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Tryb inicjowania obsługi administracyjnej ustawiony na Automatyczny](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora wprowadź **podstawowy adres URL** i wartości **tokenu dostępu stałego** pobrane wcześniej odpowiednio w **adresie URL dzierżawy** i **tokenie tajnym.** Wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Wrike. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Wrike ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Wybierz **pozycję Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Wrike**.

    ![Mapowania użytkowników Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Wrike w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Wrike dla operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Wrike, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników lub grupy, które mają być aprowizować dla Wrike, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

14. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników lub grup do aprowizowania, zobacz [Jak długo potrwa aprowizje użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć sekcji **Bieżący stan** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie Wrike. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
