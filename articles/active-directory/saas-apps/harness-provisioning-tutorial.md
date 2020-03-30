---
title: 'Samouczek: Konfigurowanie wiązki przewodów ekscesowych do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników w celu wykorzystania.
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
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057842"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie wiązki wiązki przewodów ekscesowych do automatycznego inicjowania obsługi administracyjnej przez

W tym artykule dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników lub grup do wykorzystania.

> [!NOTE]
> W tym artykule opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat tej usługi i odpowiedzi na często zadawane pytania, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym artykule zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawca uprzęży](https://harness.io/pricing/)
* Konto użytkownika w uprzęży z uprawnieniami *administratora*

## <a name="assign-users-to-harness"></a>Przypisywanie użytkowników do uprzęży

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownika, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do uprzęży. Następnie można przypisać tych użytkowników lub grupy do wykorzystania, postępując zgodnie z instrukcjami w [Przypisz użytkownika lub grupę do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Ważne wskazówki dotyczące przypisywania użytkowników do uprzęży

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do uprzęży, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowych użytkowników lub grup można przypisać później.

* Po przypisaniu użytkownika do uprzęży należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym **Przypisywanie.** Użytkownicy z rolą *dostępu domyślnego* są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-harness-for-provisioning"></a>Konfigurowanie uprzęży do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej Uprzęży,](https://app.harness.io/#/login)a następnie przejdź do**zarządzania ciągłym dostępem do** **zabezpieczeń** > .

    ![Konsola administracyjna uprzęży](media/harness-provisioning-tutorial/admin.png)

1. Wybierz **klawisze INTERFEJSU API**.

    ![Łącze Klucze interfejsu API uprzęży](media/harness-provisioning-tutorial/apikeys.png)

1. Wybierz **pozycję Dodaj klucz interfejsu API**. 

    ![Łącze Dodaj klucza interfejsu API](media/harness-provisioning-tutorial/addkey.png)

1. W okienku **Dodawanie klucza interfejsu API** wykonaj następujące czynności:

    ![Okienko Dodaj klucza do wiązki wiązki wiązki wiązki przewod](media/harness-provisioning-tutorial/title.png)
   
   a. W polu **Nazwa** podaj nazwę klucza.  
   b. Z listy rozwijanej **Uprawnienia dziedziczone z** wybierz opcję. 
   
1. Wybierz pozycję **Prześlij**.

1. Skopiuj **klucz** do późniejszego użycia w tym samouczku.

    ![Utwórz wiązki utworzonej tokenu](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Dodaj uprząż z galerii

Przed skonfigurowaniem uprzężenia do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać uprząż z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk "Usługa Azure Active Directory"](common/select-azuread.png)

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Łącze "Wszystkie aplikacje"](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk "Nowa aplikacja"](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **pozycję Uprząż**, wybierz **pozycję Uprząż** na liście wyników, a następnie wybierz przycisk **Dodaj,** aby dodać aplikację.

    ![Uprząż na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w celu wykorzystania 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników lub grup w uprzęży na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samooceny jednokrotne samooki dla uprzęży oparte na SAML, postępując zgodnie z instrukcjami w [samouczku logowania jednokrotnego Uprzęży.](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) Można skonfigurować logowanie jednokrotne niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkt końcowy SCIM uprzęży, zobacz Wiązki [kluczy interfejsu API](https://docs.harness.io/article/smloyragsm-api-keys) wiązki sieci.

Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla uprzęży w usłudze Azure AD, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com)wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz opcję **Uprząż**.

    ![Łącze Uprzęży na liście aplikacji](common/all-applications.png)

1. Wybierz **pozycję Inicjowanie obsługi administracyjnej**.

    ![Przycisk inicjowania obsługi administracyjnej](common/provisioning.png)

1. Z listy rozwijanej **Tryb inicjowania obsługi administracyjnej** wybierz pozycję **Automatyczne**.

    ![Lista rozwijana "Tryb inicjowania obsługi administracyjnej"](common/provisioning-automatic.png)

1. W obszarze **Poświadczenia administratora**wykonaj następujące czynności:

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. W polu Adres URL **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** **dzierżawy** wprowadź .  
   b. W polu **Token tajny** wprowadź wartość tokenu uwierzytelniania SCIM, która została zapisana w kroku 6 sekcji "Konfigurowanie uprzęży do inicjowania obsługi administracyjnej".  
   d. Wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z uprzężą. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Harness ma uprawnienia *administratora,* a następnie spróbuj ponownie.

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu.**

    ![Pole "Wiadomość e-mail z powiadomieniem"](common/provisioning-notification-email.png)

1. Wybierz **pozycję Zapisz**.

1. W obszarze **Mapowania**wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do wykorzystania**.

    ![Łącze "Synchronizuj użytkowników usługi Azure Active Directory w celu wykorzystania"](media/harness-provisioning-tutorial/usermappings.png)

1. W obszarze **Mapowania atrybutów**przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do uprzęży. Atrybuty wybrane jako *Pasujące* są używane do dopasowania kont użytkowników w uprzęży do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Uprząż użytkownika "Mapowania atrybutów"](media/harness-provisioning-tutorial/userattributes.png)

1. W obszarze **Mapowania**wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory do wykorzystania**.

    ![Łącze "Synchronizuj grupy usługi Azure Active Directory do wykorzystania"](media/harness-provisioning-tutorial/groupmappings.png)

1. W obszarze **Mapowania atrybutów**przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do uprzęży. Atrybuty wybrane jako *Pasujące* właściwości są używane do dopasowania grup w uprzęży do operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Okienko "Mapowanie atrybutów" grupy uprzęży](media/harness-provisioning-tutorial/groupattributes.png)

1. Aby skonfigurować filtry zakresu, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. W obszarze **Ustawienia**, aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla wiązki przewodów, przełącz przełącznik **Stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Przełącznik stanu inicjowania obsługi administracyjnej przełączona na "Wł."](common/provisioning-toggle-on.png)

1. W obszarze **Ustawienia**na liście rozwijanej **Zakres** wybierz sposób synchronizowania użytkowników lub grup, które są inicjowane w celu zainicjowania obsługi administracyjnej.

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

1. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Przycisk zapisywania inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna synchronizację początkową użytkowników lub grup, które inicjujesz inicjującą obsługę administracyjną. Synchronizacja początkowa trwa dłużej niż później. Synchronizuje występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. Aby monitorować postęp, przejdź do sekcji **Szczegóły synchronizacji.** Można również wykonać łącza do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w uprzęży.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raport dotyczący automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
