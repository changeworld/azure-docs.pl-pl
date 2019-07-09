---
title: 'Samouczek: Konfigurowanie rozwiązania Zscaler ZSCloud dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub anulować aprowizację kont użytkowników do rozwiązania Zscaler ZSCloud.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 99c94792f48db7a932e670f05216bcea0e90a27c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672807"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie rozwiązania Zscaler ZSCloud dla automatycznej aprowizacji użytkowników

W tym samouczku dowiesz się, jak skonfigurować usługi Azure Active Directory (Azure AD), aby automatycznie przeprowadzać obsługę administracyjną i anulowanie aprowizacji użytkowników i/lub grup w celu rozwiązania Zscaler ZSCloud.

> [!NOTE]
> W tym samouczku opisano łącznika, który jest oparty na usługa aprowizowania użytkowników w usłudze Azure AD. Ważne szczegóły dotyczące jaki ta usługa jest i zobacz, jak działa i odpowiedzi na często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Azure warunków użytkowania przeznaczonych do funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, potrzebne są następujące elementy:

* Dzierżawa usługi Azure AD.
* A Zscaler ZSCloud tenant.
* Konto użytkownika w ZSCloud rozwiązania Zscaler z uprawnieniami administratora.

> [!NOTE]
> Inicjowania obsługi administracyjnej integracji usługi Azure AD opiera się na interfejsie API rozwiązania Zscaler Standard SCIM ZSCloud, który jest dostępny dla konta przedsiębiorstwa.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Dodaj rozwiązania Zscaler ZSCloud z galerii

Przed skonfigurowaniem rozwiązania Zscaler ZSCloud dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać rozwiązania Zscaler ZSCloud z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**:

![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

![Wybierz nową aplikację](common/add-new-app.png)

W polu wyszukiwania wprowadź **ZSCloud rozwiązania Zscaler**. Wybierz **ZSCloud rozwiązania Zscaler** w wyniki, a następnie wybierz **Dodaj**.

![Na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Assign users to Zscaler ZSCloud

Użytkownicy usługi Azure AD muszą być przypisani dostęp do wybranych aplikacji, zanim rozpoczną korzystanie z nich. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które są przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, możesz zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do rozwiązania Zscaler ZSCloud. Po podjęciu decyzji, tych użytkowników i grupy można przypisać do rozwiązania Zscaler ZSCloud, postępując zgodnie z instrukcjami wyświetlanymi w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Ważne wskazówki dotyczące przypisywania użytkowników do ZSCloud rozwiązania Zscaler

* Zaleca się najpierw przypisać pojedynczego użytkownika usługi Azure AD do rozwiązania Zscaler ZSCloud, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać większą liczbę użytkowników i grup.

* Gdy użytkownik jest przypisany do rozwiązania Zscaler ZSCloud, musisz wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurowanie automatycznej aprowizacji użytkowników

Ta sekcja przeprowadzi Cię przez kroki dotyczące konfigurowania usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i grup w ZSCloud rozwiązania Zscaler na podstawie użytkownika i przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć opartej na SAML logowania jednokrotnego dla rozwiązania Zscaler ZSCloud. Jeśli postępuj zgodnie z instrukcjami [ZSCloud rozwiązania Zscaler pojedynczego logowania jednokrotnego samouczek](zscaler-zsCloud-tutorial.md). Logowanie jednokrotne, można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, ale te dwie funkcje uzupełniają się wzajemnie.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **ZSCloud rozwiązania Zscaler**:

    ![Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ZSCloud rozwiązania Zscaler**:

    ![Lista aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** karty:

    ![Zscaler ZSCloud Provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**:

    ![Ustaw tryb obsługi administracyjnej](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. W **poświadczeń administratora** sekcji, wprowadź **adres URL dzierżawy** i **klucz tajny tokenu** Twojego konta rozwiązania Zscaler ZSCloud zgodnie z opisem w następnym kroku.

6. Aby uzyskać **adres URL dzierżawy** i **klucz tajny tokenu**, przejdź do **administracji** > **ustawienia uwierzytelniania** w rozwiązania Zscaler ZSCloud portal i wybierz pozycję **SAML** w obszarze **typ uwierzytelniania**:

    ![Ustawienia uwierzytelniania ZSCloud rozwiązania Zscaler](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Wybierz **skonfigurować SAML** otworzyć **skonfigurować SAML** okna:

    ![Skonfigurować okno SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Wybierz **Enable SCIM-Based aprowizacji** i skopiuj **podstawowy adres URL** i **tokenu elementu nośnego**, a następnie Zapisz ustawienia. W witrynie Azure portal, Wklej **podstawowy adres URL** do **adres URL dzierżawy** pole i **tokenu elementu nośnego** do **klucz tajny tokenu** pole.

7. Po wprowadzeniu wartości w **adres URL dzierżawy** i **klucz tajny tokenu** pól, zaznacz **Testuj połączenie** się upewnić, że usługa Azure AD może nawiązać ZSCloud rozwiązania Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto ZSCloud rozwiązania Zscaler ma uprawnienia administratora i spróbuj ponownie.

    ![Testowanie połączenia](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**:

    ![Konfigurowanie powiadomień e-mail](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Wybierz pozycję **Zapisz**.

10. W **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do ZscalerZSCloud**:

    ![Synchronizowanie użytkowników usługi Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler ZSCloud w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w ZSCloud rozwiązania Zscaler operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany.

    ![Mapowania atrybutów](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. W **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do ZscalerZSCloud**:

    ![Zsynchronizuj grupy usługi Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler ZSCloud w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w ZSCloud rozwiązania Zscaler dla operacji aktualizowania. Wybierz **Zapisz** aby zatwierdzić zmiany.

    ![Mapowania atrybutów](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Aby skonfigurować filtrów określania zakresu, zapoznaj się z instrukcjami w [samouczek filtru Scoping](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, usługi dla rozwiązania Zscaler ZSCloud inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji:

    ![Stan aprowizacji](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Zdefiniować użytkowników i/lub grup, które będą obsługiwać je na ZSCloud rozwiązania Zscaler, wybierając wartości w polu **zakres** w **ustawienia** sekcji:

    ![Wartości zakresu](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**:

    ![Wybierz opcję Zapisz](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i grupy definiowane w obszarze **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują, o co 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz monitorować postęp w **szczegóły synchronizacji** sekcji. Aby raport o działaniach aprowizacji, który w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na rozwiązania Zscaler ZSCloud, skorzystaj z linków.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
