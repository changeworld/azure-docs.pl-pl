---
title: 'Samouczek: Skonfiguruj odwiedzanie w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników.
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
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 381f9cb2cb9ef196149144d40332a1de3b90f188
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802760"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Samouczek: Skonfiguruj odwiedzanie w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w odwiedzaniu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do odwiedzania.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa w trakcie odwiedzin](https://www.visitly.io/pricing/)
* Konto użytkownika w programie z uprawnieniami administratora.

## <a name="assigning-users-to-visitly"></a>Przypisywanie użytkowników do odwiedzania 

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD muszą mieć dostęp do odwiedzania. Po ustaleniu tych użytkowników i/lub grup można przypisywać je do odwiedzania, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Ważne porady dotyczące przypisywania użytkowników do odwiedzania 

* Zaleca się, aby jeden użytkownik usługi Azure AD był przypisywany do odwiedzania w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do odwiedzania należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-visitly-for-provisioning"></a>Instalator odwiedzający się w celu aprowizacji

Przed rozpoczęciem konfigurowania programu w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę Standard scim.

1. Zaloguj się do [odwiedzenia](https://app.visitly.io/login). Kliknij pozycję **integracja** > **hosta synchronizacja**.

    ![Visitly](media/Visitly-provisioning-tutorial/login.png)

2. Przewiń w dół wybierz **sekcję usługi Azure AD**

    ![Visitly](media/Visitly-provisioning-tutorial/integration.png)

3. Skopiuj **klucz interfejsu API**. Te wartości zostaną wprowadzone w polu **token tajny** na karcie aprowizacji aplikacji do odwiedzania w Azure Portal.

    ![Visitly](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Dodaj wizyty z galerii

Aby skonfigurować odwiedzanie w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD, musisz dodać witrynę z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać witrynę z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **odwiedzany**tekst, wybierz pozycję **odwiedź** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Odwiedź listę wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-visitly"></a>Konfigurowanie automatycznego aprowizacji użytkowników w celu odwiedzania  

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w odwiedzaniu opartym na przypisaniach użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML, postępując zgodnie z instrukcjami podanymi w [samouczku Logowanie jednokrotne](Visitly-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem

### <a name="to-configure-automatic-user-provisioning-for-visitly--in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Visitly**.

    ![Link odwiedzany na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W ` https://api.visitly.io/v1/usersync/SCIM` sekcji poświadczenia administratora wprowadź odpowiednio wartości i **klucze interfejsu API** pobrane wcześniej w **adresie URL dzierżawy** i w **tokenie tajnym** . Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z witryną. Jeśli połączenie nie powiedzie się, upewnij się, że konto odwiedzające ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij polecenie **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby odwiedzać**.

    ![Zapoznaj się z mapowaniami użytkowników](media/visitly-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby odwiedzać się w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w odniesieniu do operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Odwiedzaj atrybuty użytkownika](media/visitly-provisioning-tutorial/userattribute.png)



10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning w odwiedzaniu, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do odwiedzania, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo będzie trwać dla użytkowników i/lub grup, zobacz [jak długo trwa inicjowanie obsługi użytkowników](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie sieci Web. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

Usługa nie obsługuje żadnych stałych usunięć, ale wszystko tylko w przypadku usuwania nietrwałego.  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
