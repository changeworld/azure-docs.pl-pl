---
title: 'Samouczek: Konfigurowanie powiększenia do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizacji i usuwanie kont użytkowników powiększenia.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671001"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie powiększenia do automatycznej aprowizacji użytkowników

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w powiększenia i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizować i anulować obsługę użytkowników i/lub grup, aby powiększyć.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawy powiększenia](https://zoom.us/pricing)
* Konto użytkownika powiększenia z uprawnieniami administratora

## <a name="add-zoom-from-the-gallery"></a>Dodaj powiększenia z galerii

Przed skonfigurowaniem powiększenia do automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać powiększenia z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać powiększenia z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **powiększenia**, wybierz opcję **powiększenia** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja Zoom na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Przypisywanie użytkowników do powiększania

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed konfigurowania i włączenie automatycznej aprowizacji użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do powiększania. Po decyzję, należy przypisać użytkowników i/lub grup do powiększania, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Ważne wskazówki dotyczące przypisywania użytkowników do powiększania

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do powiększenia do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkownika, aby powiększyć, musisz wybrać dowolną prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Konfigurowanie automatycznej aprowizacji użytkowników do powiększania 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników lub grup w powiększenia oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla powiększenia, wykonując instrukcje podane w [powiększenia pojedynczego logowania jednokrotnego samouczek](zoom-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla powiększenia w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **powiększenia**.

    ![Link aplikacji Zoom na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź `https://api.zoom.us/scim` w **adres URL dzierżawy**. Aby pobrać **klucz tajny tokenu** konta powiększenia, postępuj zgodnie z tym przewodnikiem zgodnie z opisem w kroku 6.

6. Zaloguj się do Twojej [powiększenia konsoli administracyjnej](https://zoom.us/signin). Przejdź do **Zaawansowane > Powiększ dla deweloperów** w okienku nawigacji po lewej stronie.

    ![Integracje powiększenia](media/zoom-provisioning-tutorial/zoom01.png)

    Przejdź do **Zarządzaj** w prawym górnym rogu strony. 

    ![Zainstaluj powiększenia](media/zoom-provisioning-tutorial/zoom02.png)

    Przejdź do utworzonej aplikacji usługi Azure AD. 
    
    ![Powiększanie aplikacji](media/zoom-provisioning-tutorial/zoom03.png)

    Wybierz **poświadczenia aplikacji** w okienku nawigacji po lewej stronie.

    ![Powiększanie aplikacji](media/zoom-provisioning-tutorial/zoom04.png)

    Pobieranie tokenu JWT wartości poniżej i dane wejściowe na **klucz tajny tokenu** pola w usłudze Azure AD. Jeśli potrzebujesz uzyskać nowy token niewygasającym, musisz ponownie skonfigurować po upływie czasu, która będzie automatycznie Wygeneruj nowy token. 

    ![Zainstaluj powiększenia](media/zoom-provisioning-tutorial/zoom05.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z powiększenia. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto powiększenia ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do powiększania**.

    ![Powiększenie mapowania użytkownika](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, aby powiększyć w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w powiększenia do operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.
    
     ![Powiększenie mapowania użytkownika](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD, usługi, które do powiększania inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.
    
    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Zdefiniować użytkowników i/lub grup, które chcesz aprowizować, aby powiększyć, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na powiększenia.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Powiększenie nie obsługuje udostępniania dla grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
