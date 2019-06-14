---
title: 'Samouczek: Konfigurowanie usługi Jive dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 607d538a2a2636e17265e95195000a777f162dc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60263374"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Jive dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w Jive i Azure AD, aby automatycznie aprowizować i anulować obsługę kont użytkowników z usługi Azure AD do Jive.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   Jive logowania jednokrotnego włączonych subskrypcji.
*   Konto użytkownika w Jive z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-jive"></a>Przypisywanie użytkowników do usługi Jive

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi Jive. Po decyzję, możesz przypisać tych użytkowników z aplikacją usługi Jive, wykonując instrukcje podane w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Jive

*   Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do Jive do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do Jive, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włącz Aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika firmy Jive aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w Jive na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego do Jive, postępując zgodnie z instrukcjami dostarczone w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować, inicjowanie obsługi administracyjnej konta użytkownika:

Jest celem tej sekcji omówiono sposób włączania kont użytkowników usługi Active Directory do Jive Inicjowanie obsługi użytkowników.
W ramach tej procedury wymagane jest zapewnienie tokenu zabezpieczeń użytkownika, które musisz poprosić Jive.com.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano Jive dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Jive przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Jive** w galerii aplikacji. Wybierz Jive z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienie usługi Jive, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**. 

    ![Inicjowanie obsługi administracyjnej](./media/jive-provisioning-tutorial/provisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwy użytkownika administratora usługi Jive** pola tekstowego, typ, nazwa, która ma konta Jive **Administrator systemu** profilu w Jive.com przypisane.
   
    b. W **hasło administratora usługi Jive** pole tekstowe, wpisz hasło dla tego konta.
   
    c. W **adres URL usługi Jive dzierżawy** pole tekstowe, wpisz adres URL usługi Jive dzierżawy.
      
      > [!NOTE]
      > Adres URL dzierżawy usługi Jive jest adres URL używany do logowania do Jive przez Twoją organizację.  
      > Zazwyczaj adres URL ma następujący format: **www.\< Organizacja\>. jive.com**.          

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** zapewniające usługi Azure AD connect można Jive aplikacji.

1. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Jive.**

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Jive. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Jive operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, usługi dla usługi Jive inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do Jive w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji usługi Jive.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](jive-tutorial.md)
