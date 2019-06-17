---
title: 'Samouczek: Konfigurowanie centralnego Cerner dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory, aby automatycznie aprowizować użytkowników do spisu, Indie środkowe Cerner.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61e88e0fe7e6eec5b3cdfd03755a186744b77b47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964203"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie centralnego Cerner dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w regionie centralnym Cerner i usługi Azure AD do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do spisu użytkownika, Indie środkowe Cerner.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawy Cerner środkowa

> [!NOTE]
> Usługa Azure Active Directory integruje się z centralnego Cerner przy użyciu [Standard SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-cerner-central"></a>Przypisywanie użytkowników do centralnego Cerner

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD. 

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do centralnych Cerner. Po decyzję, możesz przypisać użytkowników do centralnego Cerner, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Ważne wskazówki dotyczące przypisywania użytkowników do centralnego Cerner

* Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do centralnego Cerner do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Po zakończeniu początkowej fazie testowania dla pojedynczego użytkownika centralnego Cerner zaleca się przypisywania całą listę użytkowników, którzy mają dostęp do wszelkich rozwiązań Cerner (nie tylko Cerner centralny) być przygotowana do spisu użytkownika Cerner firmy.  Inne rozwiązania Cerner korzystać z tej listy użytkowników w spisu użytkownika.

* Podczas przypisywania użytkowników do centralnego Cerner, musisz wybrać **użytkownika** roli w oknie dialogowym przydział. Użytkownicy z rolą "Domyślnego dostępu" są wykluczane z inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurowaniem aprowizowania użytkowników w centralnych Cerner

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z centralnego Cerner użytkownika spisu przy użyciu konta użytkownika Standard SCIM firmy Cerner aprowizujący interfejs API oraz konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz przypisanych użytkowników, kont, Indie środkowe Cerner zależnie od Przypisywanie użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla siedziby Cerner, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełniają się wzajemnie. Aby uzyskać więcej informacji, zobacz [centralnego Cerner pojedynczego logowania jednokrotnego samouczek](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do centralnego Cerner w usłudze Azure AD:

Aby zapewnić aprowizację kont użytkowników do centralnego Cerner, musisz poprosić konta system Cerner Central Cerner oraz do generowania tokenu elementu nośnego OAuth, używanego przez usługi Azure AD do łączenia z punktem końcowym Standard SCIM Cerner firmy. Zalecane jest również, że integracja wykonywanych w środowisku piaskownicy Cerner przed wdrożeniem w środowisku produkcyjnym.

1. Pierwszym krokiem jest zapewnienie osobom Zarządzanie Cerner i integracji z usługą Azure AD ma konto CernerCare, który jest wymagany do dostęp do dokumentacji, które są niezbędne do zakończenia z instrukcjami. Jeśli to konieczne, umożliwia tworzenie kont CernerCare w każdym środowisku zastosowanie poniższych adresów URL.

   * Sandbox:  https://sandboxcernercare.com/accounts/create

   * Produkcyjne:  https://cernercare.com/accounts/create  

2. Następnie należy utworzyć konta systemowego dla usługi Azure AD. Żądanie konta systemowego dla środowiska izolowanego i produkcji, skorzystaj z poniższych instrukcji.

   * Instrukcje:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produkcyjne:  https://cernercentral.com/system-accounts/

3. Kolejny krok to Generowanie tokenu elementu nośnego OAuth dla każdego konta system. Aby to zrobić, postępuj zgodnie z poniższymi instrukcjami.

   * Instrukcje:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produkcyjne:  https://cernercentral.com/system-accounts/

4. Ponadto konieczne jest uzyskanie obszaru spisu identyfikatory dla środowisk zarówno piaskownicy, jak i produkcyjnych w Cerner, aby zakończyć konfigurację. Aby uzyskać informacje na temat sposobu uzyskania to, zobacz: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Teraz można skonfigurować usługi Azure AD do aprowizacji kont użytkowników do Cerner. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

6. Środkowe Cerner została już skonfigurowana dla logowania jednokrotnego, wyszukaj wystąpienie Cerner środkowej za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **centralnego Cerner** w galerii aplikacji. Wybierz centralnego Cerner z wyników wyszukiwania i dodać go do listy aplikacji.

7. Wybierz wystąpienie usługi Cerner środkowej, a następnie wybierz **aprowizacji** kartę.

8. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

   ![Środkowe Cerner inicjowania obsługi administracyjnej](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Wypełnij następujące pola w obszarze **poświadczeń administratora**:

   * W **adres URL dzierżawy** wprowadź adres URL w formacie poniżej, zastępując "User-spisu-obszaru-ID" o identyfikatorze obszaru uzyskaną w kroku #4.

    > Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produkcyjne: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * W **klucz tajny tokenu** polu wprowadź tokenu elementu nośnego OAuth, które są generowane w kroku #3 a kliknij **Testuj połączenie**.

   * Powiadomienie o powodzeniu powinien być widoczny po stronie upperright portalu.

1. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

1. Kliknij pozycję **Zapisz**.

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkowników i grup, które mają być synchronizowane z usługi Azure AD do centralnego Cerner. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników i grup w środkowej Cerner operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, usługi dla siedziby Cerner inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

1. Kliknij pozycję **Zapisz**.

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do centralnego Cerner w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, zawierające wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji Cerner centralnego.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Środkowa Cerner: Publikowania danych tożsamości za pomocą usługi Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Samouczek: Konfigurowanie centralnego Cerner logowanie jednokrotne z usługą Azure Active Directory](cernercentral-tutorial.md)
* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
