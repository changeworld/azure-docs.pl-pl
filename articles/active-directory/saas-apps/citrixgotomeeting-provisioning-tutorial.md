---
title: 'Samouczek: Konfigurowanie GoToMeeting dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e3145d0faaa3aecb90b582b3b6ef0063572ff43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60430787"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie GoToMeeting dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w GoToMeeting i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do GoToMeeting.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory.
*   GoToMeeting logowanie jednokrotne włączone subskrypcji.
*   Konto użytkownika w GoToMeeting z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-gotomeeting"></a>Przypisywanie użytkowników do GoToMeeting

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji GoToMeeting. Po decyzję, możesz przypisać użytkowników do aplikacji GoToMeeting, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Ważne wskazówki dotyczące przypisywania użytkowników do GoToMeeting

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do GoToMeeting do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do GoToMeeting, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczną Aprowizację użytkowników

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w GoToMeeting aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w GoToMeeting na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla GoToMeeting, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować, aprowizacja kont użytkowników:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano GoToMeeting dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi GoToMeeting przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **GoToMeeting** w galerii aplikacji. Wybierz GoToMeeting z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienie GoToMeeting, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **aprowizacji** tryb **automatyczne**. 

    ![Inicjowanie obsługi administracyjnej](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. W sekcji poświadczenia administratora wykonaj następujące czynności:
   
    a. W **nazwy użytkownika administratora GoToMeeting** polu tekstowym wpisz nazwę użytkownika administratora.

    b. W **hasło administratora GoToMeeting** pola tekstowego, hasło administratora.

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z aplikacji GoToMeeting. Jeśli połączenie nie powiedzie się, upewnij się, konto usługi GoToMeeting ma uprawnienia administratora zespołu i spróbuj **"Poświadczeń administratora"** krok ponownie.

1. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do GoToMeeting.**

1. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD GoToMeeting. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w GoToMeeting dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, inicjowania obsługi usługi GoToMeeting, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do GoToMeeting w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji GoToMeeting.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


