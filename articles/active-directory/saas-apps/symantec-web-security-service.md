---
title: 'Samouczek: Konfigurowanie usługi WSS (Web Security Service) firmy Symantec do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania aprowizowania kont użytkowników do usługi Symantec Web Security Service (WSS).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063122"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi WSS (Web Security Service) firmy Symantec do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze WSS (Web Security Service) i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwzwalowania użytkowników i/lub grup do usługi Symantec Web Security Service (WSS).

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa usługi WSS (WSS) firmy Symantec](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Konto użytkownika w usłudze Symantec Web Security Service (WSS) z uprawnieniami administratora.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Przypisywanie użytkowników do usługi Symantec Web Security Service (WSS)

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownikom należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi Symantec Web Security Service (WSS). Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do usługi Symantec Web Security Service (WSS), postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Symantec Web Security Service (WSS)

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi Symantec Web Security Service (WSS) w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do usługi Symantec Web Security Service (WSS) należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Konfigurowanie usługi Symantec Web Security Service (WSS) do inicjowania obsługi administracyjnej

Przed skonfigurowaniem usługi Symantec Web Security Service (WSS) do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej systemu SCIM w usłudze WSS (Web Security Service).

1. Zaloguj się do [konsoli administracyjnej usługi Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Przejdź do usługi **Solutions** > **.**

    ![Usługa zabezpieczeń sieci Web firmy Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Przejdź do**integracji** >  **konserwacji** > kont**Nowa integracja**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Wybierz **opcję Synchronizuj & grup użytkowników**zewnętrznych . 

    ![Usługa zabezpieczeń sieci Web firmy Symantec](media/symantec-web-security-service/third-party-users.png)

4.  Skopiuj adres URL i **token** **SCIM** . Te wartości zostaną wprowadzone w polu **Adres URL dzierżawy** i **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji usługi WSS (WSS) firmy Symantec w witrynie Azure portal.

    ![Usługa zabezpieczeń sieci Web firmy Symantec](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Dodawanie usługi Symantec Web Security Service (WSS) z galerii

Aby skonfigurować usługę Symantec Web Security Service (WSS) do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać usługę Symantec Web Security Service (WSS) z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Symantec Web Security Service (WSS) z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Symantec Web Security Service**, wybierz pozycję **Symantec Web Security Service** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Usługa Symantec Web Security Service (WSS) na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej usługi Symantec Web Security Service (WSS)

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Symantec Web Security Service (WSS) na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na saml dla usługi Symantec Web Security Service (WSS), postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego usługi Symantec Web Security Service (WSS).](symantec-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Symantec Web Security Service (WSS) w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Symantec Web Security Service**.

    ![Link usługi Symantec Web Security Service (WSS) na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora wprowadź **adres** **URL scim** i token wartości pobrane wcześniej w **adresie URL dzierżawy** i **token tajny** odpowiednio. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Symantec Web Security Service. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Symantec Web Security Service (WSS) ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą Symantec Web Security Service (WSS).**

    ![Mapowania użytkowników usługi Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Symantec Web Security Service (WSS) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w usłudze Symantec Web Security Service (WSS) do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania użytkowników usługi Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą Symantec Web Security Service**.

    ![Mapowania użytkowników usługi Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi Symantec Web Security Service (WSS) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w usłudze Symantec Web Security Service (WSS) do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Mapowania użytkowników usługi Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi zabezpieczeń sieci Web firmy Symantec, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które mają zostać udostępnione symantec Web Security Service (WSS), wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji na temat czasu działania użytkowników i/lub grup w celu udostępnienia, zobacz [Jak długo potrwa aprowizję użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Za pomocą sekcji **Bieżący stan** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w usłudze Symantec Web Security Service (WSS). Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
