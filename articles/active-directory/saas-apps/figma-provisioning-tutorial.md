---
title: 'Samouczek: Konfigurowanie automatycznego inicjowania obsługi administracyjnej figma za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do figmy.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057961"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Figma do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w figma i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania administracyjnych użytkowników i/lub grup do figma.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca Figma](https://www.figma.com/pricing/).
* Konto użytkownika w Figma z uprawnieniami administratora.

## <a name="assign-users-to-figma"></a>Przypisz użytkowników do Figma.
Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do figmy. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Figma, postępując zgodnie z instrukcjami tutaj:
 
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Figmy

 * Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Figma do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Figma, należy wybrać dowolną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-figma-for-provisioning"></a>Konfigurowanie figmy do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Figma do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy pobrać niektóre informacje o inicjowaniu obsługi administracyjnej z Figma.

1. Zaloguj się do [konsoli administracyjnej Figma](https://www.Figma.com/). Kliknij ikonę koła zębatego obok najemcy.

    ![FigmaFigma-pracownik-provision](media/Figma-provisioning-tutorial/image0.png)

2. Przejdź do **ustawień dziennika aktualizacji > ogólne**.

    ![FigmaFigma-pracownik-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Skopiuj **identyfikator dzierżawy**. Ta wartość będzie używana do konstruowania adresu URL punktu końcowego SCIM, który ma zostać wprowadzony w polu **adres URL dzierżawy** na karcie Inicjowanie obsługi administracyjnej aplikacji Figma w witrynie Azure portal.

    ![Figma Tworzenie tokenu](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Przewiń w dół i kliknij **pozycję Generuj token INTERFEJSU API**.

    ![Figma Tworzenie tokenu](media/Figma-provisioning-tutorial/token.png)

5. Skopiuj wartość **tokenu interfejsu API.** Ta wartość zostanie wprowadzona w polu **Token tajny** w karcie Inicjowanie obsługi administracyjnej aplikacji Figma w witrynie Azure portal. 

    ![Figma Tworzenie tokenu](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Dodaj Figmę z galerii

Aby skonfigurować Figma do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Figma z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **figmę**, wybierz **Figma** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Figma na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla figmy 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w figma na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć samookrotne logowanie oparte na SAML dla Figmy, postępując zgodnie z instrukcjami podanymi w [samouczku Figma Single sign-on](figma-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla programu Figma w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Figma**.

    ![Łącze Figma na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres `https://www.figma.com/scim/v2/<TenantID>` **URL dzierżawy,** gdzie **TenantID** jest wartością pobraną z Figma wcześniej. Wprowadź wartość **tokenu interfejsu API** w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z Figma. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Figma ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Figma**.

    ![Mapowania użytkowników Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Figma w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Figma dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla figmy, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Figma, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć szczegóły **synchronizacji** sekcji do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD na Figma.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)