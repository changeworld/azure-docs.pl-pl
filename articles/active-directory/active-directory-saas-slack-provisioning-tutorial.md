---
title: 'Samouczek: Konfigurowanie zapas czasu dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego udostępniania i usuwanie kont użytkowników zapas czasu.
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
ms.reviewer: asmalser
ms.openlocfilehash: 897121e0dcaaf417430b892c501a243303ae9b6e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zapas czasu dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest opisano kroki należy wykonać w zapas czasu i Azure AD, aby automatycznie Zainicjuj obsługę i usuwanie kont użytkowników z usługi Azure AD do zapas czasu. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active Active directory
*   Zapas czasu dzierżawy z [Plus planu](https://aadsyncfabric.slack.com/pricing) lub lepiej jest włączone 
*   Konto użytkownika w zapas czasu z uprawnieniami administratora zespołu 

Uwaga: Usługi Azure AD, inicjowania obsługi administracyjnej integracji zależy od [API SCIM zapas czasu](https://api.slack.com/scim) które są dostępne dla zespołów Slack na Plus plan lub większą.

## <a name="assigning-users-to-slack"></a>Przypisywanie użytkowników do zapas czasu

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Slack. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do Slack aplikacji:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Ważne porady dotyczące przypisywania użytkowników do zapas czasu

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do zapas czasu, aby przetestować konfigurację inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do zapas czasu, należy wybrać **użytkownika** lub rola "Grupy" w oknie dialogowym przypisania. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.


## <a name="configuring-user-provisioning-to-slack"></a>Konfigurowanie inicjowania obsługi administracyjnej zapas czasu użytkownika 

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w zapas czasu inicjowania obsługi interfejsu API i konfigurowanie usługi inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączyć przypisany kont użytkowników w zapas czasu na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

**Porada:** można też włączyć na języku SAML rejestracji jednokrotnej dla zapas czasu, zgodnie z instrukcjami podanymi w (Azure portal) [https://portal.azure.com]. Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do zapas czasu w usłudze Azure AD:


1)  W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2) Jeśli zapas czasu został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpieniem zapas czasu za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Slack** w galerii aplikacji. Wybierz zapas czasu w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3)  Wybierz wystąpienia programu zapas czasu, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4)  Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

![Zapas czasu obsługi](./media/active-directory-saas-slack-provisioning-tutorial/Slack1.PNG)

5)  W obszarze **poświadczeń administratora** kliknij **autoryzacji**. Spowoduje to otwarcie okna dialogowego Slack autoryzacji w nowym oknie przeglądarki. 

6) W nowym oknie Zaloguj się do zapas czasu przy użyciu konta administratora zespołu. w oknie dialogowym autoryzacji wynikowy, wybierz Slack zespół, który chcesz włączyć udostępnianie, a następnie wybierz **autoryzacji**. Po ukończeniu, wróć do portalu Azure, aby zakończyć konfigurację inicjowania obsługi administracyjnej.

![Okno dialogowe autoryzacji](./media/active-directory-saas-slack-provisioning-tutorial/Slack3.PNG)

7) W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Slack. Jeśli połączenie nie powiedzie się, upewnij się, że Slack konto ma uprawnienia administratora zespołu i spróbuj ponownie krok "Autoryzuj".

8) Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru poniżej.

9) Kliknij pozycję **Zapisz**. 

10) W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom zapas czasu**.

11) W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługą Azure AD do zapas czasu. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości, które będą używane do dopasowania kont użytkowników w zapas czasu dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12) Aby włączyć usługi Azure AD usługi dla zapas czasu inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

13) Kliknij pozycję **Zapisz**. 

Spowoduje to uruchomienie synchronizacji początkowej użytkowników i/lub grupy przypisane do zapas czasu w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacji początkowej będzie trwać dłużej wykonać niż kolejne synchronizacje, występujące co 10 minut, tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raporty działania, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Slack aplikacji.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcjonalnie] Konfigurowanie grupy obiektu inicjowania obsługi administracyjnej zapas czasu 

Opcjonalnie można włączyć udostępnianie obiektów grupy z usługi Azure AD do zapas czasu. To jest inny niż "przypisanie grupy użytkowników", w tej grupie rzeczywistego obiektu oprócz jej elementów członkowskich będą replikowane z usługi Azure AD do zapas czasu. Na przykład jeśli istnieje grupa o nazwie "My" w usłudze Azure AD, wewnątrz zapas czasu zostanie utworzona grupa identitical o nazwie "My".

### <a name="to-enable-provisioning-of-group-objects"></a>Aby włączyć udostępnianie obiektów grupy:

1) W sekcji mapowania wybierz **synchronizacji Azure grup usługi Active Directory do zapas czasu**.

2) W bloku atrybutu mapowania wartość włączone tak.

3) W **mapowań atrybutów** Przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD do zapas czasu. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości, które będą używane do dopasowania grup w zapas czasu dla operacji update. 

4) Kliknij pozycję **Zapisz**.

Ten wynik w żadnych obiektów grupy przypisane do zapas czasu w **użytkowników i grup** sekcji pełni synchronizowane z usługi Azure AD do zapas czasu. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Slack aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-enterprise-apps-manage-provisioning.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
