---
title: 'Samouczek: Konfigurowanie iProva do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekwowania kont użytkowników do iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057508"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie iProva do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usługach iProva i azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aproprowowania i deekwowania użytkowników i/lub grup do iProva.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawca iProva](https://www.iProva.com/)
* Konto użytkownika w iProva z uprawnieniami administratora.

## <a name="assigning-users-to-iprova"></a>Przypisywanie użytkowników do iProva

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do iProva. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do iProva, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Ważne wskazówki dotyczące przypisywania użytkowników do iProva

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do iProva do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do iProva należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-iprova-for-provisioning"></a>Konfigurowanie iProva do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej iProva](https://www.iProva.com/). Przejdź **do > zarządzania aplikacjami**.

    ![Konsola administracyjna iProva](media/iprova-provisioning-tutorial/admin.png)

2.  Kliknij **zarządzanie użytkownikami zewnętrznymi**.

    ![iProva Dodaj scim](media/iprova-provisioning-tutorial/external.png)

3. Aby dodać nowego dostawcę, kliknij ikonę **plusa.** W nowym oknie dialogowym **Dodawanie dostawcy** podaj **pozycję Title**. Można dodać **ograniczenie dostępu oparte na protokoi.** Kliknij przycisk **OK.**

    ![iProva dodać nowe](media/iprova-provisioning-tutorial/add.png)

    ![dostawca dodawania iProva](media/iprova-provisioning-tutorial/addprovider.png)

4.  Kliknij przycisk **Token stały.** Skopiuj **token stały** i zapisz go, ponieważ będzie to jedyny czas, w jakim będzie można go wyświetlić. Ta wartość zostanie wprowadzona w polu Token tajny na karcie Inicjowanie obsługi administracyjnej aplikacji iProva w witrynie Azure portal.

    ![IProva Utwórz token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Dodawanie aplikacji iProva z galerii

Przed skonfigurowaniem iProva do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać iProva z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać iProva z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **iProva**, wybierz **iProva** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja iProva na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla systemu iProva 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w iProva na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla iProva, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego iProva.](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla iProva w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Z listy aplikacji wybierz pozycję **iProva**.

    ![Link do aplikacji iProva na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://identitymanagement.services.iProva.nl/scim` **adresie URL dzierżawy**. Wprowadź wartość **tokenu stałego** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z iProva. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto iProva ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z iProvą**.

    ![Mapowania użytkowników iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do iProva w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w iProva dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z iProvą**.

    ![Mapowania grup iProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do iProva w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w iProva dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy iProva](media/iprova-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla iProva, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić iProva, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w usłudze iProva.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

