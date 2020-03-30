---
title: 'Samouczek: Konfigurowanie programu Netsuite OneWorld do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Netsuite OneWorld.
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
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063299"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie netsuite do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w programie Netsuite OneWorld i usłudze Azure AD, aby automatycznie aprowizować i usuwać z nich konta użytkowników z usługi Azure AD do Netsuite.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active directory.
*   Subskrypcja Netsuite OneWorld. Należy zauważyć, że automatyczne inicjowanie obsługi administracyjnej użytkowników jest obecnie obsługiwane tylko w u programu NetSuite OneWorld.
*   Konto użytkownika w netsuite z uprawnieniami administratora.

## <a name="assigning-users-to-netsuite-oneworld"></a>Przypisywanie użytkowników do programu Netsuite OneWorld

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Netsuite. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji Netsuite, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Ważne wskazówki dotyczące przypisywania użytkowników do netsuite OneWorld

*   Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Netsuite do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do netsuite należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włączanie inicjowania obsługi administracyjnej użytkowników

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika netsuite i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w programie Netsuite na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP] 
> Można również włączyć samooceny jednokrotne logowanie oparte na SAML dla netsuite, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować inicjowanie obsługi administracyjnej kont użytkowników:

Celem tej sekcji jest określenie sposobu włączania obsługi administracyjnej kont użytkowników usługi Active Directory do netsuite.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

1. Jeśli netsuite został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Netsuite za pomocą pola wyszukiwania. W przeciwnym razie wybierz **pozycję Dodaj** i wyszukaj **netsuite** w galerii aplikacji. Wybierz Netsuite z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie Netsuite, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**. 

    ![Inicjowania obsługi](./media/netsuite-provisioning-tutorial/provisioning.png)

1. W sekcji **Poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W polach tekstowych **Nazwa użytkownika administratora** wpisz nazwę konta Netsuite, do których przypisano profil **Administratora systemu** w Netsuite.com.
   
    b. W polach tekstowych **Hasło administratora** wpisz hasło dla tego konta.
      
1. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Netsuite.

1. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru.

1. Kliknij **pozycję Zapisz.**

1. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z netsuite.**

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Netsuite. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Netsuite do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla netsuite, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

1. Kliknij **pozycję Zapisz.**

Rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do Netsuite w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Netsuite.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](netsuite-tutorial.md)
