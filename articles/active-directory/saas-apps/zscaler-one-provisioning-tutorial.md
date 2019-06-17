---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie aprowizować i anulować aprowizację kont użytkowników do jednego rozwiązania Zscaler
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
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706602"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler jeden dla automatycznej aprowizacji użytkowników

Ten samouczek przedstawia kroki do wykonania w jednym rozwiązania Zscaler i usługi Azure Active Directory (Azure AD) do konfigurowania usługi Azure AD automatycznie aprowizować i anulowanie aprowizacji użytkowników i grup do jednego rozwiązania Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznika, który bazuje na usłudze usługa aprowizowania użytkowników w usłudze Azure AD. Aby uzyskać informacji na temat tej usługi nie, jak działa i często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi aplikacjom software-as-a-service (SaaS) w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ten łącznik jest obecnie dostępna w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz:

* Dzierżawa usługi Azure AD.
* Rozwiązania Zscaler jednej dzierżawy.
* Konto użytkownika w jednym rozwiązania Zscaler z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD zależy od interfejsu API rozwiązania Zscaler jeden standard SCIM. Ten interfejs API jest dostępna dla jednego rozwiązania Zscaler deweloperów dla kont z pakietem Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Dodaj rozwiązania Zscaler jednego z witryny Azure Marketplace

Przed skonfigurowaniem rozwiązania Zscaler jednego użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać jednego rozwiązania Zscaler w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać jednego rozwiązania Zscaler z witryny Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **jednego rozwiązania Zscaler** i wybierz **jednego rozwiązania Zscaler** z panelu wyników. Aby dodać aplikację, wybierz **Dodaj**.

    ![Na liście wyników jednego rozwiązania Zscaler](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Przypisywanie użytkowników do jednego rozwiązania Zscaler

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do jednego rozwiązania Zscaler. Aby przypisać tych użytkowników lub grup do jednego rozwiązania Zscaler, postępuj zgodnie z instrukcjami [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Ważne wskazówki dotyczące przypisywania użytkowników do jednego rozwiązania Zscaler

* Firma Microsoft zaleca, aby przypisać jeden użytkownik usługi Azure AD do rozwiązania Zscaler jednej, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników lub grup.

* Kiedy użytkownik przypisany do jednego rozwiązania Zscaler wybierz wszystkie prawidłową rolą specyficzne dla aplikacji, jeśli to możliwe, w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurowanie automatycznej aprowizacji użytkowników do jednego rozwiązania Zscaler

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi aprowizacji usługi Azure AD. Umożliwia tworzenie, aktualizowanie i wyłączyć użytkowników lub grup w jednym rozwiązania Zscaler na podstawie użytkownika lub przypisań do grup w usłudze Azure AD.

> [!TIP]
> Ponadto można włączyć opartej na SAML logowania jednokrotnego dla jednego rozwiązania Zscaler. Postępuj zgodnie z instrukcjami w [jednego rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-One-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla jednego rozwiązania Zscaler w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **jednego rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **jednego rozwiązania Zscaler**.

    ![Rozwiązania Zscaler jednego linku na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Jeden tryb obsługi administracyjnej rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. W obszarze **poświadczeń administratora** sekcji, wypełnij **adres URL dzierżawy** i **klucz tajny tokenu** pola z ustawieniami dla jednego rozwiązania Zscaler konta zgodnie z opisem w kroku 6.

6. Aby uzyskać dzierżawy adresu URL i token wpisu tajnego, przejdź do **administracji** > **ustawienia uwierzytelniania** w portalu rozwiązania Zscaler jednego interfejsu użytkownika. W obszarze **typ uwierzytelniania**, wybierz opcję **SAML**.

    ![Ustawienia uwierzytelniania jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Wybierz **skonfigurować SAML** otworzyć **skonfigurować SAML** opcje.

    ![Rozwiązania Zscaler jeden skonfigurować SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Wybierz **aprowizacji Enable SCIM-Based** można pobrać ustawień w **podstawowy adres URL** i **tokenu elementu nośnego**. Następnie Zapisz ustawienia. Kopiuj **podstawowy adres URL** ustawienie **adres URL dzierżawy** w witrynie Azure portal. Kopiuj **tokenu elementu nośnego** ustawienie **klucz tajny tokenu** w witrynie Azure portal.

7. Po wypełnieniu pól wyświetlane w kroku 5 wybierz **Testuj połączenie** aby upewnij się, że usługi Azure AD można połączyć się z jednego rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje rozwiązania Zscaler jedno konto ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** pole, wprowadź adres e-mail osoby lub grupy do odbierania powiadomień błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria** pole wyboru.

    ![Wiadomość E-mail z powiadomieniem jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Wybierz pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do jednego rozwiązania Zscaler**.

    ![Synchronizacja użytkownika jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do jednego rozwiązania Zscaler w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w jednym rozwiązania Zscaler operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Jednego rozwiązania Zscaler pasujące atrybuty użytkownika](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do jednego rozwiązania Zscaler**.

    ![Rozwiązania Zscaler jednej grupy synchronizacji](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do jednego rozwiązania Zscaler w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w jednym rozwiązania Zscaler operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Jednego rozwiązania Zscaler pasujące atrybuty grupy](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtrów określania zakresu, postępuj zgodnie z instrukcjami [zakresu filtru samouczek](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, usługi dla jednego rozwiązania Zscaler inicjowania obsługi administracyjnej w **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

    ![Jeden stan inicjowania obsługi administracyjnej rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników lub grup, które mają dla aprowizacji funkcji do jednego rozwiązania Zscaler. W **ustawienia** wybierz wartości, które mają w **zakres**.

    ![Zakres jednego rozwiązania Zscaler](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**.

    ![Zscaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej trwa dłużej niż synchronizacje nowsze. Występują one co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. 

Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków, aby raport o działaniach aprowizacji. Raport w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD usługi na rozwiązania Zscaler One inicjowania obsługi administracyjnej.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
