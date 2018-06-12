---
title: 'Samouczek: Konfigurowanie LinkedIn podniesienia uprawnień dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do podniesienia uprawnień LinkedIn.
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
ms.date: 01/28/2018
ms.author: asmalser-msft
ms.openlocfilehash: 77f3660eef182afe43ec626d71d606ee10037cad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292341"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie LinkedIn podniesienia uprawnień dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest opisano czynności, które należy wykonać w podniesienia uprawnień LinkedIn i usługi Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do podniesienia uprawnień LinkedIn. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active Directory
*   Dzierżawca LinkedIn podniesienia uprawnień 
*   Konto administratora w LinkedIn podniesienia uprawnień dostępu do Centrum konta LinkedIn

> [!NOTE]
> Usługa Azure Active Directory integruje się z LinkedIn podniesienia uprawnień przy użyciu [SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-linkedin-elevate"></a>Przypisywanie użytkowników do LinkedIn podniesienia uprawnień

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane. 

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do podniesienia uprawnień LinkedIn. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do podniesienia uprawnień LinkedIn:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Ważne porady dotyczące przypisywania użytkowników do LinkedIn podniesienia uprawnień

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do LinkedIn podniesienia uprawnień, aby przetestować konfigurację inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do LinkedIn podniesienia uprawnień, należy wybrać **użytkownika** roli w oknie dialogowym przypisania. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.


## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurowanie podniesienia poziomu LinkedIn Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z podnieść LinkedIn SCIM konta użytkownika inicjowania obsługi interfejsu API i konfigurowanie usługi inicjowania obsługi administracyjnej do tworzenia, aktualizacji i wyłączyć przypisane kont użytkowników w LinkedIn podniesienia uprawnień na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

**Porada:** można też włączyć na języku SAML logowania jednokrotnego dla LinkedIn podniesienia uprawnień, zgodnie z instrukcjami podanymi w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniają.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Aby skonfigurować użytkownika automatyczne Inicjowanie obsługi konta do LinkedIn podniesienia uprawnień w usłudze Azure AD:


Pierwszym krokiem jest można pobrać tokenu dostępu LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa może samodzielnie inicjować tokenu dostępu. W Centrum konta, przejdź do **ustawienia &gt; ustawienia globalne** , a następnie otwórz **Instalator SCIM** panelu.

> [!NOTE]
> Jeśli uzyskujesz dostęp do Centrum konta bezpośrednio, a nie za pośrednictwem łącza, można osiągnąć go, wykonując następujące kroki.

1)  Zaloguj się do konta Center.

2)  Wybierz **Admin &gt; ustawienia administratora** .

3)  Kliknij przycisk **zaawansowane integracji** na lewym pasku bocznym. Użytkownik jest kierowany do Centrum konta.

4)  Kliknij przycisk **+ Dodaj nową konfigurację SCIM** i postępuj zgodnie z procedurą, wypełniając każdego pola.

> Autoassign licencji nie jest włączona, oznacza, że tylko dane użytkownika jest synchronizowany.

![LinkedIn podniesienie poziomu udostępniania](./media/active-directory-saas-linkedin-elevate-provisioning-tutorial/linkedin_elevate1.PNG)

> Po włączeniu autolicense przypisania musisz Zanotuj wystąpienia aplikacji i typu licencji. Przypisano licencje na pierwszym dostarczanych, najpierw służyć podstawę, dopóki wszystkie licencje są pobierane.

![LinkedIn podniesienie poziomu udostępniania](./media/active-directory-saas-linkedin-elevate-provisioning-tutorial/linkedin_elevate2.PNG)

5)  Kliknij przycisk **Generuj token**. Powinien zostać wyświetlony ekran tokenu dostępu w obszarze **token dostępu** pola.

6)  Zapisz tokenu dostępu do Schowka lub komputera przed opuszczeniem strony.

7) Następnie zaloguj się do [portalu Azure](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

8) Jeśli skonfigurowano już LinkedIn podniesienia uprawnień do logowania jednokrotnego, wyszukiwanie wystąpienie LinkedIn podniesienia uprawnień przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **LinkedIn podniesienia uprawnień** w galerii aplikacji. Wybierz LinkedIn podniesienia uprawnień w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

9)  Wybierz wystąpienie LinkedIn podniesienia uprawnień, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

10) Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

![LinkedIn podniesienie poziomu udostępniania](./media/active-directory-saas-linkedin-elevate-provisioning-tutorial/linkedin_elevate3.PNG)

11)  Wypełnij następujące pola w obszarze **poświadczeń administratora** :

* W **adres URL dzierżawy** wprowadź https://api.linkedin.com.

* W **klucz tajny tokenu** wprowadź token dostępu wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

* Powiadomienie o Powodzenie powinna zostać wyświetlona po stronie upperright portalu usługi.

12) Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru poniżej.

13) Kliknij pozycję **Zapisz**. 

14) W **mapowań atrybutów** Przejrzyj atrybuty użytkowników i grup, które będą synchronizowane z usługi Azure AD do podniesienia uprawnień LinkedIn. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości, które będą używane do dopasowania kont użytkowników i grup w LinkedIn podniesienia uprawnień do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

![LinkedIn podniesienie poziomu udostępniania](./media/active-directory-saas-linkedin-elevate-provisioning-tutorial/linkedin_elevate4.PNG)

15) Aby włączyć usługi Azure AD, świadczenie usługi dla LinkedIn podniesienia uprawnień, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji

16) Kliknij pozycję **Zapisz**. 

Spowoduje to uruchomienie synchronizacji początkowej użytkowników i/lub grupy przypisane do LinkedIn podniesienia uprawnień w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacji początkowej będzie trwać dłużej wykonać niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji LinkedIn podniesienia uprawnień.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
