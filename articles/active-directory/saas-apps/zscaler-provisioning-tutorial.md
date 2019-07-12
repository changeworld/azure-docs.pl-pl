---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do rozwiązania Zscaler
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: dd8335442cd370e0478c029a927c71e26fe6ef1b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672853"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w rozwiązania Zscaler i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu rozwiązania Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Dzierżawy rozwiązania Zscaler
* Konto użytkownika rozwiązania Zscaler z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi usługi Azure AD opiera się na interfejsie API Standard SCIM rozwiązania Zscaler, który jest dostępny dla deweloperów rozwiązania Zscaler dla kont z pakietem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie rozwiązania Zscaler z galerii

Przed skonfigurowaniem rozwiązania Zscaler dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać rozwiązania Zscaler z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązania Zscaler**, wybierz opcję **rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Rozwiązania Zscaler na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Przypisywanie użytkowników do rozwiązania Zscaler

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do rozwiązania Zscaler. Po decyzję, należy przypisać użytkowników i/lub grup do rozwiązania Zscaler, wykonując instrukcje podane w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Ważne wskazówki dotyczące przypisywania użytkowników do rozwiązania Zscaler

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do rozwiązania Zscaler do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do rozwiązania Zscaler, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurowanie automatycznej aprowizacji użytkowników do rozwiązania Zscaler

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w rozwiązania Zscaler oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla rozwiązania Zscaler, wykonując instrukcje podane w [rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla rozwiązania Zscaler w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **rozwiązania Zscaler**.

    ![Łącze rozwiązania Zscaler na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** Twojego konta rozwiązania Zscaler zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **klucz tajny tokenu**, przejdź do **Administracja > Ustawienia uwierzytelniania** w interfejsie użytkownika portalu rozwiązania Zscaler i kliknąć **SAML** w obszarze **typ uwierzytelniania**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **skonfigurować SAML** otworzyć **konfiguracji SAML** opcje.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Wybierz **aprowizacji Enable SCIM-Based** można pobrać **podstawowy adres URL** i **tokenu elementu nośnego**, następnie Zapisz ustawienia. Kopiuj **podstawowy adres URL** do **adres URL dzierżawy**, i **tokenu elementu nośnego** do **klucz tajny tokenu** w witrynie Azure portal.

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje rozwiązania Zscaler konto ma uprawnienia administratora i spróbuj ponownie.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/notification.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do rozwiązania Zscaler**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do rozwiązania Zscaler**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w rozwiązania Zscaler dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, usługi dla rozwiązania Zscaler inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Zdefiniować użytkowników i/lub grup, które chcesz, aby obsługiwać je na rozwiązania Zscaler, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/scoping.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na rozwiązania Zscaler.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
