---
title: 'Samouczek: Konfigurowanie arkusza Smartsheet do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników w arkuszu Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063207"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie arkusza Smartsheet do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w arkuszu Smartsheet i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do arkusza Smartsheet.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa arkusza Smartsheet](https://www.smartsheet.com/pricing)
* Konto użytkownika w planie Smartsheet Enterprise lub Enterprise Premier z uprawnieniami Administrator systemu.

## <a name="assign-users-to-smartsheet"></a>Przypisywanie użytkowników do arkusza Smartsheet

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do arkusza Smartsheet. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Smartsheet, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Ważne wskazówki dotyczące przypisywania użytkowników do arkusza Smartsheet

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do arkusza Smartsheet w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do arkusza Smartsheet należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

* Aby zapewnić parzystość w przypisaniach ról użytkowników między arkuszem Smartsheet a usługą Azure AD, zaleca się korzystanie z tych samych przypisań ról wypełnionych pełną listą użytkowników arkusza smartsheet. Aby pobrać tę listę użytkowników z arkusza Smartsheet, przejdź do **pozycji Administrator konta > Zarządzanie użytkownikiem > więcej akcji > pobierz listę użytkowników (csv).**

* Aby uzyskać dostęp do niektórych funkcji w aplikacji, smartsheet wymaga od użytkownika wielu ról. Aby dowiedzieć się więcej o typach użytkowników i uprawnieniach w arkuszu Smartsheet, przejdź do [sekcji Typy użytkowników i uprawnienia](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Jeśli użytkownik ma wiele ról przypisanych w arkuszu Smartsheet, **należy upewnić** się, że te przypisania ról są replikowane w usłudze Azure AD, aby uniknąć scenariusza, w którym użytkownicy mogą utracić dostęp do obiektów smartsheet na stałe. Każda unikatowa rola w arkuszu smartsheet **MUSI** być przypisana do innej grupy w usłudze Azure AD. Użytkownik **MUSI** następnie zostać dodany do każdej z grup odpowiadających żądanym rolom. 

## <a name="set-up-smartsheet-for-provisioning"></a>Konfigurowanie arkusza smartsheet do inicjowania obsługi administracyjnej

Przed skonfigurowaniem arkusza Smartsheet do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w arkuszu Smartsheet.

1. Zaloguj się jako **SysAdmin** w **[portalu Smartsheet](https://app.smartsheet.com/b/home)** i przejdź do **administratora konta**.

    ![Administrator konta arkusza inteligentnego](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Przejdź do funkcji **Kontrola zabezpieczeń > automatyczna inicjowanie obsługi administracyjnej użytkownika > edycji**.

    ![Kontrola zabezpieczeń arkusza inteligentnego](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Dodawanie i sprawdzanie poprawności domen poczty e-mail dla użytkowników, których zamierzasz aprowizować z usługi Azure AD do arkusza Smartsheet. Wybierz **opcję Nie włączone,** aby upewnić się, że wszystkie akcje inicjowania obsługi administracyjnej pochodzą tylko z usługi Azure AD, a także upewnij się, że lista użytkowników arkusza smartsheet jest zsynchronizowana z przypisaniami usługi Azure AD.

    ![Inicjowanie obsługi administracyjnej użytkowników arkusza inteligentnego](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po zakończeniu sprawdzania poprawności należy aktywować domenę. 

    ![Aktywowanie domeny w arkuszu Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Wygeneruj **token tajny** wymagany do skonfigurowania automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD przez przejście do **aplikacji i integracji.**

    ![Instalacja arkusza Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wybierz **pozycję Dostęp do interfejsu API**. Kliknij **pozycję Generuj nowy token dostępu**.

    ![Instalacja arkusza Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zdefiniuj nazwę tokenu dostępu interfejsu API. Kliknij przycisk **OK**.

    ![Instalacja arkusza Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Skopiuj token dostępu interfejsu API i zapisz go, ponieważ będzie to jedyny czas, w jakim będzie można go wyświetlić. Jest to wymagane w polu **Token tajny** w usłudze Azure AD.

    ![Token arkusza inteligentnego](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Dodawanie arkusza Smartsheet z galerii

Aby skonfigurować arkusz Smartsheet do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać arkusz smartsheet z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Smartsheet**, wybierz pozycję **Smartsheet** w panelu wyników. 

    ![Arkusz smartsheet na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Zarejestruj się w arkuszu Smartsheet,** który przekieruje Cię na stronę logowania smartsheet. 

    ![Dodanie arkusza smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Ponieważ Smartsheet jest aplikacją OpenIDConnect, należy zalogować się do arkusza Smartsheet przy użyciu konta służbowego Microsoft.

    ![Logowanie do OIDC arkusza SmartSheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po pomyślnym uwierzytelnieniu zaakceptuj monit o zgodę na stronę zgody. Aplikacja zostanie automatycznie dodana do dzierżawy i zostaniesz przekierowany do konta Smartsheet.

    ![Zgoda Smartsheet OIDc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w arkuszu Smartsheet 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w arkuszu Smartsheet na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla arkusza Smartsheet w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Smartsheet**.

    ![Łącze Smartsheet na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://scim.smartsheet.com/v2/` **adresie URL dzierżawy**. Wprowadź wartość pobraną i zapisaną wcześniej z arkusza Smartsheet w **pliku Secret Token**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z arkuszem Smartsheet. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Smartsheet ma uprawnienia SysAdmin i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z arkuszem smartsheet**.

    ![Mapowania użytkowników arkuszy inteligentnych](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do arkusza smartsheet w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w arkuszu Smartsheet do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika arkusza smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla arkusza Smartsheet, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić arkuszowi Smartsheet, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w arkuszu Smartsheet.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Arkusz smartsheet nie obsługuje usuwania nietrwałego. Gdy **aktywny** atrybut użytkownika jest ustawiony na Fałsz, arkusz smartsheet trwale usuwa użytkownika.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
