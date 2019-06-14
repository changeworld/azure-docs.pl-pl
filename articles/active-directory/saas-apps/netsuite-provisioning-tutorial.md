---
title: 'Samouczek: Konfigurowanie Netsuite OneWorld dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 928070ae7e5c9077c6f77e8cb7beb36815f47d6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515841"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Netsuite do automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w Netsuite OneWorld i Azure AD, do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do Netsuite.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Subskrypcja Netsuite OneWorld. Należy pamiętać, że automatyczna aprowizacja użytkowników jest obecnie obsługiwane tylko w przypadku NetSuite OneWorld.
*   Konto użytkownika w Netsuite z uprawnieniami administratora.

## <a name="assigning-users-to-netsuite-oneworld"></a>Przypisywanie użytkowników do Netsuite OneWorld

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Netsuite. Po decyzję, możesz przypisać użytkowników do aplikacji Netsuite, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Netsuite OneWorld

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Netsuite do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do Netsuite, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz Aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w Netsuite aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w Netsuite na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP] 
> Można też włączyć opartej na SAML logowania jednokrotnego dla Netsuite, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi administracyjnej konta użytkownika:

Jest celem tej sekcji omówiono sposób włączania kont użytkowników usługi Active Directory do Netsuite Inicjowanie obsługi użytkowników.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano Netsuite dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Netsuite przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Netsuite** w galerii aplikacji. Wybierz Netsuite z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienie Netsuite, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![Inicjowanie obsługi administracyjnej](./media/netsuite-provisioning-tutorial/provisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwy użytkownika administratora** pola tekstowego, typ, nazwa, która ma konta Netsuite **Administrator systemu** profilu w Netsuite.com przypisane.
   
    b. W **hasło administratora** pole tekstowe, wpisz hasło dla tego konta.
      
1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD connect można Netsuite aplikacji.

1. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia o błędach aprowizacji i zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Netsuite.**

1. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD Netsuite. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Netsuite operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, usługi dla Netsuite inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do Netsuite w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Netsuite.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](netsuite-tutorial.md)
