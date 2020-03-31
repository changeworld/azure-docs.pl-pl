---
title: 'Samouczek: Konfigurowanie starleaf do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników w witrynie StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064295"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie StarLeaf do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w starleaf i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do starleaf.

> [!NOTE]
>  W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca StarLeaf](https://www.starleaf.com/solutions/).
* Konto użytkownika w StarLeaf z uprawnieniami administratora.

## <a name="assign-users-to-starleaf"></a>Przypisywanie użytkowników do StarLeaf
Usługa Azure Active Directory używa koncepcji o nazwie przydziały, aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, należy zdecydować, którzy użytkownicy i grupy w usłudze Azure AD potrzebują dostępu do StarLeaf. Następnie możesz przypisać użytkowników i grupy do StarLeaf, postępując zgodnie z [tymi instrukcjami.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Ważne wskazówki dotyczące przypisywania użytkowników do StarLeaf

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do StarLeaf do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowych użytkowników i grupy można przypisać później.

* Po przypisaniu użytkownika do StarLeaf należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-starleaf-for-provisioning"></a>Konfigurowanie StarLeaf do inicjowania obsługi administracyjnej

Przed skonfigurowaniem StarLeaf do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy skonfigurować inicjowanie obsługi administracyjnej scim w StarLeaf:

1. Zaloguj się do [konsoli administracyjnej StarLeaf](https://portal.starleaf.com/#page=login). Przejdź do **integracji** > **Dodaj integrację**.

    ![StarLeaf Dodaj SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Wybierz **typ, który** ma być usługą Microsoft Azure Active Directory. Wprowadź odpowiednią nazwę w **pliku Name**. Kliknij przycisk **Zastosuj**.

    ![StarLeaf Dodaj SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Następnie zostaną wyświetlone podstawowe wartości adresu URL i **tokenu dostępu** **scim.** Te wartości zostaną wprowadzone w adresie **URL dzierżawy** i **tajne token** pola na karcie inicjowania obsługi administracyjnej aplikacji StarLeaf w witrynie Azure portal. 

    ![StarLeaf Utwórz token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Dodaj StarLeaf z galerii

Aby skonfigurować StarLeaf do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać StarLeaf z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać StarLeaf z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **StarLeaf**, wybierz **StarLeaf** w panelu wyników.
    ![StarLeaf na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w starleaf

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w StarLeaf na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **StarLeaf**.

    ![Łącze StarLeaf na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora wprowadź podstawowe wartości adresu URL i **tokenu dostępu** **SCIM** pobrane wcześniej odpowiednio w **adresie URL dzierżawy** i **tokenie tajnym.** Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się ze starleaf. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto StarLeaf ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole **Wyślij powiadomienie e-mail po wystąpieniu błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z gwiazdką StarLeaf**.

    ![StarLeaf Utwórz token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do StarLeaf w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w StarLeaf dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![StarLeaf Utwórz token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla starleaf, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić StarLeaf, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć sekcji **Szczegóły synchronizacji** do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w starleaf.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* StarLeaf obecnie nie obsługuje inicjowania obsługi administracyjnej grupy. 
* StarLeaf wymaga, aby wartości **poczty e-mail** i **userName** miały taką samą wartość źródłową.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](../app-provisioning/check-status-user-account-provisioning.md)
