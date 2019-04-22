---
title: 'Samouczek: Konfigurowanie chmury Atlassian dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w chmurze Atlassian.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: f4e76121f7815702270d6601413ff7a4c2c25839
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270306"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie chmury Atlassian dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w chmurze Atlassian i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup w celu Atlassian chmury.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawy w chmurze Atlassian](https://www.atlassian.com/licensing/cloud)
* Konto użytkownika w chmurze Atlassian z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na **Standard SCIM interfejsu API usługi Cloud Atlassian**, który jest dostępny dla chmury Atlassian zespołów.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Dodaj Atlassian chmury z galerii

Przed skonfigurowaniem Atlassian chmury dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Atlassian chmury z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Atlassian chmury z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **chmury Atlassian**, wybierz opcję **chmury Atlassian** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Atlassian Cloud na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Przypisywanie użytkowników do chmury Atlassian

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do chmury Atlassian. Po decyzję, należy przypisać użytkowników i/lub grup do chmury Atlassian, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Ważne wskazówki dotyczące przypisywania użytkowników do chmury Atlassian

* Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisany do chmury Atlassian do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkownika w chmurze Atlassian, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurowanie automatycznej aprowizacji użytkowników w chmurze Atlassian 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w chmurze Atlassian oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowanie jednokrotne dla chmury Atlassian, postępując zgodnie z instrukcjami dostarczone w [chmury Atlassian pojedynczego logowania jednokrotnego samouczek](atlassian-cloud-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Atlassian chmury w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **chmury Atlassian**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **chmury Atlassian**.

    ![Link do usługi Atlassian Cloud na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** chmury Atlassian konta. Przykłady te wartości są:

   * W **adres URL dzierżawy** pole, wprowadź punkt końcowy określonej dzierżawy otrzymasz od Atlassian, zgodnie z opisem w kroku 6. Na przykład: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * W **klucz tajny tokenu** pola, wypełnij token wpisu tajnego, zgodnie z opisem w kroku 6.

6. Przejdź do [Menedżer organizacji Atlassian](https://admin.atlassian.com) **> aprowizacji użytkowników** i kliknij pozycję **Utwórz Token**. Kopiuj **podstawowy adres URL katalogu** i **tokenu elementu nośnego** do **adres URL dzierżawy** i **klucz tajny tokenu** odpowiednio w polach.

    ![Aprowizacji w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![aprowizacji w chmurze Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można Atlassian chmury. Jeśli połączenie nie powiedzie się, upewnij się, że konta w chmurze Atlassian ma uprawnienia administratora i spróbuj ponownie.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory w chmurze Atlassian**.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD w chmurze Atlassian w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania konta użytkowników w chmurze Atlassian na potrzeby operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory w chmurze Atlassian**.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD w chmurze Atlassian w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w chmurze Atlassian dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, usługi w chmurze Atlassian inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na chmurze Atlassian, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Atlassian aprowizacji w chmurze](./media/atlassian-cloud-provisioning-tutorial/save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, usługi w chmurze Atlassian inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Atlassian chmury umożliwia inicjowanie obsługi administracyjnej użytkowników tylko z [zweryfikowanych domen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian chmury nie obsługuje obecnie zmienia nazwę grupy. To oznacza, że wszelkie zmiany w nazwie wyświetlanej grupy w usłudze Azure AD nie będzie można zaktualizować i zostaną uwzględnione w chmurze Atlassian.
* Wartość **poczty** atrybut użytkownika w usłudze Azure AD tylko jest wypełniana, jeśli użytkownik ma skrzynki pocztowej programu Microsoft Exchange. Jeśli użytkownik nie ma jednego, zalecane jest inny atrybut odpowiednią do mapowania **wiadomości e-mail** atrybutu w chmurze Atlassian.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
