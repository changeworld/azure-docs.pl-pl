---
title: 'Samouczek: Konfigurowanie Storegate do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i deekwowania kont użytkowników storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064261"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Storegate do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w Storegate i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup storegate.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Storegate](https://www.storegate.com)
* Konto użytkownika w Storegate z uprawnieniami administratora.

## <a name="assign-users-to-storegate"></a>Przypisywanie użytkowników do Storegate

Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Storegate. Po podjęciu decyzji, można przypisać tych użytkowników i /lub grup do Storegate, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Storegate

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Storegate do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Storegate należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-storegate-for-provisioning"></a>Konfigurowanie Storegate do inicjowania obsługi administracyjnej

Przed skonfigurowaniem Storegate do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy pobrać niektóre informacje dotyczące inicjowania obsługi administracyjnej z Storegate.

1. Zaloguj się do [konsoli administracyjnej Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) i przejdź do ustawień, klikając ikonę użytkownika w prawym górnym rogu i wybierz pozycję **Ustawienia konta**.

    ![Storegate Dodaj SCIM](media/storegate-provisioning-tutorial/admin.png)

2. W ustawieniach przejdź do **ustawień > zespołu** i sprawdź, czy przełącznik jest włączony w sekcji **Logowanie jednokrotne.**

    ![Ustawienia Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Przycisk przełączania Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Skopiuj **adres URL dzierżawy** i **token**. Te wartości zostaną wprowadzone w adresie **URL dzierżawy** i **tajne token** pola odpowiednio w aprowizacji karty aplikacji Storegate w witrynie Azure portal. 

    ![Token tworzenia storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Dodawanie Storegate z galerii

Aby skonfigurować Storegate do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać Storegate z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Storegate**, wybierz **Storegate** w panelu wyników. 

    ![Storegate na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Zarejestruj się w Storegate,** który przekieruje Cię na stronę logowania Storegate. 

    ![Storegate OIDC Dodaj](media/storegate-provisioning-tutorial/signup.png)

6.  Zaloguj się do [konsoli administracyjnej Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) i przejdź do ustawień, klikając ikonę użytkownika w prawym górnym rogu i wybierz pozycję **Ustawienia konta**.

    ![Logowanie Storegate](media/storegate-provisioning-tutorial/admin.png)

7. W ustawieniach przejdź do **ustawień > zespołu** i kliknij przełącznik przełącznika w sekcji Logowanie jednokrotne spowoduje uruchomienie przepływu zgody. Kliknij **przycisk Aktywuj**.

    ![Zespół Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Aktywacja Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Ponieważ Storegate jest aplikacją OpenIDConnect, należy zalogować się do Storegate przy użyciu konta służbowego Microsoft.

    ![Logowanie Storegate OIDC](media/storegate-provisioning-tutorial/login.png)

9. Po pomyślnym uwierzytelnieniu zaakceptuj monit o zgodę na stronę zgody. Aplikacja zostanie automatycznie dodana do dzierżawy i zostaniesz przekierowany do konta Storegate.

    ![Storegate OIDc Zgoda](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej storegate 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Storegate na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym SCIM storegate, zapoznaj się [z tą .](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla storegate w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **storegate**.

    ![Łącze Storegate na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://dialpad.com/scim` **adresie URL dzierżawy**. Wprowadź wartość pobraną i zapisaną wcześniej z Storegate w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z storegate. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Storegate ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do Storegate**.

    ![Mapowania użytkowników Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Storegate w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Storegate dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla Storegate, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Storegate, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w storegate.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
