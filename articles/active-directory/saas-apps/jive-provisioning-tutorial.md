---
title: 'Samouczek: Konfigurowanie Jive do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Jive.
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
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057738"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Jive do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w Jive i usługi Azure AD, aby automatycznie aprowizować i de-provision kont użytkowników z usługi Azure AD do Jive.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active directory.
*   Jive jednokrotny znak na włączonej subskrypcji.
*   Konto użytkownika w Jive z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-jive"></a>Przypisywanie użytkowników do Jive

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Jive. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji Jive, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Jive

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Jive do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

*   Podczas przypisywania użytkownika do Jive należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-user-provisioning"></a>Włączanie inicjowania obsługi administracyjnej użytkowników

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta Jive i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w jive na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć samoosticie oparte na SAML logowania jednokrotnego dla Jive, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować inicjowanie obsługi administracyjnej kont użytkowników:

Celem tej sekcji jest określenie sposobu włączania obsługi administracyjnej kont użytkowników usługi Active Directory do Jive.
W ramach tej procedury są wymagane, aby zapewnić token zabezpieczający użytkownika, które należy zażądać od Jive.com.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

1. Jeśli jive został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Jive'a za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **Jive** w galerii aplikacji. Wybierz Jive z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie Jive, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

1. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**. 

    ![Inicjowania obsługi](./media/jive-provisioning-tutorial/provisioning.png)

1. W sekcji **Poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W pole **tekstowym Nazwa użytkownika administratora Jive** wpisz nazwę konta Jive, do których przypisano profil **Administratora systemu** w Jive.com.
   
    b. W polach **tekstowych "Hasło administratora Jive"** wpisz hasło dla tego konta.
   
    d. W polach **tekstowych adresu URL dzierżawy Jive** wpisz adres URL dzierżawy Jive.
      
      > [!NOTE]
      > Adres URL dzierżawy Jive to adres URL używany przez organizację do logowania się do Jive.  
      > Zazwyczaj adres URL ma następujący format: **www.\< organizacji\>.jive.com**.          

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Jive.

1. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

1. Kliknij **pozycję Zapisz.**

1. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z Jive.Under** the Mappings section, select Synchronize Azure Active Directory Users to Jive.

1. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Jive. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Jive dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

1. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla języka Jive, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

1. Kliknij **pozycję Zapisz.**

Rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do Jive w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Jive.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](jive-tutorial.md)
