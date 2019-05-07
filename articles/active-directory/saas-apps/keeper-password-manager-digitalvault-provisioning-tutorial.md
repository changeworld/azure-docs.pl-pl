---
title: 'Samouczek: Konfigurowanie Menedżera haseł hodowca & cyfrowego magazynu dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników na Menedżera haseł hodowca & cyfrowego magazynu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: zchia
ms.openlocfilehash: b7b096bd1ecf0a4df37ed4de0cf618216dccc2bc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159468"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Menedżera haseł hodowca & cyfrowego magazynu dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest zademonstrowanie czynności wykonywanych w Menedżera haseł hodowca & cyfrowego magazynu i usługi Azure Active Directory (Azure AD) do konfigurowania usługi Azure AD do automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup w celu hodowca hasła Menedżer & cyfrowego magazynu.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Menedżer haseł hodowca & cyfrowego magazynu dzierżawy](https://keepersecurity.com/pricing.html?t=e)
* Konto użytkownika, w Menedżera haseł hodowca & cyfrowego magazynu z uprawnieniami administratora.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodaj Menedżera haseł hodowca & cyfrowego magazynu z galerii

Przed skonfigurowaniem Menedżera haseł hodowca & cyfrowego magazynu dla automatycznej aprowizacji użytkowników z usługą Azure AD, należy dodać Menedżera haseł hodowca & cyfrowego magazynu z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Menedżera haseł hodowca & cyfrowego magazynu z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Menedżera haseł hodowca & cyfrowego magazynu**, wybierz opcję **Menedżera haseł hodowca & cyfrowego magazynu** panel wyników, a następnie kliknij **Dodaj**przycisk, aby dodać aplikację.

    ![Usługa Keeper Password Manager & Digital Vault na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Przypisywanie użytkowników do Menedżera haseł hodowca & cyfrowego magazynu

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Menedżera haseł hodowca & cyfrowego magazynu. Po decyzję, można przypisać użytkowników i/lub grup do Menedżera haseł hodowca & cyfrowego magazynu zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Menedżera haseł hodowca & cyfrowego magazynu

* Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisane do Menedżera haseł hodowca & cyfrowego magazynu do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Gdy przypisanie użytkownika do Menedżera haseł hodowca & cyfrowego magazynu, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurowanie automatycznej aprowizacji użytkowników na Menedżera haseł hodowca & cyfrowego magazynu 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grupy w Menedżerze hasło hodowca & cyfrowego magazynu oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowanie jednokrotne dla Menedżera haseł hodowca & cyfrowego magazynu, postępując zgodnie z instrukcjami dostarczone w [Menedżera haseł hodowca & cyfrowego magazyn pojedynczego logowania jednokrotnego samouczek](keeperpasswordmanager-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Menedżera haseł hodowca & cyfrowego magazynu w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Menedżera haseł hodowca & cyfrowy Vault**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Keeper Password Manager & Digital Vault**.

    ![Link do usługi Keeper Password Manager & Digital Vault na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** hodowca Menedżera haseł i konta magazynu cyfrowych, zgodnie z opisem w kroku 6.

6. Zaloguj się do Twojej [konsoli administracyjnej hodowca](https://keepersecurity.com/console/#login). Kliknij pozycję **administratora** i wybierz istniejący węzeł lub Utwórz nową. Przejdź do **aprowizacji** kartę, a następnie wybierz pozycję **Dodaj metodę**.

    ![Konsola administracyjna hodowca](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-admin-console.png)

    Wybierz **Standard SCIM (System do zarządzania tożsamościami międzydomenowe**.

    ![Hodowca Dodaj Standard SCIM](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kliknij przycisk **tworzenie, inicjowanie obsługi administracyjnej Token**.

    ![Hodowca Tworzenie punktu końcowego](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Skopiuj wartości pól **adresu URL** i **tokenu** i wklej je do **adres URL dzierżawy** i **klucz tajny tokenu** w usłudze Azure AD. Kliknij przycisk **Zapisz** do zakończenia inicjowania obsługi administracyjnej instalacji na hodowca.

    ![Hodowca Utwórz Token](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-token.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można na Menedżera haseł hodowca & cyfrowego magazynu. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto menedżera haseł hodowca & cyfrowego magazynu ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory na Menedżera haseł hodowca & cyfrowego magazynu**.

    ![Hodowca mapowania użytkownika](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD na Menedżera haseł hodowca & cyfrowego magazynu w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Menedżera haseł hodowca & cyfrowego magazynu dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Hodowca atrybutów użytkownika](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory na Menedżera haseł hodowca & cyfrowego magazynu**.

    ![Mapowania posiadacza grup](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD na Menedżera haseł hodowca & cyfrowego magazynu w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w Menedżera haseł hodowca & cyfrowego magazynu dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Hodowca grupy atrybutów](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Menedżera haseł hodowca & cyfrowego magazynu, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na Menedżera haseł hodowca & cyfrowego magazynu, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Menedżera haseł hodowca & Vault cyfrowych.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Menedżer haseł hodowca & cyfrowego magazynu wymaga **wiadomości e-mail** i **nazwy użytkownika** mieć taką samą wartość źródła wszelkie aktualizacje do obu atrybutów będzie modyfikować inną wartość.
* Menedżer haseł hodowca & cyfrowego magazynu obsługi Usuwa użytkownika, nie tylko wyłączyć. Wyłączeni użytkownicy będą wyświetlane, jako zablokowany w interfejsie użytkownika konsoli administratora hodowca.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
