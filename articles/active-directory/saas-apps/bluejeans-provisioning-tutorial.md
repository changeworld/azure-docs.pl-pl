---
title: 'Samouczek: Konfigurowanie bluejeans do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i wyrównywalego udostępniania kont użytkowników do bluejeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059035"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BlueJeans do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w bluejeans i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i de-provision użytkowników i/lub grup do BlueJeans.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Najemca BlueJeans z planem [Mojej Firmy](https://www.BlueJeans.com/pricing) lub lepiej włączony
* Konto użytkownika w BlueJeans z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API BlueJeans](https://BlueJeans.github.io/developer), który jest dostępny dla zespołów BlueJeans w planie standardowym lub lepszym.

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie rozwiązania BlueJeans z galerii

Przed skonfigurowaniem BlueJeans do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać BlueJeans z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **bluejeans**, wybierz **BlueJeans** w panelu wyników, a następnie wybierz przycisk **Dodaj,** aby dodać aplikację.

    ![Rozwiązanie BlueJeans na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Przypisywanie użytkowników do BlueJeans

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do BlueJeans. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do BlueJeans, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Ważne wskazówki dotyczące przypisywania użytkowników do BlueJeans

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do BlueJeans do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do BlueJeans należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla bluejeans

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w BlueJeans na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne oparte na SAML dla BlueJeans, postępując zgodnie z instrukcjami podanymi w [samouczek logowania jednokrotnego BlueJeans](bluejeans-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla bluejeans w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz pozycję **BlueJeans**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BlueJeans**.

    ![Link rozwiązania BlueJeans na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. W sekcji **Poświadczenia administratora** wprowadź **nazwę użytkownika administratora**i **hasło administratora** konta BlueJeans. Przykładami tych wartości są:

   * W polu **Nazwa użytkownika administratora** wypełnij nazwę użytkownika konta administratora w dzierżawie BlueJeans. Przykład: admin@contoso.com.

   * W polu **Hasło administratora** wypełnij hasło odpowiadające nazwie użytkownika administratora.

6. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z BlueJeans. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto BlueJeans ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z bluejeans**.

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do BlueJeans w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w BlueJeans dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla BlueJeans, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić BlueJeans, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej BlueJeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć sekcji **Szczegóły synchronizacji** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w bluejeans.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Bluejeans nie zezwala na nazwy użytkowników przekraczające 30 znaków.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
