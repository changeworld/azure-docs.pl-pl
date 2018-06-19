---
title: 'Samouczek: Konfigurowanie Jive dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: e8e486993ee45b04e922fc6a3849901c71405559
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941227"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Jive dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w Jive i Azure AD można automatycznie udostępnić i usuwanie kont użytkowników z usługi Azure AD do Jive.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Jive jednokrotnego włączone subskrypcji.
*   Konto użytkownika w Jive z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-jive"></a>Przypisywanie użytkowników do Jive

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD jest zsynchronizowany.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Jive. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do aplikacji Jive:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Ważne porady dotyczące przypisywania użytkowników do Jive

*   Zalecane jest pojedynczego użytkownika usługi Azure AD można przypisać do Jive do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do Jive, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w Jive inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w Jive w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla Jive, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest przedstawiają sposób włączania kont użytkowników usługi Active Directory do Jive Inicjowanie obsługi użytkowników.
W ramach tej procedury możesz są wymagane do potrzebnych do żądania od Jive.com token zabezpieczeń użytkownika.

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli Jive został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpieniem Jive przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Jive** w galerii aplikacji. Wybierz Jive w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu Jive, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/jive-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwa użytkownika administratora Jive** pole tekstowe, typ, nazwa, która ma konta Jive **Administrator systemu** profil Jive.com przypisane.
   
    b. W **hasło administratora Jive** tekstowym, wpisz hasło dla tego konta.
   
    c. W **adres URL dzierżawy Jive** tekstowym, wpisz adres URL dzierżawy Jive.
      
      > [!NOTE]
      > Adres URL dzierżawy Jive jest adres URL, który jest używany przez organizację do logowania do Jive.  
      > Zwykle adres URL ma następujący format: **www.\< Organizacja\>. jive.com**.          

6. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Jive.

7. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru poniżej.

8. Kliknij przycisk **zapisać.**

9. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom Jive.**

10. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Jive. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Jive dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD usługi dla Jive inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w sekcji Ustawienia

12. Kliknij przycisk **zapisać.**

Rozpoczyna się wstępnej synchronizacji użytkowników i/lub grupy przypisane do Jive w sekcji Użytkownicy i grupy. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Jive aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](jive-tutorial.md)