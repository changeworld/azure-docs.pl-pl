---
title: 'Samouczek: Konfigurowanie LinkedIn Nawigator sprzedaży dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do LinkedIn sprzedaży nawigatora.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 544fcb2c10d2efc4ee02f9ce70c6ce06214c85cd
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215253"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie LinkedIn Nawigator sprzedaży dla użytkownika automatycznego inicjowania obsługi administracyjnej


Celem tego samouczka jest opisano czynności, które należy wykonać w Navigator sprzedaży LinkedIn i usługi Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do LinkedIn sprzedaży nawigatora. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active Directory
*   Nawigator sprzedaży LinkedIn dzierżawcy 
*   Konto administratora w Nawigatorze sprzedaży LinkedIn dostęp do Centrum konta LinkedIn

> [!NOTE]
> Azure Active Directory integruje się z przy użyciu nawigatora sprzedaży LinkedIn [SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Przypisywanie użytkowników do LinkedIn Nawigator sprzedaży

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, konieczne będzie zdecyduj, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do LinkedIn sprzedaży nawigatora. Po decyzję, można przypisać tych użytkowników do LinkedIn Nawigator sprzedaży, postępując zgodnie z instrukcjami poniżej:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Ważne porady dotyczące przypisywania użytkowników do LinkedIn Nawigator sprzedaży

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do Nawigatora sprzedaży LinkedIn do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do LinkedIn Nawigator sprzedaży, należy wybrać **użytkownika** roli w oknie dialogowym przypisania. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.


## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurowanie do LinkedIn sprzedaży Nawigatora Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika SCIM LinkedIn sprzedaży Navigator inicjowania obsługi interfejsu API i konfigurowanie usługi inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączyć przypisany kont użytkowników w Nawigatorze sprzedaży LinkedIn na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

> [!TIP]
> Można też włączone na języku SAML logowania jednokrotnego dla LinkedIn Nawigator sprzedaży, postępując zgodnie z instrukcjami zawarte w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniają.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do LinkedIn Nawigatora sprzedaży w usłudze Azure AD:


Pierwszym krokiem jest można pobrać tokenu dostępu LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa może samodzielnie inicjować tokenu dostępu. W Centrum konta, przejdź do **ustawienia &gt; ustawienia globalne** , a następnie otwórz **Instalator SCIM** panelu.

> [!NOTE]
> Jeśli uzyskujesz dostęp do Centrum konta bezpośrednio, a nie za pośrednictwem łącza, można osiągnąć go, wykonując następujące kroki.

1)  Zaloguj się do konta Center.

2)  Wybierz **Admin &gt; ustawienia administratora** .

3)  Kliknij przycisk **zaawansowane integracji** na lewym pasku bocznym. Użytkownik jest kierowany do Centrum konta.

4)  Kliknij przycisk **+ Dodaj nową konfigurację SCIM** i postępuj zgodnie z procedurą, wypełniając każdego pola.

> Autoassign licencji nie jest włączona, oznacza, że tylko dane użytkownika jest synchronizowany.

![Nawigator sprzedaży LinkedIn inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

> Po włączeniu autolicense przypisania musisz Zanotuj wystąpienia aplikacji i typu licencji. Przypisano licencje na pierwszym dostarczanych, najpierw służyć podstawę, dopóki wszystkie licencje są pobierane.

![Nawigator sprzedaży LinkedIn inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5)  Kliknij przycisk **Generuj token**. Powinien zostać wyświetlony ekran tokenu dostępu w obszarze **token dostępu** pola.

6)  Zapisz tokenu dostępu do Schowka lub komputera przed opuszczeniem strony.

7) Następnie zaloguj się do [portalu Azure](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

8) Nawigator sprzedaży LinkedIn został już skonfigurowany dla logowania jednokrotnego, wyszukaj wystąpieniem LinkedIn Nawigator sprzedaży, korzystając z pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **LinkedIn sprzedaży Navigator** w galerii aplikacji. Wybierz Navigator sprzedaży LinkedIn z wyników wyszukiwania i dodaj go do listy aplikacji.

9)  Wybierz wystąpienie LinkedIn Nawigator sprzedaży, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

10) Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

![Nawigator sprzedaży LinkedIn inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11)  Wypełnij następujące pola w obszarze **poświadczeń administratora** :

* W **adres URL dzierżawy** wprowadź https://api.linkedin.com.

* W **klucz tajny tokenu** wprowadź token dostępu wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

* Powiadomienie o Powodzenie powinna zostać wyświetlona po stronie upperright portalu usługi.

12) Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru poniżej.

13) Kliknij pozycję **Zapisz**. 

14) W **mapowań atrybutów** Przejrzyj atrybuty użytkowników i grup, które będą synchronizowane z usługi Azure AD do LinkedIn sprzedaży nawigatora. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości, które będą używane do dopasowania kont użytkowników i grup w Nawigatorze sprzedaży LinkedIn dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

![Nawigator sprzedaży LinkedIn inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15) Aby włączyć usługi Azure AD, świadczenie usługi dla LinkedIn Navigator sprzedaży, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

16) Kliknij pozycję **Zapisz**. 

Spowoduje to uruchomienie synchronizacji początkowej użytkowników i/lub grupy przypisane do LinkedIn Nawigatora sprzedaży w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacji początkowej będzie trwać dłużej wykonać niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji LinkedIn sprzedaży nawigatora.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
