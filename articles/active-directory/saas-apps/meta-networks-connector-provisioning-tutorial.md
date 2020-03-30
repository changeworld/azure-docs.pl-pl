---
title: 'Samouczek: Konfigurowanie łącznika sieci meta do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i deekprymakowania kont użytkowników do łącznika sieci meta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061364"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie łącznika sieci meta do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w łączniku sieci meta i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do łącznika sieci meta.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa łącznika sieci meta](https://www.metanetworks.com/)
* Konto użytkownika w łączniku Meta Networks z uprawnieniami administratora.

## <a name="assigning-users-to-meta-networks-connector"></a>Przypisywanie użytkowników do łącznika sieci meta

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do łącznika meta sieci. Po podjęciu decyzji, można przypisać tych użytkowników i /lub grup do Meta Networks Connector, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Ważne wskazówki dotyczące przypisywania użytkowników do łącznika meta sieci

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do łącznika sieci meta do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do łącznika sieci meta należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Instalatora łącznika sieci meta do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej łącznika meta sieci](https://login.metanetworks.com/login/) przy użyciu nazwy organizacji. Przejdź do **pozycji > administracji.**

    ![Konsola administracyjna łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kliknij znak plus w prawym górnym rogu ekranu, aby utworzyć nowy **klucz API**.

    ![Ikona meta networks connector plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Ustaw **nazwę klucza interfejsu API** i **opis klucza interfejsu API**.

    ![Łącznik meta sieci tworzenie tokenu](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Włącz uprawnienia **do zapisu** dla **grup** i **użytkowników**.

    ![Uprawnienia łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Kliknij **przycisk Dodaj**. Skopiuj **secret** i zapisz go, ponieważ będzie to jedyny czas, w jakim możesz go wyświetlić. Ta wartość zostanie wprowadzona w polu Tajny token na karcie Inicjowanie obsługi administracyjnej aplikacji łącznika meta sieci w witrynie Azure portal.

    ![Łącznik meta sieci tworzenie tokenu](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Dodaj IdP, przechodząc do **ustawień > administracji > IdP > Utwórz nowy**.

    ![Łącznik meta sieci Dodaj IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na stronie **Konfiguracja IdP** można **nazwać** konfigurację IdP i wybrać **ikonę**.

    ![Nazwa idP łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ikona idP łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  W obszarze **Konfigurowanie SCIM** wybierz nazwę klucza interfejsu API utworzoną w poprzednich krokach. Kliknij **zapisz**.

    ![Łącznik sieci meta konfiguruje scim](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Przejdź do **karty Ustawienia > administracyjne > IdP**. Kliknij nazwę konfiguracji IdP utworzonej w poprzednich krokach, aby wyświetlić **identyfikator IdP**. Ten **identyfikator** jest dodawany na końcu **adresu URL dzierżawy** podczas wprowadzania wartości w polu Adres URL **dzierżawy** na karcie Inicjowanie obsługi administracyjnej aplikacji łącznika meta sieci w witrynie Azure portal.

    ![Identyfikator IDP łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Dodawanie łącznika sieci meta z galerii

Przed skonfigurowaniem łącznika Meta Networks do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać łącznik sieci meta z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać łącznik sieci meta z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **punkt złącza Meta Networks Connector**, wybierz **opcję Łącznik sieci meta** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja Meta Networks Connector na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej do łącznika sieci meta 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w łączniku Sieci meta na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotnego logowania opartego na SAML dla łącznika meta sieci, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego łącznika meta networks connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla łącznika sieci meta w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Meta Networks Connector**.

    ![Link do aplikacji Meta Networks Connector na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź w `https://api.metanetworks.com/v1/scim/<IdP ID>` **adresie URL dzierżawy**. Wprowadź wartość **tokenu uwierzytelniania SCIM** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się ze łącznikiem sieci meta. Jeśli połączenie nie powiedzie się, upewnij się, że konto łącznika sieci meta ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z łącznikiem sieci met.**

    ![Mapowania użytkowników łączników sieci meta](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do łącznika sieci meta w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Meta Networks Connector do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika łącznika sieci meta](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z łącznikiem sieci meta.**

    ![Mapowania grup łączników sieci meta](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do łącznika sieci meta w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w łączniku sieci meta do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy łączników sieci meta](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla łącznika sieci meta, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które mają być aprowione dla łącznika sieci meta, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w łączniku meta sieci.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

