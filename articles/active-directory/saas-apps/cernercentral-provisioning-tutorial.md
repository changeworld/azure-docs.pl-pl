---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla cerner central — azure ad'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego udostępniania użytkownikom planu w cerner central.
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
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058320"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie narzędzia Cerner Central do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w cerner central i usługi Azure AD, aby automatycznie aprowizować i wyrównywać konta użytkowników z usługi Azure AD do planu użytkownika w cerner central.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Najemca Cerner Central

> [!NOTE]
> Usługa Azure Active Directory integruje się z cerner central przy użyciu protokołu [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Przypisywanie użytkowników do cerner central

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane. 

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do cerner central. Po podjęciu decyzji, można przypisać tych użytkowników do Cerner Central, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Ważne wskazówki dotyczące przypisywania użytkowników do cerner central

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do cerner central w celu przetestowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Po zakończeniu wstępnego testowania dla jednego użytkownika, Cerner Central zaleca przypisanie całej listy użytkowników przeznaczonych do uzyskania dostępu do dowolnego rozwiązania Cerner (nie tylko Cerner Central), które mają być aprowizacji do dyżurów użytkownika Cerner.  Inne rozwiązania Cerner wykorzystują tę listę użytkowników w dyżurze użytkowników.

* Podczas przypisywania użytkownika do centrum cernera należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Użytkownicy z rolą "Dostęp domyślny" są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w cerner central

W tej sekcji przewodnik po połączeniu usługi Azure AD z listą użytkowników cerner Central przy użyciu interfejsu API inicjowania obsługi administracyjnej konta użytkownika narzędzia Cerner oraz skonfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w cerner central na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla cerner central, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają. Aby uzyskać więcej informacji, zobacz [samouczek logowania jednokrotnego Cerner Central](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika w witrynie Cerner Central w usłudze Azure AD:

Aby aprowizować konta użytkowników do cerner central, należy zażądać konta systemu Cerner Central z Cerner i wygenerować token na okaziciela OAuth, który może używać usługi Azure AD do łączenia się z punktem końcowym SCIM certyfikatu Cerner. Zaleca się również, aby integracja była wykonywana w środowisku piaskownicy Cerner przed wdrożeniem w środowisku produkcyjnym.

1. Pierwszym krokiem jest zapewnienie osobom zarządzającym integracją cerner i usługi Azure AD konto CernerCare, który jest wymagany do uzyskania dostępu do dokumentacji niezbędnej do wykonania instrukcji. Jeśli to konieczne, użyj poniższych adresów URL, aby utworzyć konta CernerCare w każdym odpowiednim środowisku.

   * Piaskownicy:https://sandboxcernercare.com/accounts/create

   * Produkcji:https://cernercare.com/accounts/create  

2. Następnie należy utworzyć konto systemowe dla usługi Azure AD. Skorzystaj z poniższych instrukcji, aby poprosić o konto systemowe dla piaskownicy i środowisk produkcyjnych.

   * Instrukcje:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Piaskownicy:https://sandboxcernercentral.com/system-accounts/

   * Produkcji:https://cernercentral.com/system-accounts/

3. Następnie wygeneruj token na okaziciela OAuth dla każdego konta systemowego. Aby to zrobić, postępuj zgodnie z poniższymi instrukcjami.

   * Instrukcje:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Piaskownicy:https://sandboxcernercentral.com/system-accounts/

   * Produkcji:https://cernercentral.com/system-accounts/

4. Na koniec musisz uzyskać identyfikatory obszaru listy użytkowników dla środowisk piaskownicy i środowiskach produkcyjnych w cernerze, aby ukończyć konfigurację. Aby uzyskać informacje na temat https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMtego, jak to nabyć, zobacz: . 

5. Teraz można skonfigurować usługi Azure AD do aprowizowania kont użytkowników do cerner. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

6. Jeśli certyfikat Cerner Central został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Cerner Central za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **certyfikat Cerner Central** w galerii aplikacji. Wybierz Cerner Central z wyników wyszukiwania i dodaj go do listy aplikacji.

7. Wybierz wystąpienie cerner central, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

8. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

   ![Certyfikacja centralna](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Wypełnij następujące pola w obszarze **Poświadczenia administratora:**

   * W polu **Adres URL dzierżawy** wprowadź adres URL w poniższym formacie, zastępując "User-Roster-Realm-ID" identyfikatorem obszaru uzyskanym w kroku #4.

    > Piaskownicy:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produkcji:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * W polu **Tajny token** wprowadź token na okaziciela OAuth wygenerowany w kroku #3 i kliknij przycisk **Test połączenia**.

   * Powiadomienie o sukcesie powinno być widoczne po prawej stronie portalu.

1. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

1. Kliknij przycisk **Zapisz**.

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika i grupy, które mają być synchronizowane z usługi Azure AD do Cerner Central. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników i grup w Cerner Central do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla cerner central, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

1. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie początkowej synchronizacji wszystkich użytkowników i/lub grup przypisanych do cerner central w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Cerner Central.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Cerner Central: Publikowanie danych tożsamości przy użyciu usługi Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Samouczek: Konfigurowanie narzędzia Cerner Central do logowania jednokrotnego za pomocą usługi Azure Active Directory](cernercentral-tutorial.md)
* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
