---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler Beta dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do rozwiązania Zscaler w wersji Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 82f9746b8f2f6665506491c328841e6a88438472
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672955"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler Beta dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w wersji Beta rozwiązania Zscaler i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznej aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu rozwiązania Zscaler w wersji Beta.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Dzierżawy rozwiązania Zscaler w wersji Beta
* Konta użytkownika z uprawnieniami administratora w wersji Beta rozwiązania Zscaler

> [!NOTE]
> Inicjowania obsługi administracyjnej integracji usługi Azure AD opiera się na interfejsie API rozwiązania Zscaler Standard SCIM Beta, który jest dostępny dla deweloperów rozwiązania Zscaler Beta dla kont z pakietem Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Dodawanie usługi Zscaler Beta z galerii

Przed skonfigurowaniem rozwiązania Zscaler Beta dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać rozwiązania Zscaler w wersji Beta z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler w wersji Beta z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler Beta**, wybierz pozycję **Zscaler Beta** na panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Przypisywanie użytkowników do wersji Beta rozwiązania Zscaler

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do rozwiązania Zscaler w wersji Beta. Po decyzję, należy przypisać użytkowników i/lub grup do rozwiązania Zscaler wersji Beta, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Ważne wskazówki dotyczące przypisywania użytkowników do wersji Beta rozwiązania Zscaler

* Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisane do rozwiązania Zscaler w wersji Beta do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do rozwiązania Zscaler w wersji Beta, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurowanie automatycznej aprowizacji użytkowników do wersji Beta rozwiązania Zscaler

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w wersji Beta rozwiązania Zscaler oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla rozwiązania Zscaler Beta, wykonując instrukcje podane w [Beta rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-beta-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla rozwiązania Zscaler w wersji Beta w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Beta rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Beta rozwiązania Zscaler**.

    ![Link usługi Zscaler Beta na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** Twojego konta rozwiązania Zscaler w wersji Beta, zgodnie z opisem w kroku 6.

6. Aby uzyskać **adres URL dzierżawy** i **klucz tajny tokenu**, przejdź do **Administracja > Ustawienia uwierzytelniania** w interfejsie użytkownika portalu rozwiązania Zscaler w wersji Beta i ,kliknijprzycisk **SAML** w obszarze **typ uwierzytelniania**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **skonfigurować SAML** otworzyć **konfiguracji SAML** opcje.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Wybierz **aprowizacji Enable SCIM-Based** można pobrać **podstawowy adres URL** i **tokenu elementu nośnego**, następnie Zapisz ustawienia. Kopiuj **podstawowy adres URL** do **adres URL dzierżawy**, i **tokenu elementu nośnego** do **klucz tajny tokenu** w witrynie Azure portal.

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z rozwiązania Zscaler w wersji Beta. Jeśli połączenie nie powiedzie się, upewnij się, że konta w wersji Beta rozwiązania Zscaler ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do wersji Beta rozwiązania Zscaler**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD w wersji Beta rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w wersji Beta rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do wersji Beta rozwiązania Zscaler**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD w wersji Beta rozwiązania Zscaler w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w wersji Beta rozwiązania Zscaler dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD provisioning service dla rozwiązania Zscaler Beta, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na rozwiązania Zscaler w wersji Beta, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej w wersji Beta rozwiązania Zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, usługi w wersji Beta rozwiązania Zscaler inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
