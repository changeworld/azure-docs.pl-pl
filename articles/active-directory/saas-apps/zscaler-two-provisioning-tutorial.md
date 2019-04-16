---
title: 'Samouczek: Skonfiguruj dwa rozwiązania Zscaler dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub anulowanie aprowizacji rozwiązania Zscaler dwóch kont użytkowników.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 837014fde6962f64d7da023a001a4c41089a0097
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578275"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Samouczek: Skonfiguruj dwa rozwiązania Zscaler dla automatycznej aprowizacji użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługi Azure Active Directory (Azure AD), aby automatycznie przeprowadzać obsługę administracyjną i anulowanie aprowizacji użytkowników i/lub grup w celu rozwiązania Zscaler dwa.

> [!NOTE]
> W tym samouczku opisano łącznika, który jest oparty na usługa aprowizowania użytkowników w usłudze Azure AD. Ważne szczegóły dotyczące jaki ta usługa jest i zobacz, jak działa i odpowiedzi na często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Azure warunków użytkowania przeznaczonych do funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebne są następujące elementy:

* Dzierżawa usługi Azure AD.
* Dzierżawy rozwiązania Zscaler dwa.
* Konto użytkownika w dwa rozwiązania Zscaler z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi usługi Azure AD opiera się na API Standard SCIM dwa rozwiązania Zscaler, który jest dostępny dla konta przedsiębiorstwa.

## <a name="add-zscaler-two-from-the-gallery"></a>Dodaj dwa rozwiązania Zscaler z galerii

Przed skonfigurowaniem rozwiązania Zscaler dwóch użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy dodać dwa rozwiązania Zscaler z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**:

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wprowadź **dwa rozwiązania Zscaler**. Wybierz **dwa rozwiązania Zscaler** w wyniki, a następnie wybierz **Dodaj**.

![Na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Przypisywanie użytkowników do dwóch rozwiązania Zscaler

Użytkownicy usługi Azure AD muszą być przypisani dostęp do wybranych aplikacji, zanim rozpoczną korzystanie z nich. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które są przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, możesz zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do rozwiązania Zscaler dwa. Po podjęciu decyzji, tych użytkowników i grupy można przypisać do dwóch rozwiązania Zscaler, postępując zgodnie z instrukcjami wyświetlanymi w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Ważne wskazówki dotyczące przypisywania użytkowników do dwóch rozwiązania Zscaler

* Zaleca się najpierw przypisać pojedynczego użytkownika usługi Azure AD do rozwiązania Zscaler dwóch, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać większą liczbę użytkowników i grup.

* Kiedy użytkownik przypisany do rozwiązania Zscaler dwóch należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznej aprowizacji użytkowników

Ta sekcja przeprowadzi Cię przez kroki dotyczące konfigurowania usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i grup w dwa rozwiązania Zscaler na podstawie użytkownika i przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć opartej na SAML logowanie jednokrotne dla rozwiązania Zscaler dwa. Jeśli postępuj zgodnie z instrukcjami [dwa rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-two-tutorial.md). Logowanie jednokrotne, można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, ale te dwie funkcje uzupełniają się wzajemnie.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **dwa rozwiązania Zscaler**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **dwa rozwiązania Zscaler**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** karty:

    ![Inicjowanie obsługi administracyjnej dwa rozwiązania Zscaler](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**:

    ![Ustaw tryb obsługi administracyjnej](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. W **poświadczeń administratora** sekcji, wprowadź **adres URL dzierżawy** i **klucz tajny tokenu** rozwiązania Zscaler dwa konta, zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **klucz tajny tokenu**, przejdź do **administracji** > **ustawienia uwierzytelniania** w dwa rozwiązania Zscaler Portal i wybierz pozycję **SAML** w obszarze **typ uwierzytelniania**:

    ![Ustawienia uwierzytelniania dwa rozwiązania Zscaler](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Wybierz **skonfigurować SAML** otworzyć **skonfigurować SAML** okna:

    ![Skonfigurować okno SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Wybierz **Enable SCIM-Based aprowizacji** i skopiuj **podstawowy adres URL** i **tokenu elementu nośnego**, a następnie Zapisz ustawienia. W witrynie Azure portal, Wklej **podstawowy adres URL** do **adres URL dzierżawy** pole i **tokenu elementu nośnego** do **klucz tajny tokenu** pole.

7. Po wprowadzeniu wartości w **adres URL dzierżawy** i **klucz tajny tokenu** pól, zaznacz **Testuj połączenie** się upewnić, że usługa Azure AD może nawiązać dwa rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że dwa rozwiązania Zscaler konto ma uprawnienia administratora i spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**:

    ![Konfigurowanie powiadomień e-mail](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Wybierz pozycję **Zapisz**.

10. W **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do ZscalerTwo**:

    ![Synchronizowanie użytkowników usługi Azure AD](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD w dwa rozwiązania Zscaler **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dwa rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany.

    ![Mapowania atrybutów](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. W **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do ZscalerTwo**:

    ![Zsynchronizuj grupy usługi Azure AD](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD w dwa rozwiązania Zscaler **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w dwa rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany.

    ![Mapowania atrybutów](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtrów określania zakresu, zapoznaj się z instrukcjami w [samouczek filtru Scoping](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, usługi dla rozwiązania Zscaler dwóch inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji:

    ![Stan aprowizacji](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Zdefiniować użytkowników i/lub grup, które będą obsługiwać je na dwa rozwiązania Zscaler, wybierając wartości w polu **zakres** w **ustawienia** sekcji:

    ![Wartości zakresu](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**:

    ![Wybierz opcję Zapisz](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i grupy definiowane w obszarze **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują, o co 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz monitorować postęp w **szczegóły synchronizacji** sekcji. Aby raport o działaniach aprowizacji, który w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na dwa rozwiązania Zscaler, skorzystaj z linków.

Aby uzyskać informacje o tym, jak odczytać aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
