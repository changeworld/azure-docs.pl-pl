---
title: 'Samouczek: Konfigurowanie MerchLogix dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058122"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie MerchLogix dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w MerchLogix i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu MerchLogix.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Dzierżawa MerchLogix
* Techniczne kontakt MerchLogix, który udostępnia adres URL punktu końcowego Standard SCIM i wpisu tajnego token jest wymagany do aprowizacji użytkownika

## <a name="adding-merchlogix-from-the-gallery"></a>Dodawanie MerchLogix z galerii

Przed skonfigurowaniem MerchLogix dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać MerchLogix z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać MerchLogix z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje w przedsiębiorstwie sekcji][2]

3. Aby dodać MerchLogix, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **MerchLogix**.

5. W panelu wyników wybierz **MerchLogix**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać MerchLogix do listy aplikacji SaaS.

    ![MerchLogix inicjowania obsługi administracyjnej.][4]

## <a name="assigning-users-to-merchlogix"></a>Przypisywanie użytkowników do MerchLogix

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD. 

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do MerchLogix. Po decyzję, możesz przypisać użytkownikom i/lub grup MerchLogix zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Ważne wskazówki dotyczące przypisywania użytkowników do MerchLogix

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do MerchLogix do przetestowania Twojej początkowej automatyczne aprowizowanie użytkowników konfiguracji. Dodatkowych użytkowników i/lub grup można przypisać później po pomyślnych testów.

* Podczas przypisywania użytkowników do MerchLogix, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurowanie automatycznej aprowizacji użytkowników do MerchLogix 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w MerchLogix oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla MerchLogix, wykonując instrukcje podane w [MerchLogix pojedynczego logowania jednokrotnego samouczek](merchlogix-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla MerchLogix w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz MerchLogix z listy aplikacji SaaS.

3. Wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![MerchLogix inicjowania obsługi administracyjnej.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. W obszarze **poświadczeń administratora** sekcji:

    * W **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard SCIM udostępniane przez usługi MerchLogix technicznych skontaktuj się z pomocą.

    * W **klucz tajny tokenu** wprowadź token wpisu tajnego udostępniane przez usługi MerchLogix technicznych skontaktuj się z pomocą.

6. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z MerchLogix. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto MerchLogix ma uprawnienia administratora i spróbuj ponownie.

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

8. Kliknij pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do MerchLogix**.

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD MerchLogix w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w MerchLogix operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

11. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do MerchLogix**.

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD MerchLogix w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w MerchLogix dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

13. Aby włączyć usługi Azure AD, usługi dla MerchLogix inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

14. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na MerchLogix aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
