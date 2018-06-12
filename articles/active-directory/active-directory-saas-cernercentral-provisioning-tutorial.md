---
title: 'Samouczek: Konfigurowanie centralnego Cerner dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie udostępnić użytkownikom spisu w środkowej Cerner.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 5440f9e747957a017eac109cddc4881244e506eb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292691"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie centralnego Cerner dla użytkownika automatycznego inicjowania obsługi administracyjnej

Celem tego samouczka jest opisano czynności, które należy wykonać w środkowej Cerner i usługi Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do spisu użytkownika, Indie środkowe Cerner. 


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active Directory
*   Dzierżawy Cerner środkowe 

> [!NOTE]
> Usługa Azure Active Directory integruje się z centralnego Cerner przy użyciu [SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-cerner-central"></a>Przypisywanie użytkowników do centralnego Cerner

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD są synchronizowane. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy podjąć decyzję dotyczącą jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do centralnego Cerner. Po decyzję, można przypisać tych użytkowników do centralnego Cerner, postępując zgodnie z instrukcjami poniżej:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Ważne porady dotyczące przypisywania użytkowników do centralnego Cerner

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do centralnego Cerner do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

* Po zakończeniu testowania początkowej dla pojedynczego użytkownika centralnego Cerner zaleca przypisywanie całą listę użytkownicy mają dostęp do wszelkich rozwiązań Cerner (nie tylko Cerner centralnego) być przygotowana do Cerner przez użytkownika spisu.  Inne rozwiązania Cerner korzystać z tej listy użytkowników w spisu użytkownika.

*   Przypisanie użytkownika do centralnego Cerner, należy wybrać **użytkownika** roli w oknie dialogowym przypisania. Użytkownicy z rolą "Domyślnego dostępu" są wykluczone z inicjowania obsługi administracyjnej.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurowanie do centralnego Cerner Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi użytkownika przez łączenie usługi Azure AD z centralnego Cerner spisu użytkownika przy użyciu konta użytkownika SCIM Cerner przez Inicjowanie obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania przypisany użytkownik, na podstawie kont w środkowej Cerner Przypisywanie użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML logowania jednokrotnego dla siedziby Cerner, wykonując instrukcje podane w [portalu Azure (https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniają. Aby uzyskać więcej informacji, zobacz [Cerner centralnego pojedynczego logowania jednokrotnego samouczek](active-directory-saas-cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do centralnego Cerner w usłudze Azure AD:


Aby udostępnić konta użytkowników do centralnego Cerner, należy poprosić konta systemu centralnego Cerner Cerner oraz do generowania tokenu elementu nośnego OAuth używanego przez usługi Azure AD do nawiązania połączenia przez Cerner SCIM endpoint. Zalecane jest również, że integracji można wykonać w środowisku piaskownicy Cerner przed wdrożeniem w środowisku produkcyjnym.

1.  Pierwszym krokiem jest zapewnienie osobom Zarządzanie Cerner i integracji z usługą Azure AD ma konto CernerCare, co jest wymagane, aby uzyskać dostęp do dokumentacji, które należy wykonać instrukcje. Jeśli to konieczne, umożliwiają utworzenie kont CernerCare w każdym środowisku dotyczy poniżej adresy URL.

   * Piaskownicy:  https://sandboxcernercare.com/accounts/create

   * Produkcyjnym:  https://cernercare.com/accounts/create  

2.  Następnie należy utworzyć konta systemu dla usługi Azure AD. Poniższe instrukcje umożliwiają żądania konto systemowe dla środowiska izolowanego i produkcji.

   * Instrukcje:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Piaskownicy: https://sandboxcernercentral.com/system-accounts/

   * Produkcyjnym:  https://cernercentral.com/system-accounts/

3.  Następnie można wygenerować tokenu elementu nośnego OAuth dla każdego konta użytkownika systemu. Aby to zrobić, postępuj zgodnie z poniższymi instrukcjami.

   * Instrukcje:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Piaskownicy: https://sandboxcernercentral.com/system-accounts/

   * Produkcyjnym:  https://cernercentral.com/system-accounts/

4. Na koniec należy uzyskać identyfikatory obszaru spisu użytkownika dla środowisk zarówno piaskownicy, jak i produkcji w Cerner, aby zakończyć konfigurację. Aby uzyskać informacje dotyczące sposobu uzyskania to, zobacz: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Teraz można skonfigurować usługi Azure AD do kont użytkowników należy Cerner. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

6. Środkowe Cerner został już skonfigurowany dla logowania jednokrotnego, wyszukaj wystąpienia środkowej Cerner przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **centralnego Cerner** w galerii aplikacji. Wybierz Cerner centralnego w wynikach wyszukiwania i dodaj ją do listy aplikacji.

7.  Wybierz wystąpienie Cerner środkowej, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

8.  Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

   ![Środkowe Cerner inicjowania obsługi administracyjnej](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Wypełnij następujące pola w obszarze **poświadczeń administratora**:

   * W **adres URL dzierżawy** wprowadź adres URL w formacie poniżej, zastępując "Użytkownik-spisu-obszaru-ID" z Identyfikatorem obszaru uzyskaną w kroku #4.

> Piaskownicy: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produkcyjnym: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * W **klucz tajny tokenu** wprowadź token elementu nośnego OAuth wygenerowany w kroku #3 i kliknij przycisk **Testuj połączenie**.

   * Powiadomienie o Powodzenie powinna zostać wyświetlona po stronie upperright portalu usługi.

10. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru poniżej.

11. Kliknij pozycję **Zapisz**. 

12. W **mapowań atrybutów** Przejrzyj atrybuty użytkowników i grup, które mają być synchronizowane z usługi Azure AD do centralnego Cerner. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników i grup w środkowej Cerner dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

13. Aby włączyć usługi Azure AD usługi dla siedziby Cerner inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

14. Kliknij pozycję **Zapisz**. 

Spowoduje to uruchomienie synchronizacji początkowej użytkowników i/lub grupy przypisane do centralnego Cerner w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Cerner centralnego.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Środkowe Cerner: Publikowania danych tożsamości za pomocą usługi Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Samouczek: Konfigurowanie centralnego Cerner na potrzeby rejestracji jednokrotnej z usługą Azure Active Directory](active-directory-saas-cernercentral-tutorial.md)
* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
