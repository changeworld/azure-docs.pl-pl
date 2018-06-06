---
title: 'Samouczek: Konfigurowanie usługi ServiceNow dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do usługi ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d3e64cc232aa01a5619a55290f9b07421165a6d5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698734"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Samouczek: Konfigurowanie usługi ServiceNow dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory

Celem tego samouczka jest opisano czynności, które należy wykonać w ServiceNow i Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD do usługi ServiceNow.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi ServiceNow, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dla usługi ServiceNow, wystąpienie lub dzierżawy usługi ServiceNow, wersja Calgary lub nowszej
- Usługi ServiceNow Express, wystąpienie usługi ServiceNow Express, wersja Helsinkach lub nowszej

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Przypisywanie użytkowników do usługi ServiceNow

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi ServiceNow. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników aplikacji usługi ServiceNow: [przypisać użytkownika lub grupy do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Zalecane jest pojedynczego użytkownika usługi Azure AD jest przypisany do usługi ServiceNow do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.
>*   Przypisanie użytkownika do usługi ServiceNow, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników automatycznych

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika usługi ServiceNow na inicjowanie obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w usługi ServiceNow na podstawie przypisań użytkowników i grup w usłudze Azure AD.

> [!TIP]
>Można też włączyć na języku SAML logowania jednokrotnego dla usługi ServiceNow, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Skonfiguruj użytkownika automatyczne Inicjowanie obsługi konta

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli został już skonfigurowany dla logowania jednokrotnego usługi ServiceNow, wyszukaj dla swojego wystąpienia usługi ServiceNow przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **ServiceNow** w galerii aplikacji. Wybierz usługi ServiceNow z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie usługi ServiceNow, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **inicjowania obsługi administracyjnej** tryb **automatyczne**. 

    ![aprowizowanie](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. W sekcji poświadczenia administratora wykonaj następujące czynności:
   
    a. W **nazwy wystąpienia usługi ServiceNow** tekstowym, wpisz nazwę wystąpienia usługi ServiceNow.

    b. W **nazwa użytkownika administratora usługi ServiceNow** tekstowym, wpisz nazwę użytkownika administratora.

    c. W **hasło administratora usługi ServiceNow** pole tekstowe, hasło administratora.

6. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, Twoje konto usługi ServiceNow ma uprawnienia administratora zespołu i spróbuj **"Poświadczeń administratora"** krok ponownie.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

8. Kliknij przycisk **zapisać.**

9. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom usługi ServiceNow.**

10. W **mapowań atrybutów** Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD do usługi ServiceNow. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w usługi ServiceNow dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD, świadczenie usługi dla usługi ServiceNow, zmień **stan inicjowania obsługi administracyjnej** do **na** w sekcji Ustawienia

12. Kliknij przycisk **zapisać.**

Rozpoczyna się wstępnej synchronizacji użytkowników i/lub grupy przypisane do usługi ServiceNow w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na aplikację usługi ServiceNow.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](active-directory-saas-servicenow-tutorial.md)


