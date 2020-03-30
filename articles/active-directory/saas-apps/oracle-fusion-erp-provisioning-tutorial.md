---
title: 'Samouczek: Konfigurowanie rozwiązania Oracle Fusion ERP do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania obsługi konta użytkowników do oracle fusion ERP.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061203"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie oracle fusion ERP do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w oracle fusion ERP i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do oracle fusion ERP.

> [!NOTE]
>  W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Najemca [Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Konto użytkownika w Oracle Fusion ERP z uprawnieniami administratora.

## <a name="assign-users-to-oracle-fusion-erp"></a>Przypisywanie użytkowników do oracle fusion ERP 
Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do oracle fusion ERP. Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do Oracle Fusion ERP, postępując zgodnie z instrukcjami tutaj:
 
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Oracle Fusion ERP 

 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do rozwiązania Oracle Fusion ERP w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do oracle fusion ERP należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Konfigurowanie rozwiązania Oracle Fusion ERP do inicjowania obsługi administracyjnej

Przed skonfigurowaniem oracle fusion ERP do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w oracle fusion ERP.

1. Zaloguj się do [konsoli administracyjnej Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Kliknij nawigator w lewym górnym górnym rogu. W obszarze **Narzędzia**wybierz pozycję **Konsola zabezpieczeń**.

    ![Oracle Fusion ERP Dodaj SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Przejdź do **pozycji Użytkownicy**.
    
    ![Oracle Fusion ERP Dodaj SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Zapisz nazwę użytkownika i hasło dla konta administratora, którego użyjesz do zalogowania się do konsoli administracyjnej Oracle Fusion ERP. Te wartości należy wprowadzić w polach Nazwa użytkownika i **hasło** **administratora** na karcie Inicjowanie obsługi administracyjnej aplikacji Oracle Fusion ERP w witrynie Azure portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Dodaj Oracle Fusion ERP z galerii

Aby skonfigurować oracle fusion ERP do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Oracle Fusion ERP z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać oracle fusion ERP z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Oracle Fusion ERP**, wybierz **Oracle Fusion ERP** w panelu wyników.

    ![Aplikacja Oracle Fusion ERP na liście wyników](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w oracle fusion ERP 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w oracle fusion ERP na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla Oracle Fusion ERP, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym SCIM firmy Oracle Fusion ERP, zobacz INTERFEJS API REST, aby zapoznać się [z typowymi funkcjami w chmurze Oracle Applications Cloud.](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Fuze w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Oracle Fusion ERP**.

    ![Link do aplikacji Oracle Fusion ERP na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **adresie URL dzierżawy**. Wprowadź nazwę użytkownika administratora i hasło pobrane wcześniej w polach Nazwa użytkownika i **hasło administratora.** **Admin Username** Kliknij **na połączenie testowe** między usługą Azure AD i Oracle Fusion ERP. 

    ![Oracle Fusion ERP Dodaj SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z oracle fusion ERP**.

    ![Oracle Fusion ERP Dodaj SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Oracle Fusion ERP w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Oracle Fusion ERP dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Oracle Fusion ERP Dodaj SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z oracle fusion ERP**.

    ![Mapowania grup Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Oracle Fusion ERP w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Oracle Fusion ERP dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla oracle fusion ERP, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Oracle Fusion ERP, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

    Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w oracle fusion ERP.

    Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Oracle Fusion ERP obsługuje tylko uwierzytelnianie podstawowe dla punktu końcowego SCIM.
* Oracle Fusion ERP nie obsługuje inicjowania obsługi administracyjnej grup.
* Role w oracle fusion ERP są mapowane do grup w usłudze Azure AD. Aby przypisać role użytkownikom w oracle fusion ERP z usługi Azure AD, należy przypisać użytkowników do żądanych grup usługi Azure AD, które są nazwane po rolach w oracle fusion ERP.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
