---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla lucidchart — usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do lucidchart.
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
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057332"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie lucidchart do automatycznego inicjowania obsługi administracyjnej użytkownika

Celem tego samouczka jest pokazanie kroki, które należy wykonać w lucidchart i usługi Azure AD, aby automatycznie aprowizować i de-provision kont użytkowników z usługi Azure AD do LucidChart. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active
* Dzierżawa LucidChart z [planem Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) lub lepiej włączona
* Konto użytkownika w lucidchart z uprawnieniami administratora

## <a name="assigning-users-to-lucidchart"></a>Przypisywanie użytkowników do wykresu lucidchart

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji LucidChart. Po podjęciu decyzji, można przypisać tych użytkowników do aplikacji LucidChart, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Ważne wskazówki dotyczące przypisywania użytkowników do lucidchart

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do lucidchart do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do lucidchart, należy wybrać rolę **użytkownika** lub inną prawidłową rolę specyficzne dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Rola **domyślnego dostępu** nie działa w przypadku inicjowania obsługi administracyjnej, a ci użytkownicy są pomijane.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika do wykresu lucidchart

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika lucidchart i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w lucidchart na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla lucidchart, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika do narzędzia LucidChart w usłudze Azure AD

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli lucidchart został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie lucidchart przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **lucidchart** w galerii aplikacji. Wybierz LucidChart z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie LucidChart, a następnie wybierz kartę **Inicjowania obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Udostępnianie obsługi administracyjnej lucidchart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. W sekcji **Poświadczenia administratora** wprowadź **tajny token wygenerowany** przez konto LucidChart (token można znaleźć pod swoim kontem:**SciM****integracji** > aplikacji **zespołu).** > 

    ![Udostępnianie obsługi administracyjnej lucidchart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. W witrynie Azure portal kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją LucidChart. Jeśli połączenie nie powiedzie się, upewnij się, że konto LucidChart ma uprawnienia administratora i spróbuj ponownie wykonać krok 5.

7. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail powiadomień,** i zaznacz pole wyboru "Wyślij powiadomienie e-mail w przypadku wystąpienia błędu".

8. Kliknij przycisk **Zapisz**.

9. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z wykresem lucidchart**.

10. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do LucidChart. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w LucidChart dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla lucidchart, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

12. Kliknij przycisk **Zapisz**.

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do lucidchart w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
