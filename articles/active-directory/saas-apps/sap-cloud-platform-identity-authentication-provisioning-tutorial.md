---
title: 'Samouczek: Konfigurowanie uwierzytelniania tożsamości platformy SAP cloud platform do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekprymprokowania kont użytkowników do uwierzytelniania tożsamości platformy SAP Cloud Platform.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060467"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie uwierzytelniania tożsamości platformy sap w chmurze do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze SAP Cloud Platform Identity Authentication i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do uwierzytelniania tożsamości platformy sap cloud.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa uwierzytelniania tożsamości platformy SAP w chmurze](https://cloudplatform.sap.com/pricing.html)
* Konto użytkownika w uwierzytelnianiu tożsamości platformy SAP Cloud z uprawnieniami administratora.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Przypisywanie użytkowników do uwierzytelniania tożsamości platformy SAP Cloud

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do uwierzytelniania tożsamości platformy sap cloud. Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do uwierzytelniania tożsamości platformy SAP Cloud, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Ważne wskazówki dotyczące przypisywania użytkowników do uwierzytelniania tożsamości platformy SAP Cloud

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do uwierzytelniania tożsamości platformy SAP Cloud Platform w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do uwierzytelniania tożsamości platformy SAP Cloud Platform należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Konfigurowanie uwierzytelniania tożsamości platformy SAP w chmurze do obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej uwierzytelniania tożsamości platformy SAP Cloud Platform](https://sapmsftintegration.accounts.ondemand.com/admin). Przejdź do **pozycji Użytkownicy & Autoryzacje > administratorzy**.

    ![Konsola administracyjna uwierzytelniania tożsamości platformy SAP Cloud](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Utwórz użytkownika administracyjnego i wybierz go.  

3.  W obszarze Konfigurowanie autoryzacji włącz przycisk przełączania względem **Zarządzanie użytkownikami** i **zarządzanie grupami**.

    ![Uwierzytelnianie tożsamości platformy sap cloud Add SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Otrzymasz wiadomość e-mail z aktywacją konta i ustawić hasło do **usługi uwierzytelniania tożsamości platformy SAP Cloud Platform.**

4.  Skopiuj **identyfikator użytkownika** i **hasło**. Wartości te zostaną wprowadzone odpowiednio w polach Nazwa użytkownika administratora i Hasło administratora na karcie Inicjowanie obsługi administracyjnej aplikacji uwierzytelniania tożsamości platformy SAP Cloud Platform w witrynie Azure portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania tożsamości platformy SAP cloud z galerii

Przed skonfigurowaniem uwierzytelniania tożsamości platformy SAP Cloud Platform do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać uwierzytelnianie tożsamości platformy SAP Cloud Platform z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać uwierzytelnianie tożsamości platformy SAP cloud z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **uwierzytelnianie tożsamości platformy SAP Cloud Platform**, wybierz pozycję **UWIERZYTELNIANIE TOŻSAMOŚCI PLATFORMY SAP** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja SAP Cloud Platform Identity Authentication na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej do uwierzytelniania tożsamości platformy SAP Cloud 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w uwierzytelnianiu tożsamości platformy SAP Cloud na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla uwierzytelniania tożsamości platformy SAP Cloud Platform, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego uwierzytelniania tożsamości platformy SAP Cloud Platform Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla uwierzytelniania tożsamości platformy SAP Cloud w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SAP Cloud Platform Identity Authentication**.

    ![Link do usługi SAP Cloud Platform Identity Authentication na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://<tenantID>.accounts.ondemand.com/service/scim ` **adresie URL dzierżawy**. Wprowadź wartości **identyfikatora użytkownika** i **hasła** pobrane wcześniej odpowiednio w polu **Nazwa użytkownika** administratora i **hasło administratora.** Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z uwierzytelnianiem tożsamości platformy SAP Cloud. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto uwierzytelniania tożsamości platformy SAP Cloud Ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z uwierzytelnianiem tożsamości platformy SAP Cloud Platform**.

    ![Mapowania użytkowników uwierzytelniania tożsamości platformy sap cloud](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do uwierzytelniania tożsamości platformy SAP Cloud w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w SAP Cloud Platform Identity Authentication dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika uwierzytelniania tożsamości platformy SAP Cloud](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla uwierzytelniania tożsamości platformy SAP cloud, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować do uwierzytelniania tożsamości platformy SAP Cloud Platform, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w ramach uwierzytelniania tożsamości platformy sap cloud.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Punkt końcowy uwierzytelniania tożsamości platformy SAP Cloud Platform wymaga, aby określone atrybuty były w określonym formacie. Możesz dowiedzieć się więcej o tych atrybutach i ich określonym formacie [tutaj](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

