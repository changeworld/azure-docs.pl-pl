---
title: 'Samouczek: Konfigurowanie usługi GoToMeeting pod kątem automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
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
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058267"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie gotomeeting do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w GoToMeeting i usługi Azure AD, aby automatycznie aprowizować i de-provision kont użytkowników z usługi Azure AD do GoToMeeting.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active directory.
*   Subskrypcja z włączoną funkcją logowania jednokrotnego GoToMeeting.
*   Konto użytkownika w witrynie GoToMeeting z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-gotomeeting"></a>Przypisywanie użytkowników do gotomeeting

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji GoToMeeting. Po podjęciu decyzji można przypisać tych użytkowników do aplikacji GoToMeeting, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Ważne wskazówki dotyczące przypisywania użytkowników do GoToMeeting

*   Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do GoToMeeting do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do GoToMeeting, należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne inicjowanie obsługi administracyjnej użytkowników

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API obsługi administracyjnej konta użytkownika usługi GoToMeeting i skonfigurowaniu usługi inicjowania obsługi administracyjnej w celu utworzenia, zaktualizowania i wyłączenia przypisanych kont użytkowników w usłudze GoToMeeting na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samoograniczenie jednokrotne oparte na SAML dla GoToMeeting, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

1. Jeśli program GoToMeeting został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie GoToMeeting przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **gotomeeting** w galerii aplikacji. Wybierz goToMeeting z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie GoToMeeting, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw tryb **inicjowania obsługi administracyjnej** na **Automatyczny**. 

    ![Inicjowania obsługi](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. W sekcji Poświadczenia administratora wykonaj następujące czynności:
   
    a. W pole **tekstowym GoToMeeting Admin User Name** wpisz nazwę użytkownika administratora.

    b. W polach tekstowych **Hasło administratora GoToMeeting** hasło administratora.

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją GoToMeeting. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto GoToMeeting ma uprawnienia administratora zespołu i spróbuj ponownie wykonać krok **"Poświadczenia administratora".**

1. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail powiadomień,** i zaznacz pole wyboru.

1. Kliknij **pozycję Zapisz.**

1. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem GoToMeeting.**

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do GoToMeeting. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w GoToMeeting dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi GoToMeeting, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

1. Kliknij **pozycję Zapisz.**

Rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do GoToMeeting w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji GoToMeeting.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


