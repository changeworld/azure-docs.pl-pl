---
title: 'Samouczek: Konfigurowanie systemu Zendesk dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do systemu Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278585"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie systemu Zendesk dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Zendesk i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup do systemu Zendesk.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Dzierżawcy systemu Zendesk z [Enterprise](https://www.zendesk.com/product/pricing/) plan lub lepiej jest włączona
* Konto użytkownika w usłudze Zendesk z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest systemu Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), co jest dostępne dla zespołów systemu Zendesk z planem Enterprise lub większą.

## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie aplikacji Zendesk z galerii

Przed skonfigurowaniem systemu Zendesk dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać systemu Zendesk z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać systemu Zendesk z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zendesk**, wybierz pozycję **Zendesk** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Zendesk na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Przypisywanie użytkowników do systemu Zendesk

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do systemu Zendesk. Gdy decyzję, można przypisać użytkowników i/lub grup zendesk zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Ważne wskazówki dotyczące przypisywania użytkowników do systemu Zendesk

* Role systemu Zendesk są dynamiczne i automatycznie uzupełniane w witrynie Azure portal, interfejsu użytkownika już dziś. Przed przypisaniem Zendesk ról do użytkowników, upewnij się, że synchronizacja początkowa jest finalizowane przy użyciu systemu Zendesk, aby pobrać najnowsze ról w dzierżawie usługi Zendesk.

* Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisane do systemu Zendesk do przetestowania Twojej początkowej automatyczne aprowizowanie użytkowników konfiguracji. Dodatkowych użytkowników i/lub grup można przypisać później po pomyślnych testów.
  
* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do systemu Zendesk do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do systemu Zendesk, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfigurowanie automatycznej aprowizacji użytkowników do systemu Zendesk 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w usłudze Zendesk oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla systemu Zendesk, wykonując instrukcje podane w [Zendesk pojedynczego logowania jednokrotnego samouczek](zendesk-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla systemu Zendesk w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Zendesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Zendesk**.

    ![Link aplikacji Zendesk na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **nazwa użytkownika administratora**, **klucz tajny tokenu**, i **domeny** konta użytkownika systemu Zendesk. Przykłady te wartości są:

   * W **nazwa użytkownika administratora** pola, wypełnij nazwę użytkownika konta administratora w dzierżawie usługi Zendesk. Przykład: admin@contoso.com.

   * W **klucz tajny tokenu** pola, wypełnij token wpisu tajnego, zgodnie z opisem w kroku 6.

   * W **domeny** pola, wypełnij poddomeny dzierżawy systemu Zendesk.
     Przykład: Dla konta, którego adres URL dzierżawy `https://my-tenant.zendesk.com`, Twojej domeny podrzędnej byłaby **Moje dzierżawy**.

6. **Klucz tajny tokenu** dla Twojego systemu Zendesk konto znajduje się w **Administrator > Interfejs API > Ustawienia**.
   Upewnij się, że **dostęp do tokenu** ustawiono **włączone**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z systemu Zendesk. Jeśli połączenie nie powiedzie się, upewnij się, że konta systemu Zendesk, ma uprawnienia administratora i spróbuj ponownie.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do systemu Zendesk**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD zendesk w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Zendesk dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do systemu ZenDesk**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD zendesk w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w usłudze Zendesk dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD provisioning service dla systemu Zendesk, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na systemu Zendesk, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD provisioning service w systemie Zendesk.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Zendesk obsługuje użycie grupy dla użytkowników tylko role agenta. Aby uzyskać więcej informacji można znaleźć [dokumentacji firmy systemu Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Gdy rola niestandardowa zostanie przypisany do użytkownika i/lub grupy, usługa aprowizowania użytkowników automatyczne usługi Azure AD będzie także przypisać domyślna rola **agenta**. Tylko **agentów** można przypisać roli niestandardowej. Aby uzyskać więcej informacji, zapoznaj się z tym [dokumentacji interfejsu API w systemu Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
