---
title: 'Samouczek: Konfigurowanie Spark firmy Cisco dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do platformy Spark firmy Cisco.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77dab6ad0480bc1565c219766d17211995dcfc20
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056949"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Spark firmy Cisco dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Cisco Spark i Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników do platformy Spark firmy Cisco.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Dzierżawy Spark firmy Cisco
* Konto użytkownika na platformie Cisco Spark z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [Cisco Spark Webservice](https://developer.webex.com/getting-started.html), który jest dostępny dla zespołów Spark firmy Cisco.

## <a name="adding-cisco-spark-from-the-gallery"></a>Dodawanie aplikacji Cisco Spark z galerii

Przed rozpoczęciem konfigurowania platformy Spark firmy Cisco dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać Cisco Spark z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Cisco Spark z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Cisco Spark**, wybierz pozycję **Cisco Spark** w panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Cisco Spark na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-cisco-spark"></a>Przypisywanie użytkowników do platformy Spark firmy Cisco

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed konfigurowania i włączenie automatycznej aprowizacji użytkowników, należy zdecydować, użytkowników, którzy w usłudze Azure AD muszą mieć dostęp do platformy Spark firmy Cisco. Po decyzję, możesz przypisać użytkowników do platformy Spark Cisco, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Ważne wskazówki dotyczące przypisywania użytkowników do platformy Spark firmy Cisco

* Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisany do platformy Spark Cisco do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników.

* Podczas przypisywania użytkowników do platformy Spark Cisco, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Konfigurowanie automatycznej aprowizacji użytkowników do platformy Spark firmy Cisco

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników na platformie Spark Cisco, w oparciu o przypisania użytkownika w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Cisco platformy Spark w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Cisco Spark**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Cisco Spark**.

    ![Link aplikacji Cisco Spark na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy**, i **klucz tajny tokenu** konta usługi Spark firmy Cisco.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/secrettoken1.png)

    * W **adres URL dzierżawy** pola, wypełnij adresu URL interfejsu API Cisco Spark Standard SCIM dla dzierżawy, który ma postać `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, gdzie `[Tenant ID]` jest ciągiem alfanumerycznym o długości, zgodnie z opisem w kroku 6.

    * W **klucz tajny tokenu** pola, wypełnij klucz tajny tokenu, zgodnie z opisem w kroku 6.

6. **Identyfikator dzierżawy** i **klucz tajny tokenu** dla platformy Spark Cisco konta można znaleźć, logując się do [witryny dla deweloperów platformy Spark Cisco](https://developer.webex.com/) przy użyciu konta administratora. Po zalogowaniu -

   * Przejdź do [Getting Started page](https://developer.webex.com/getting-started.html)

   * Przewiń w dół do [sekcji uwierzytelnianie](https://developer.webex.com/getting-started.html#authentication)
  
    ![Token uwierzytelniania Spark firmy Cisco](./media/cisco-spark-provisioning-tutorial/SecretToken.png)

   * Ciąg alfanumeryczny w polu jest Twoja **klucz tajny tokenu**. Skopiuj ten token do Schowka

   * Przejdź do [Pobierz moje własne szczegóły strony](https://developer.webex.com/endpoint-people-me-get.html)
       * Upewnij się, że tryb testowy jest ON
       * Wpisz słowo "Bearer" spację, a następnie wklej klucz tajny tokenu w polu autoryzacji ![tokenu uwierzytelniania Spark firmy Cisco](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Kliknij przycisk Uruchom

   * Tekst odpowiedzi z prawej strony **identyfikator dzierżawy** jest wyświetlany jako "z identyfikatorem organizacji":

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można do platformy Spark firmy Cisco. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto platformy Spark Cisco ma uprawnienia administratora i spróbuj ponownie.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do platformy Spark Cisco**.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Spark Cisco w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Spark firmy Cisco dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD usługi Spark Cisco inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na Cisco Spark, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Spark Cisco inicjowania obsługi administracyjnej](./media/cisco-spark-provisioning-tutorial/Save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na platformie Spark firmy Cisco.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Cisco Spark jest obecnie w fazie wczesnego testowania pola (EFT) firmy Cisco. Aby uzyskać więcej informacji, skontaktuj się z pomocą [zespołem pomocy technicznej firmy Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
