---
title: 'Samouczek: Konfigurowanie usługi Atlassian Cloud do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania obsługi administracyjnej kont użytkowników w usłudze Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059355"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Atlassian Cloud do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze Atlassian Cloud i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do usługi Atlassian Cloud.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa atlassian cloud](https://www.atlassian.com/licensing/cloud)
* Konto użytkownika w atlassian cloud z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD zależy od **interfejsu API SCIM w chmurze atlasian**, który jest dostępny dla zespołów atlassian cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Dodaj Atlassian Cloud z galerii

Przed skonfigurowaniem usługi Atlassian Cloud do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę Atlassian Cloud z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać atlassian cloud z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **atlassian cloud**, wybierz **Atlassian Cloud** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Atlassian Cloud na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Przypisywanie użytkowników do atlassian cloud

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do atlassian cloud. Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do atlassian cloud, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Ważne wskazówki dotyczące przypisywania użytkowników do atlassian cloud

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do usługi Atlassian Cloud, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do atlassian cloud należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w usłudze Atlassian Cloud 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Atlassian Cloud na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla atlasian cloud, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego atlassian cloud](atlassian-cloud-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Atlassian Cloud w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje,** a następnie wybierz **atlassian cloud**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Atlassian Cloud**.

    ![Link do usługi Atlassian Cloud na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Przejdź do [Atlassian Organization Manager](https://admin.atlassian.com) **> wybierz katalog > org**.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Kliknij **pozycję Inicjowanie obsługi administracyjnej użytkownika** i kliknij pozycję **Utwórz katalog**. Skopiuj **adres URL bazy katalogu** i token na **okaziciela** odpowiednio do pól **Adres URL dzierżawy** i **Tajny token.**

    ![Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![inicjowania obsługi](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![administracyjnej Atlassian Cloud Inicjowania obsługi administracyjnej Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** i **tajny token** konta atlassian cloud. Przykładami tych wartości są:

   * W polu **Adres URL dzierżawy** wypełnij określony punkt końcowy dzierżawy otrzymany od atlasu, zgodnie z opisem w kroku 6. Na przykład: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * W polu **Tajny token** wypełnij tajny token zgodnie z opisem w kroku 6.

8. Po zapełnieniu pól wyświetlanych w kroku 7 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Atlassian Cloud. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Atlassian Cloud ma uprawnienia administratora i spróbuj ponownie.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Kliknij przycisk **Zapisz**.

11. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą Atlassian Cloud**.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do atlassian cloud w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w atlassian cloud dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą Atlassian Cloud**.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do atlassian cloud w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w atlassian cloud dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Atlassian Cloud, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić atlassian cloud, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Usługa aprowizacji chmury atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w usłudze Atlassian Cloud.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Atlassian Cloud umożliwia inicjowanie obsługi administracyjnej użytkowników tylko ze [zweryfikowanych domen.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* Atlassian Cloud nie obsługuje nazwy grup dzisiaj. Oznacza to, że wszelkie zmiany w displayName grupy w usłudze Azure AD nie zostaną zaktualizowane i odzwierciedlone w usłudze Atlassian Cloud.
* Wartość atrybutu użytkownika **poczty** w usłudze Azure AD jest wypełniana tylko wtedy, gdy użytkownik ma skrzynkę pocztową programu Microsoft Exchange. Jeśli użytkownik nie ma, zaleca się mapowanie innego żądanego atrybutu do atrybutu **wiadomości e-mail** w atlassian cloud.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png