---
title: 'Samouczek: Konfigurowanie programu Infor CloudSuite do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekprymakowania kont użytkowników do programu Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057548"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie infor CloudSuite do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w infor cloudsuite i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do programu Infor CloudSuite.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Infor CloudSuite](https://www.infor.com/products/infor-os)
* Konto użytkownika w infor CloudSuite z uprawnieniami administratora.

## <a name="assigning-users-to-infor-cloudsuite"></a>Przypisywanie użytkowników do infor cloudsuite

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Infor CloudSuite. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Infor CloudSuite, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Infor CloudSuite

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Infor CloudSuite do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do programu Infor CloudSuite należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Konfigurowanie infor cloudsuite do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej Infor CloudSuite](https://www.infor.com/customer-center). Kliknij ikonę użytkownika, a następnie przejdź do **zarządzania użytkownikami**.

    ![Konsola administracyjna Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Kliknij ikonę menu w lewym górnym rogu ekranu. Kliknij **przycisk Zarządzaj**.

    ![Infor CloudSuite Dodaj SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Przejdź do **pozycji Konta SCIM**.

    ![Konto SCIM Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Dodaj użytkownika administratora, klikając ikonę plus. Podaj **hasło SCIM** i wpisz to samo hasło w obszarze **Potwierdź hasło**. Kliknij ikonę folderu, aby zapisać hasło. Zostanie wyświetlony **identyfikator użytkownika** wygenerowany dla użytkownika administratora.

    ![Użytkownik administratora Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Hasło Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identyfikator Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Aby wygenerować token nośnika, skopiuj **identyfikator użytkownika** i **hasło SCIM**. Wklej je w notatniku++ oddzielonym dwukropekem. Zakoduj wartość ciągu, przechodząc do **wtyczek > MIME Tools > Basic64 Encode**. 

    ![Identyfikator Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Skopiuj token okaziciela. Ta wartość zostanie wprowadzona w polu Tajny token na karcie Infor CloudSuite w witrynie Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Dodaj Infor CloudSuite z galerii

Przed skonfigurowaniem infor CloudSuite do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać infor CloudSuite z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program Infor CloudSuite z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **infor CloudSuite**, wybierz **Infor CloudSuite** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Infor CloudSuite na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w usłudze Infor CloudSuite 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Infor CloudSuite na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla infor CloudSuite, postępując zgodnie z instrukcjami podanymi w [samouczku infor CloudSuite z logiem jednokrotnym.](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym SCIM firmy Infor CloudSuite, zapoznaj się [z tym](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla infor CloudSuite w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Infor CloudSuite**.

    ![Łącze Infor CloudSuite na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **adresie URL dzierżawy**. Wprowadź wartość tokenu nośnika pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z programem Infor CloudSuite. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Infor CloudSuite ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Infor CloudSuite**.

    ![Mapowania użytkowników Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Infor CloudSuite w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Infor CloudSuite dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z programem Infor CloudSuite**.

    ![Mapowania grup Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Infor CloudSuite w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Infor CloudSuite dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla infor cloudsuite, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Infor CloudSuite, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w programie Infor CloudSuite.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)