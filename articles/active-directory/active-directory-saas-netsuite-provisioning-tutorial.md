---
title: 'Samouczek: Konfigurowanie Netsuite dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 18c6c632131a7e83d4cf1587282192345d8c72f2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341451"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Netsuite dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest opisano czynności, które należy wykonać w Netsuite i Azure AD, aby automatycznie zapewnianie i usuwanie kont użytkowników z usługi Azure AD Netsuite.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Netsuite jednokrotnego włączone subskrypcji.
*   Konto użytkownika w Netsuite z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-netsuite"></a>Przypisywanie użytkowników do Netsuite

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatyczne Inicjowanie obsługi konta tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD są synchronizowane.

Przed Skonfiguruj i włącz usługę inicjowania obsługi administracyjnej, należy zdecydować, jakie użytkownicy i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Netsuite. Po decyzję, postępując zgodnie z instrukcjami w tym miejscu można przypisać tych użytkowników do aplikacji Netsuite:

[Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Ważne porady dotyczące przypisywania użytkowników do Netsuite

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD Netsuite do testowania konfiguracji inicjowania obsługi administracyjnej. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Przypisanie użytkownika do Netsuite, musisz wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez łączenie usługi Azure AD z konta użytkownika w Netsuite inicjowania obsługi interfejsu API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania konta użytkowników przypisane w Netsuite w oparciu o przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP] 
> Można też włączyć na języku SAML logowania jednokrotnego dla Netsuite, wykonując instrukcje podane w [portalu Azure](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest przedstawiają sposób włączania kont użytkowników usługi Active Directory do Netsuite Inicjowanie obsługi użytkowników.

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje przedsiębiorstwa > wszystkie aplikacje** sekcji.

2. Jeśli Netsuite został już skonfigurowany dla logowania jednokrotnego, wyszukiwanie wystąpieniem Netsuite przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Netsuite** w galerii aplikacji. Wybierz Netsuite w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienia programu Netsuite, a następnie wybierz **inicjowania obsługi administracyjnej** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![aprowizowanie](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwa użytkownika administratora** pole tekstowe, typ, nazwa, która ma konta Netsuite **Administrator systemu** profil Netsuite.com przypisane.
   
    b. W **hasło administratora** tekstowym, wpisz hasło dla tego konta.
      
6. W portalu Azure kliknij **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z aplikacji Netsuite.

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru.

8. Kliknij przycisk **zapisać.**

9. W sekcji mapowania wybierz **synchronizacji Azure Active Directory użytkownikom Netsuite.**

10. W **mapowań atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Netsuite. Należy pamiętać, że atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Netsuite dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługi Azure AD usługi dla Netsuite inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w sekcji Ustawienia

12. Kliknij przycisk **zapisać.**

Rozpoczyna się wstępnej synchronizacji użytkowników i/lub grupy przypisane do Netsuite w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacji początkowej dłużej, aby wykonać niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak usługa jest uruchomiona. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej Dzienniki aktywności, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej na Netsuite aplikacji.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie rejestracji jednokrotnej](active-directory-saas-netsuite-tutorial.md)