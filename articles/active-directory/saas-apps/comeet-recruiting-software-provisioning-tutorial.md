---
title: 'Samouczek: Konfigurowanie oprogramowania rekrutacji Comeet dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Comeet rekrutacji oprogramowania.
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
ms.openlocfilehash: d54750e731f92a574ac6571612f8dbd9054a9530
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672981"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie oprogramowania rekrutacji Comeet dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Comeet rekrutacji oprogramowanie i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup w celu Comeet rekrutacji oprogramowania.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawy Comeet rekrutacji oprogramowania](https://www.comeet.co/)
* Konto użytkownika w oprogramowaniu rekrutacji Comeet z uprawnieniami administratora.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Dodaj oprogramowanie rekrutacji Comeet z galerii

Przed rozpoczęciem konfigurowania oprogramowania rekrutacji Comeet dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać Comeet rekrutacji oprogramowania z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Comeet rekrutacji oprogramowania z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **oprogramowania rekrutacji Comeet**, wybierz opcję **oprogramowania rekrutacji Comeet** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja Comeet Recruiting Software na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Przypisywanie użytkowników do Comeet rekrutacji oprogramowania

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do oprogramowania rekrutacji Comeet. Po decyzję, należy przypisać użytkowników i/lub grup do Comeet rekrutacji oprogramowania, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Comeet rekrutacji oprogramowania

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Comeet rekrutacji oprogramowania do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Comeet rekrutacji oprogramowania, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurowanie automatycznej aprowizacji użytkowników w oprogramowaniu rekrutacji Comeet 

Przewodniki dotyczące tej sekcji Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz użytkowników i/lub grup w oprogramowaniu rekrutacji Comeet oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego Comeet rekrutacji oprogramowania, wykonując instrukcje podane w [Comeet rekrutacji oprogramowania pojedynczego logowania jednokrotnego samouczek](comeetrecruitingsoftware-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Comeet rekrutacji oprogramowania w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Comeet Recruiting Software**.

    ![Link do aplikacji Comeet Recruiting Software na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** konta Comeet rekrutacji oprogramowania zgodnie z opisem w kroku 6.

6. W [konsoli administracyjnej oprogramowania rekrutacji Comeet](https://app.comeet.co/), przejdź do **Comeet > Ustawienia > Uwierzytelnianie > Microsoft Azure**, a następnie skopiuj **klucz tajny tokenu dla swojej firmy**wartość **klucz tajny tokenu** pola w usłudze Azure AD.

    ![Comeet rekrutacji dostarczanie oprogramowania](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można Comeet rekrutacji oprogramowania. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Comeet rekrutacji oprogramowania ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-token.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Comeet**.

    ![Comeet rekrutacji mapowania użytkownika oprogramowania](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Comeet oprogramowaniem rekrutacji w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w oprogramowaniu rekrutacji Comeet operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Comeet rekrutacji atrybuty grupy oprogramowania](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD usługi oprogramowania rekrutacji Comeet inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Zdefiniować użytkowników i/lub grup, które chcesz aprowizować Comeet rekrutacji oprogramowania, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD usługi oprogramowania rekrutacji Comeet inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Oprogramowanie rekrutacji comeet aktualnie nie obsługuje grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

