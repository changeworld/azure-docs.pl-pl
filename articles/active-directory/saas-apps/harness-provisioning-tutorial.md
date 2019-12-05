---
title: 'Samouczek: Konfigurowanie programu dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807760"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach programu obsługi i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do udostępnienia.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa programu](https://harness.io/pricing/)
* Konto użytkownika w programie z uprawnieniami administratora.

## <a name="assigning-users-to-harness"></a>Przypisywanie użytkowników do programu

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do programu. Po ustaleniu tych użytkowników i/lub grup możesz przypisać je do programu, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Ważne porady dotyczące przypisywania użytkowników do programu

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do programu do obsługi przetestowania automatycznej konfiguracji aprowizacji użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do programu, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-harness-for-provisioning"></a>Konfigurowanie programu dla aprowizacji

1. Zaloguj się do [konsoli administracyjnej](https://app.harness.io/#/login)programu. Przejdź do programu **ciągłe zabezpieczenia > zarządzanie dostępem**.

    ![Konsola administracyjna programu](media/harness-provisioning-tutorial/admin.png)

2.  Kliknij pozycję **klucze interfejsu API**.

    ![Dodaj Standard scim](media/harness-provisioning-tutorial/apikeys.png)

3. Kliknij pozycję **Dodaj nowy klucz**. W oknie dialogowym **Dodaj klucz interfejsu API** Podaj **nazwę** i wybierz opcję z **uprawnień dziedziczonych z** menu rozwijanego. Kliknij przycisk **Prześlij** .

    ![Dodaj nowy klucz do zespołu](media/harness-provisioning-tutorial/addkey.png)

    ![Okno dialogowe Dodawanie nowego klucza](media/harness-provisioning-tutorial/title.png)

3.  Skopiuj **klucz**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji programu dla zespołu w Azure Portal.

    ![Utwórz token dla zespołu](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Dodaj zespół z galerii

Przed skonfigurowaniem programu do obsługi automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać zespół z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program do programu z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **zespół**, wybierz pozycję **zespół** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Korzystanie z listy wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Konfigurowanie automatycznego aprowizacji użytkowników do programu 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie dla programu na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla programu obsługi, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)jednokrotne w programie. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym "Standard scim", zapoznaj się z [tym](https://docs.harness.io/article/smloyragsm-api-keys)

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Aby skonfigurować funkcję automatycznej aprowizacji użytkowników dla programu obsługi w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zespół**.

    ![Link programu dla programu z listy aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` w **adresie URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi dla zespołu ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail powiadamiania](common/provisioning-notification-email.png)

7. Kliknij przycisk **Save** (Zapisz).

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do programu**.

    ![Mapowanie użytkowników z korzystaniem z zespołu](media/harness-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika synchronizowane z usługą Azure AD, aby uzyskać dostęp do programu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w programie dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Wykorzystaj atrybuty użytkownika](media/harness-provisioning-tutorial/userattributes.png)

10. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory grupy, aby uzyskać**dostęp.

    ![Mapowania grup zespołu](media/harness-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, aby uzyskać możliwość wypróbowania w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w programie dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy dla zespołu](media/harness-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w ramach programu.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
