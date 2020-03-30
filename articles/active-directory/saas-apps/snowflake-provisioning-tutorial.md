---
title: 'Samouczek: Konfigurowanie płatka śniegu do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do płatka śniegu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063164"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie płatka śniegu do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w płatka śniegu i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do płatka śniegu.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca płatka śniegu](https://www.Snowflake.com/pricing/).
* Konto użytkownika w płatek śniegu z uprawnieniami administratora.

## <a name="assigning-users-to-snowflake"></a>Przypisywanie użytkowników do płatka śniegu

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do płatka śniegu. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Snowflake, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Ważne wskazówki dotyczące przypisywania użytkowników do płatka śniegu

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do płatka śniegu, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do płatka śniegu należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-snowflake-for-provisioning"></a>Konfigurowanie płatka śniegu do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Płatek śniegu do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy włączyć inicjowanie obsługi administracyjnej scim na płatek śniegu.

1. Zaloguj się do konsoli administracyjnej płatka śniegu. Wprowadź zapytanie pokazane poniżej w wyróżnionym arkuszu i kliknij przycisk **Uruchom**.

    ![Konsola administracyjna płatka śniegu](media/Snowflake-provisioning-tutorial/image00.png)

2.  Token dostępu SCIM zostanie wygenerowany dla dzierżawy płatka śniegu. Aby go odzyskać, kliknij link wyróżniony poniżej.

    ![Płatek śniegu Dodaj SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Skopiuj wygenerowaną wartość tokenu i kliknij przycisk **Gotowe**. Ta wartość zostanie wprowadzona w polu **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji Płatek śniegu w witrynie Azure portal.

    ![Płatek śniegu Dodaj SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Dodaj płatek śniegu z galerii

Aby skonfigurować płatek śniegu do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodaćSnowflake z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać płatek śniegu z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **płatek śniegu**, wybierz **Płatek śniegu** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja Snowflake na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w płatkach śniegu 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w płatku śniegu na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć jednokrotne logowanie oparte na SAML dla płatka śniegu, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego płatka śniegu.](Snowflake-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla płatka śniegu w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz opcję **Płatek śniegu**.

    ![Link aplikacji Snowflake na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora `https://<Snowflake Account URL>/scim/v2` wprowadź adres URL dzierżawy. Przykład adresu URL dzierżawy:`https://acme.snowflakecomputing.com/scim/v2`

6. Wprowadź wartość **tokenu uwierzytelniania SCIM** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z płatem śniegu. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Snowflake ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z płatkami śniegu**.

    ![Mapowania użytkowników płatka śniegu](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do płatka śniegu w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Płatek śniegu dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika płatka śniegu](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z płatem śniegu**.

    ![Mapowania grup płatków śniegu](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do płatka śniegu w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Płatek śniegu dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy płatka śniegu](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla płatka śniegu, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić płatek śniegu, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.** Jeśli ta opcja nie jest dostępna, skonfiguruj wymagane pola w obszarze Poświadczenia administratora, kliknij przycisk **Zapisz** i odśwież stronę. 

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

16. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

    Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć szczegóły **synchronizacji** sekcji do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD na płatek śniegu.

    Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Tokeny SCIM wygenerowane przez płatek śniegu wygasają w ciągu 6 miesięcy. Należy pamiętać, że te muszą być odświeżane, zanim wygasną, aby umożliwić synchronizacji inicjowania obsługi administracyjnej, aby kontynuować pracę. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](../app-provisioning/check-status-user-account-provisioning.md)
