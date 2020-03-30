---
title: 'Samouczek: Konfigurowanie leapsome dla automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do leapsome.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e5837887325c06f9140a3f40eb183139782e2a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057468"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie leapsome dla automatycznego inicjowania obsługi administracyjnej użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w leapsome i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i de-provision użytkowników i/lub grup leapsome.

> [!NOTE]
>  W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Leapsome najemcy.](https://www.Leapsome.com/en/pricing)
* Konto użytkownika w Leapsome z uprawnieniami administratora.

## <a name="assigning-users-to-leapsome"></a>Przypisywanie użytkowników do Leapsome

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Leapsome. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Leapsome, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Leapsome

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Leapsome do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Leapsome, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.


## <a name="setup-leapsome-for-provisioning"></a>Konfiguracja Leapsome do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli leapsome admin](https://www.Leapsome.com/app/#/login). Przejdź do **ustawień > ustawieniach administratora**.

    ![Konsola administracyjna Leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Przejdź do **integracji > inicjowania obsługi administracyjnej użytkownika scim**.

    ![Leapsome Dodaj SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Skopiuj **token uwierzytelniania SCIM**. Ta wartość zostanie wprowadzona w polu Token tajny na karcie Inicjowanie obsługi administracyjnej aplikacji Leapsome w witrynie Azure portal.

    ![Leapsome Tworzenie tokenu](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Dodaj Leapsome z galerii

Przed skonfigurowaniem Leapsome do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Leapsome z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Leapsome z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Leapsome**, wybierz **Leapsome** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Leapsome na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla leapsome 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Leapsome na podstawie przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne oparte na SAML leapsome , zgodnie z instrukcjami podanymi w [leapsome samouczek logowania jednokrotnego.](Leapsome-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla leapsome w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz opcję **Leapsome**.

    ![Łącze Leapsome na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://www.leapsome.com/api/scim` **adresie URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania SCIM** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z Leapsome. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Leapsome ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z przeskoczącymi**.

    ![Leapsome Mapowania użytkowników](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Leapsome w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Leapsome dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Leapsome atrybuty użytkownika](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory do przeskoczących**.

    ![Leapsome Mapowania grupowe](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Leapsome w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Leapsome dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Leapsome atrybuty grupy](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla leapsome, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Leapsome, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć sekcji **Szczegóły synchronizacji** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w trybie leapsome.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Leapsome wymaga **userName** być unikatowe.
* Leapsome pozwala tylko na zapisywanie służbowych adresów e-mail.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
