---
title: 'Samouczek: Konfigurowanie usługi Dropbox dla firm dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do usługi Dropbox dla firm.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672907"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Dropbox dla firm dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w usłudze Dropbox dla firm i Azure Active Directory (Azure AD) skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup do usługi Dropbox dla firm.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dropbox dla firm dzierżawy](https://www.dropbox.com/business/pricing)
* Konto użytkownika w usłudze Dropbox dla firm z uprawnieniami administratora.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dodaj usługa Dropbox dla firm z galerii

Przed rozpoczęciem konfigurowania usługi Dropbox dla firm dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać usługa Dropbox dla firm z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać usługa Dropbox dla firm z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **usługa Dropbox dla firm**, wybierz opcję **usługa Dropbox dla firm** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja Dropbox dla Firm na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do usługi Dropbox dla firm

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do usługi Dropbox dla firm. Po decyzję, należy przypisać użytkowników i/lub grup do usługi Dropbox dla firm, wykonując instrukcje podane w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Dropbox dla firm

* Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisany do usługi Dropbox dla firm, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do usługi Dropbox dla firm, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurowanie automatycznej aprowizacji użytkowników do usługi Dropbox dla firm 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w usłudze Dropbox dla firm w oparciu przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego do usługi Dropbox dla firm, wykonując instrukcje podane w [Dropbox dla firm pojedynczego logowania jednokrotnego samouczka](dropboxforbusiness-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla usługi Dropbox dla firm w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **usługa Dropbox dla firm**.

    ![Link aplikacji Dropbox dla Firm na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** kliknij **Autoryzuj**. Dropbox dla okna dialogowego logowania firmy zostanie otwarty w nowym oknie przeglądarki.

    ![Inicjowanie obsługi ](common/provisioning-oauth.png)

6. Na **logowania do usługi Dropbox dla firm, aby połączyć się z usługą Azure AD** okno dialogowe, zaloguj się w usłudze Dropbox dla firm dzierżawy i zweryfikować Twoją tożsamość.

    ![Usługa Dropbox dla firm logowania](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po wykonaniu kroków 5 i 6 kliknij **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z usługi Dropbox dla firm. Jeśli połączenie nie powiedzie się, upewnij się, że w usłudze Dropbox dla firm konta ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-oauth.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Dropbox**.

    ![Mapowania użytkownika w usłudze Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Dropbox w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Dropbox do operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkowników usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do usługi Dropbox**.

    ![Mapowania grup w usłudze Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi Dropbox w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w usłudze Dropbox dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty grupy usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Dropbox, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

16. Zdefiniować użytkowników i/lub grup, które chcesz aprowizować do usługi Dropbox, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi w usłudze Dropbox.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika
 
* Dropbox nie obsługuje zawieszanie zaproszonych użytkowników. Jeśli Zaproszono użytkownika jest wstrzymana, tego użytkownika zostaną usunięte.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

