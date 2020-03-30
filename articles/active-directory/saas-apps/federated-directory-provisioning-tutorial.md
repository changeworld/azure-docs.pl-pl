---
title: 'Samouczek: Konfigurowanie katalogu Federated do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i wyrównywalego udostępniania kont użytkowników do katalogu Federated Directory.
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
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057940"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie katalogu Federated do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w federatywnym katalogu i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego inicjowania obsługi administracyjnej i deprowizowania użytkowników i/lub grup do katalogu Federated Directory.

> [!NOTE]
>  W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Katalog federatywny](https://www.federated.directory/pricing).
* Konto użytkownika w katalogu Federated z uprawnieniami administratora.

## <a name="assign-users-to-federated-directory"></a>Przypisywanie użytkowników do katalogu federacyjnego
Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do katalogu Federated. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Federated Directory, postępując zgodnie z instrukcjami tutaj:

 * [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Ważne wskazówki dotyczące przypisywania użytkowników do katalogu federacyjnego
 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do katalogu Federated Directory w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do katalogu federacyjnego należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Konfigurowanie katalogu federacyjnego do inicjowania obsługi administracyjnej

Przed skonfigurowaniem federacyjnego katalogu do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim w katalogu Federated.

1. Logowanie się do [konsoli administracyjnej katalogu federacyjnego](https://federated.directory/of)

    ![Poradnik katalogu federacyjnego](media/federated-directory-provisioning-tutorial/companyname.png)

2. Przejdź do **katalogów > katalogów użytkownika** i wybierz dzierżawę. 

    ![katalog federowany](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Aby wygenerować stały token nośny, przejdź do **klawiszy katalogu > utwórz nowy klucz.** 

    ![katalog federowany](media/federated-directory-provisioning-tutorial/federated01.png)

4. Tworzenie klucza katalogu. 

    ![katalog federowany](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Skopiuj wartość **tokenu dostępu.** Ta wartość zostanie wprowadzona w polu **Token tajny** na karcie Inicjowanie obsługi administracyjnej aplikacji Federated Directory w witrynie Azure portal. 

    ![katalog federowany](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Dodawanie katalogu federacyjnego z galerii

Aby skonfigurować Federated Directory do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać katalog federadowany z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać katalog federatywny z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **katalog federacyjny**, wybierz pozycję **Katalog federacyjny** w panelu wyników.

    ![Katalog federatywny na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżnionego poniżej w osobnej przeglądarce. 

    ![katalog federowany](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Kliknij pozycję **ZALOGUJ SIĘ**.

    ![katalog federowany](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Ponieważ Federated Directory jest aplikacją OpenIDConnect, wybierz opcję logowania do federacyjnego katalogu przy użyciu konta służbowego Microsoft.
    
    ![katalog federowany](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Po pomyślnym uwierzytelnieniu zaakceptuj monit o zgodę na stronę zgody. Aplikacja zostanie automatycznie dodana do dzierżawy i zostaniesz przekierowany do swojego konta Federated Directory.

    ![katalog federowany Dodaj SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w katalogu federatywnym 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w katalogu Federated na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla federacyjnego katalogu w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Federated Directory**.

    ![Łącze Katalog federowany na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres URL `https://api.federated.directory/v2/` dzierżawy. Wprowadź wartość pobraną i zapisaną wcześniej z katalogu Federated w **pliku Secret Token**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z katalogiem federatywnym. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Federated Directory ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z katalogiem federatywnym**.

    ![Poradnik katalogu federacyjnego](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do federacyjnego katalogu w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w katalogu Federated do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Poradnik katalogu federacyjnego](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla katalogu federacyjnego, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić katalogowi federacyjnej, wybierając żądane wartości w **obszarze** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w katalogu federacyjnym.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
