---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do jednego rozwiązania Zscaler
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 26448d5056e58cf1110e825ad04c5123fca20684
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259443"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w jednego rozwiązania Zscaler i Azure Active Directory (Azure AD) do konfigurowania usługi Azure AD do automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup do jednego rozwiązania Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Rozwiązania Zscaler jednej dzierżawy
* Konto użytkownika w jednym rozwiązania Zscaler z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi usługi Azure AD opiera się na API Standard SCIM jednego rozwiązania Zscaler, który jest dostępny dla jednego rozwiązania Zscaler deweloperów dla kont z pakietem Enterprise.

## <a name="adding-zscaler-one-from-the-gallery"></a>Dodawanie rozwiązania Zscaler jednego z galerii

Przed skonfigurowaniem rozwiązania Zscaler jednego użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać rozwiązania Zscaler jednego z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać jednego rozwiązania Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **jednego rozwiązania Zscaler**, wybierz opcję **jednego rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Na liście wyników jednego rozwiązania Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-one"></a>Przypisywanie użytkowników do jednego rozwiązania Zscaler

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do jednego rozwiązania Zscaler. Po decyzję, należy przypisać do jednego rozwiązania Zscaler użytkowników i/lub grup zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Ważne wskazówki dotyczące przypisywania użytkowników do jednego rozwiązania Zscaler

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do jednego rozwiązania Zscaler do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do jednego rozwiązania Zscaler, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zscaler-one"></a>Konfigurowanie automatycznej aprowizacji użytkowników do jednego rozwiązania Zscaler

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w jednym rozwiązania Zscaler oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla jednego rozwiązania Zscaler, wykonując instrukcje podane w [jednego rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-One-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla jednego rozwiązania Zscaler w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **jednego rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **jednego rozwiązania Zscaler**.

    ![Rozwiązania Zscaler jednego linku na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** Twojego rozwiązania Zscaler jednego konta, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **klucz tajny tokenu**, przejdź do **Administracja > Ustawienia uwierzytelniania** rozwiązania Zscaler jeden interfejs użytkownika portalu i kliknij pozycję  **SAML** w obszarze **typ uwierzytelniania**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **skonfigurować SAML** otworzyć **konfiguracji SAML** opcje.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    Wybierz **aprowizacji Enable SCIM-Based** można pobrać **podstawowy adres URL** i **tokenu elementu nośnego**, następnie Zapisz ustawienia. Kopiuj **podstawowy adres URL** do **adres URL dzierżawy** i **tokenu elementu nośnego** do **klucz tajny tokenu** w witrynie Azure portal.

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z jednego rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje rozwiązania Zscaler jedno konto ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do jednego rozwiązania Zscaler**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do jednego rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w jednym rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do jednego rozwiązania Zscaler**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do jednego rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w jednym rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, usługi dla jednego rozwiązania Zscaler inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na rozwiązania Zscaler jeden, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na jeden rozwiązania Zscaler.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
