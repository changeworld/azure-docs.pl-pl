---
title: 'Samouczek: Konfigurowanie G Suite do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i usuwać aprowizyję kont użytkowników z usługi Azure AD do G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057723"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie G Suite do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w G Suite i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do G Suite.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Łącznik G Suite został niedawno zaktualizowany w październiku 2019. Zmiany wprowadzone w łączniku G Suite obejmują:
> - Dodano obsługę dodatkowych atrybutów użytkowników i grup G Suite. 
> - Zaktualizowano nazwy atrybutów docelowych G Suite, aby dopasować się do tego, co jest zdefiniowane [tutaj](https://developers.google.com/admin-sdk/directory).
> - Zaktualizowano domyślne mapowania atrybutów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą G Suite, potrzebne są następujące elementy:

- Dzierżawa usługi Azure AD
- [Najemca G Suite](https://gsuite.google.com/pricing.html)
- Konto użytkownika w G Suite z uprawnieniami administratora.

## <a name="assign-users-to-g-suite"></a>Przypisywanie użytkowników do G Suite

Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do G Suite. Po podjęciu decyzji możesz przypisać tych użytkowników i/lub grupy do G Suite, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Ważne wskazówki dotyczące przypisywania użytkowników do G Suite

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do G Suite, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do G Suite w oknie dialogowym przypisania należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna). Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-g-suite-for-provisioning"></a>Konfiguracja G Suite do inicjowania obsługi administracyjnej

Przed skonfigurowaniem G Suite do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w G Suite.

1. Zaloguj się do [konsoli administratora G Suite](https://admin.google.com/) za pomocą konta administratora, a następnie wybierz pozycję **Zabezpieczenia**. Jeśli nie widzisz łącza, może on być ukryty w menu **Więcej kontrolek** u dołu ekranu.

    ![Wybierz zabezpieczenia.][10]

1. Na stronie **Zabezpieczenia** wybierz pozycję **Odwołanie do interfejsu API**.

    ![Wybierz odwołanie do interfejsu API.][15]

1. Wybierz **pozycję Włącz dostęp do interfejsu API**.

    ![Wybierz odwołanie do interfejsu API.][16]

   > [!IMPORTANT]
   > Dla każdego użytkownika, który zamierza udostępnić G Suite, ich nazwa użytkownika w usłudze Azure AD **musi** być powiązana z domeną niestandardową. Na przykład nazwy użytkowników, bob@contoso.onmicrosoft.com które wyglądają, nie są akceptowane przez G Suite. Z drugiej strony, bob@contoso.com jest akceptowany. Domenę istniejącego użytkownika można zmienić, postępując zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Po dodaniu i zweryfikowaniu żądanych domen niestandardowych za pomocą usługi Azure AD musisz zweryfikować je ponownie za pomocą G Suite. Aby zweryfikować domeny w G Suite, zapoznaj się z następującymi krokami:

    a. W [Konsoli administracyjnej G Suite](https://admin.google.com/)wybierz pozycję **Domeny**.

    ![Wybierz domeny][20]

    b. Wybierz **pozycję Dodaj domenę lub alias domeny**.

    ![Dodawanie nowej domeny][21]

    d. Wybierz **pozycję Dodaj inną domenę**, a następnie wpisz nazwę domeny, którą chcesz dodać.

    ![Wpisz nazwę domeny][22]

    d. Wybierz **pozycję Kontynuuj i zweryfikuj własność domeny**. Następnie wykonaj kroki, aby sprawdzić, czy jesteś właścicielem nazwy domeny. Aby uzyskać wyczerpujące instrukcje dotyczące weryfikacji domeny za pomocą Google, zobacz [Sprawdzanie, czy właściciel witryny jest w stanie zweryfikować.](https://support.google.com/webmasters/answer/35179)

    e. Powtórz powyższe kroki dla wszystkich dodatkowych domen, które zamierzasz dodać do G Suite.

1. Następnie określ, którego konta administratora chcesz użyć do zarządzania inicjowania obsługi administracyjnej użytkowników w G Suite. Przejdź do **roli administratora**.

    ![Wybierz Google Apps][26]

1. Dla **roli administratora** tego konta edytuj **uprawnienia** dla tej roli. Upewnij się, aby włączyć wszystkie **uprawnienia interfejsu API administratora,** dzięki czemu to konto może służyć do inicjowania obsługi administracyjnej.

    ![Wybierz Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Dodaj G Suite z galerii

Aby skonfigurować G Suite do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać G Suite z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS. 

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

1. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **G Suite**, wybierz **G Suite** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Usługa G Suite na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w G Suite 

W tej sekcji znajdziesz przewodnik po krokach konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w G Suite na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla G Suite, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego G Suite.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym interfejsu API katalogu G Suite, zobacz [Interfejs API katalogu](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla G Suite w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **G Suite**.

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

1. W sekcji **Poświadczenia administratora** wybierz pozycję **Autoryzuj**. Otwiera okno dialogowe autoryzacji Google w nowym oknie przeglądarki.

    ![G Suite Autoryzuj](media/google-apps-provisioning-tutorial/authorize.png)

1. Potwierdź, że chcesz przyznać usłudze Azure AD uprawnienia do wprowadzania zmian w dzierżawie G Suite. Wybierz pozycję **Zaakceptuj**.

    ![Potwierdź uprawnienia.][28]

1. W witrynie Azure portal wybierz pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto G Suite ma uprawnienia administratora zespołu. Następnie spróbuj ponownie wykonać krok **Autoryzuj.**

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

1. Kliknij przycisk **Zapisz**.

1. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z G Suite**.

    ![Mapowania użytkowników G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do G Suite w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w G Suite do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z pakietem G Suite**.

    ![Mapowania grup G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do G Suite w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w G Suite do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany. Interfejs użytkownika wyświetla domyślny zestaw mapowań atrybutów między usługą Azure AD i G Suite. Możesz dodać dodatkowe atrybuty, takie jak Jednostka organizacyjna, klikając dodaj nowe mapowanie.

    ![Atrybuty grupy G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla G Suite, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić G Suite, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

1. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w pakiecie G Suite.

> [!NOTE]
> Jeśli użytkownicy mają już istniejące konto osobiste/konsumenckie przy użyciu adresu e-mail użytkownika usługi Azure AD, może to spowodować problem, który można rozwiązać za pomocą narzędzia Google Transfer Tool przed wykonaniem synchronizacji katalogu.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Typowe problemy
* Błędy autoryzacji mogą wystąpić, gdy konto używane do nawiązania połączenia nie jest dla administratora w GSuite. Upewnij się, że konto używane do autoryzowania dostępu ma uprawnienia administratora we **wszystkich domenach,** które użytkownicy muszą być aprowizowani. 
* Usługa Azure AD obsługuje wyłączanie użytkowników w GSuite, dzięki czemu nie mogą uzyskać dostępu do aplikacji, ale nie usuwa użytkowników w GSuite.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
