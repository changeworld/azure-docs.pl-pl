---
title: 'Samouczek: Konfigurowanie menedżera haseł aplikacji Keeper & digital vault w celu automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania z obsługi administracyjnej kont użytkowników do menedżera haseł keeper & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057528"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Menedżera haseł aplikacji Keeper & Digital Vault w celu automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w menedżerze haseł programu Keeper & digital vault i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do menedżera haseł keeper & Digital Vault.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Menedżer haseł keeper & dzierżawcę Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Konto użytkownika w Menedżerze haseł aplikacji Keeper & Digital Vault z uprawnieniami administratora.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodaj Menedżera haseł keeper & Digital Vault z galerii

Przed skonfigurowaniem programu Keeper Password Manager & Digital Vault do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać menedżera haseł aplikacji Keeper & Digital Vault z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Menedżera haseł aplikacji Keeper & Digital Vault z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **menedżera haseł keeper & Digital Vault**, wybierz pozycję Keeper Password Manager & Digital **Vault** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Usługa Keeper Password Manager & Digital Vault na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Przypisywanie użytkowników do Menedżera haseł keeper & Digital Vault

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do menedżera haseł aplikacji Keeper & Digital Vault. Po podjęciu decyzji możesz przypisać tych użytkowników i/lub grupy do Menedżera haseł keeper & Digital Vault, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Menedżera haseł keeper & Digital Vault

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do menedżera haseł programu Keeper & digital vault w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Menedżera haseł aplikacji Keeper & Digital Vault należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej menedżera haseł w aplikacji Keeper & Digital Vault 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Keeper Password Manager & Digital Vault na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla menedżera haseł Keeper & Digital Vault, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Keeper Password Manager & Digital Vault](keeperpasswordmanager-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej menedżera haseł keeper & digital vault w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Keeper Password Manager & Digital Vault**.

    ![Link do usługi Keeper Password Manager & Digital Vault na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** i **tajny token** menedżera haseł opiekuna & konto Digital Vault zgodnie z opisem w kroku 6.

6. Zaloguj się do [konsoli administracyjnej keeper](https://keepersecurity.com/console/#login). Kliknij **pozycję Administrator** i wybierz istniejący węzeł lub utwórz nowy. Przejdź do karty **Inicjowanie obsługi administracyjnej** i wybierz pozycję **Dodaj metodę**.

    ![Konsola administracyjna Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Wybierz **SCIM (System zarządzania tożsamościami między domenami**.

    ![Keeper Dodaj SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kliknij pozycję **Utwórz token inicjowania obsługi administracyjnej**.

    ![Tworzenie punktu końcowego programu Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Skopiuj wartości **adresu URL** i **tokenu** i wklej je do **adresu URL dzierżawy** i **tokenu tajnego** w usłudze Azure AD. Kliknij **przycisk Zapisz,** aby ukończyć konfigurację inicjowania obsługi administracyjnej w aplikacji Keeper.

    ![Token tworzenia uchwytu](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z menedżerem haseł aplikacji Keeper & Digital Vault. Jeśli połączenie nie powiedzie się, upewnij się, że Menedżer haseł keeper & konto Digital Vault ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z Menedżerem haseł keeper & Digital Vault**.

    ![Mapowania użytkowników aplikacji Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do menedżera haseł programu Keeper & Digital Vault w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Menedżerze haseł aplikacji Keeper & Digital Vault do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z Menedżerem haseł keeper & Digital Vault**.

    ![Mapowania grupy keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do menedżera haseł programu Keeper & Digital Vault w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w Menedżerze haseł aplikacji Keeper & Digital Vault do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla menedżera haseł aplikacji Keeper & digital vault, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Menedżerowi haseł aplikacji Keeper & Digital Vault, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

17. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w menedżerze haseł keeper & digital vault.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Menedżer haseł aplikacji Keeper & Digital Vault wymaga, aby **wiadomości e-mail** i **userName** miały taką samą wartość źródłową, ponieważ wszelkie aktualizacje tych atrybutów zmodyfikują inną wartość.
* Keeper Password Manager & Digital Vault nie obsługuje usuwania przez użytkownika, tylko wyłącza. Wyłączeni użytkownicy będą pojawiać się jako zablokowani w interfejsie użytkownika konsoli administracyjnej keepera.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

