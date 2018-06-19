---
title: 'Samouczek: Konfigurowanie usługi Zendesk dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do usługi Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 3f5f489328c44d090091e767c09140043604bc9e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35942727"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Zendesk dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w Zendesk i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu Zendesk. 

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące wymagania wstępne:

*   Dzierżawa usługi Azure AD
*   Dzierżawcy Zendesk z [Enterprise](https://www.zendesk.com/product/pricing/) plan lub lepiej jest włączone 
*   Konto użytkownika z uprawnieniami administratora w Zendesk 

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [interfejsu API Rest usługi Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), który jest dostępny dla zespołów Zendesk na Enterprise plan lub większą.

## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie Zendesk z galerii
Przed skonfigurowaniem usługi Zendesk dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać Zendesk z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Zendesk z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać Zendesk, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Zendesk**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. W panelu wyników wybierz **Zendesk**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać Zendesk do listy aplikacji SaaS.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Przypisywanie użytkowników do usługi Zendesk

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD. 

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do usługi Zendesk. Po decyzję, można przypisać użytkowników i/lub grup usługi Zendesk zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Zendesk

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD Zendesk próba automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do usługi Zendesk, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej Zendesk 

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Zendesk oparciu o przypisania użytkownika i/lub grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla Zendesk, wykonując instrukcje podane w [Zendesk pojedynczego logowania jednokrotnego samouczek](zendesk-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od użytkownika automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla usługi Zendesk w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Zendesk z listy aplikacji SaaS.
 
    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.
    
    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **administratora**, **klucz tajny tokenu**, i **domeny** konta programu Zendesk. Przykłady te wartości są:

    *   W **administratora** pola, wypełnij nazwę użytkownika konta administratora w dzierżawie usługi Zendesk. Przykład: admin@contoso.com.

    *   W **klucz tajny tokenu** pola, wypełnij tajny tokenu, zgodnie z opisem w kroku 6.

    *   W **domeny** pola, wypełnij poddomeny dzierżawy usługi Zendesk.
    Przykład: dla konta z adresem URL dzierżawy https://my-tenant.zendesk.com, użytkownika domeny podrzędnej byłaby **Moje dzierżawy**.

6. **Klucz tajny tokenu** dla programu Zendesk konto znajduje się w **Administracja > interfejsu API > Ustawienia**. 

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk inicjowania obsługi administracyjnej.](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z usługi Zendesk. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto usługi Zendesk ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Zendesk**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD do usługi Zendesk w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Zendesk dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do usługi ZenDesk**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi Zendesk w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania grup w Zendesk dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](./../active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Zendesk, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać Zendesk, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![Inicjowanie obsługi usługi Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD usługi na Zendesk inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika
* Zendesk obsługuje użycia grupy użytkowników tylko role agenta. Aby uzyskać więcej informacji, zapoznaj się [Zendesk w dokumentacji](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
