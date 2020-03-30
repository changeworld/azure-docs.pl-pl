---
title: 'Samouczek: Konfigurowanie usługi Visitly do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego udostępniania i usuwania kont użytkowników usługi Visitly.
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
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063171"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie funkcji Visitly do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które można wykonać w usłudze Visitly i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników lub grup do usługi Visitly.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Najemca wizytowy](https://www.visitly.io/pricing/)
* Konto użytkownika w funkcji Visitly z uprawnieniami administratora

## <a name="assign-users-to-visitly"></a>Przypisywanie użytkowników do visitly 

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownika, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Visitly. Następnie przypisz tych użytkowników lub grupy do Visitly, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Visitly 

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do usługi Visitly, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowych użytkowników lub grup można przypisać później.

* Po przypisaniu użytkownika do funkcji Visitly należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisywania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-visitly-for-provisioning"></a>Konfigurowanie visitly do inicjowania obsługi administracyjnej

Przed skonfigurowaniem visitly do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy włączyć system dla zarządzania tożsamościami między domenami (SCIM) in Visitly.

1. Zaloguj się do [Visitly](https://app.visitly.io/login). Wybierz **opcję Integracja** > **synchronizacji hostów**.

    ![Synchronizacja hostów](media/Visitly-provisioning-tutorial/login.png)

2. Wybierz sekcję **usługi Azure AD.**

    ![Sekcja usługi Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Skopiuj **klucz interfejsu API**. Te wartości są wprowadzane w polu **Token tajny** na karcie **inicjowanie obsługi administracyjnej** aplikacji Visitly w witrynie Azure portal.

    ![Klucz interfejsu API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Dodaj Visitly z galerii

Aby skonfigurować usługę Visitly do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, dodaj visitly z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać Visitly z galerii aplikacji usługi Azure AD, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **pozycję Visitly**, wybierz **pozycję Odwiedź** w panelu wyników, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![Aplikacja Visitly na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla funkcji Visitly 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników lub grup w funkcji Visitly na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Aby włączyć logowanie jednokrotne oparte na SAML dla Visitly, postępuj zgodnie z instrukcjami w [samouczku logowania jednokrotnego Visitly](Visitly-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla usługi Visitly w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Wszystkie aplikacje](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Visitly**.

    ![Link do aplikacji Visitly na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Tryb inicjowania obsługi administracyjnej ustawiony na Automatyczny](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora `https://api.visitly.io/v1/usersync/SCIM` wprowadź wartości **klucza** i interfejsu API pobrane wcześniej odpowiednio w **adresie URL dzierżawy** i **tokenie tajnym.** Wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Visitly. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Visitly ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz **pozycję Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do odwiedzenia**.

    ![Mapowania użytkowników odwiedzających](media/visitly-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Visitly w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Visitly dla operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika wizyty](media/visitly-provisioning-tutorial/userattribute.png)

10. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Visitly, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Visitly, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników lub grup do aprowizowania, zobacz [Jak długo potrwa aprowizje użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć sekcji **Bieżący stan** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w usłudze Visitly. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia złącza

Visitly nie obsługuje twardych usuwania. Wszystko jest tylko nietrwałe.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
