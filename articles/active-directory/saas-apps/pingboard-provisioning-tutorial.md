---
title: 'Samouczek: Konfigurowanie Pingboard dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Pingboard.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f7e2fabc86374f7fe055303d056ae8e00f33389
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964368"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Pingboard dla automatycznej aprowizacji użytkowników

Ten samouczek ma na celu dowiesz się, kroki, które należy wykonać, aby włączyć automatyczne aprowizację i cofanie aprowizacji kont użytkowników z usługi Azure Active Directory (Azure AD) do Pingboard.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawy Pingboard [konto Pro](https://pingboard.com/pricing)
* Konto użytkownika Pingboard z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji z usługi Azure AD opiera się na [Pingboard API](https://pingboard.docs.apiary.io/#), który jest dostępny dla Twojego konta.

## <a name="assign-users-to-pingboard"></a>Przypisywanie użytkowników do Pingboard

Usługa Azure AD używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranej aplikacji. W kontekście aprowizacja kont użytkowników tylko do użytkowników, które są przypisane do aplikacji w usłudze Azure AD są synchronizowane. 

Przed skonfigurowaniem i włączyć usługę aprowizacji, należy zdecydować, użytkowników, którzy w usłudze Azure AD muszą mieć dostęp do aplikacji Pingboard. Następnie można przypisać tych użytkowników do aplikacji Pingboard zgodnie z instrukcjami w tym miejscu:

[Przypisz użytkownika do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Pingboard

Firma Microsoft zaleca, aby przypisać jeden Pingboard do testowania konfiguracji aprowizacji użytkownika usługi Azure AD. Później można przypisać dodatkowych użytkowników.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurowanie aprowizacji użytkownika Pingboard 

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika Pingboard aprowizujący interfejs API. Możesz również skonfigurować usługę aprowizacji, aby tworzyć, aktualizować i wyłączanie kont użytkowników przypisane w Pingboard, które są oparte na przypisania użytkownika w usłudze Azure AD.

> [!TIP]
> Aby włączyć opartej na SAML logowania jednokrotnego dla Pingboard, postępuj zgodnie z instrukcjami podanymi w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do Pingboard w usłudze Azure AD

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano Pingboard dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Pingboard przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Pingboard** w galerii aplikacji. Wybierz **Pingboard** z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie Pingboard, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Pingboard inicjowania obsługi administracyjnej.](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, wykonaj następujące czynności:

    a. W **adres URL dzierżawy**, wprowadź `https://your_domain.pingboard.com/scim/v2`i zastąp "format domena_użytkownika" rzeczywistych domeny.

    b. Zaloguj się do [Pingboard](https://pingboard.com/) przy użyciu konta administratora.

    c. Wybierz **dodatki** > **integracje** > **usługi Azure Active Directory**.

    d. Przejdź do **Konfiguruj** , a następnie wybierz pozycję **Włącz aprowizacji użytkowników z usługi Azure**.

    e. Skopiuj token w **tokenu elementu nośnego OAuth**, a następnie wprowadź go w **klucz tajny tokenu**.

1. W witrynie Azure portal wybierz **Testuj połączenie** aby testowanie usługi Azure AD można połączyć się z aplikacją Pingboard. Jeśli połączenie nie powiedzie się, testowanie, że Twoje konto Pingboard ma uprawnienia administratora i spróbuj **Testuj połączenie** krok ponownie.

1. Wprowadź adres e-mail osoby lub grupy, które chcesz otrzymywać inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

1. Wybierz pozycję **Zapisz**.

1. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Pingboard**.

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do Pingboard. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Pingboard operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany. Aby uzyskać więcej informacji, zobacz [aprowizacji mapowania atrybutów użytkowników Dostosuj](../manage-apps/customize-application-attributes.md).

1. Aby włączyć usługi Azure AD, usługi dla Pingboard, inicjowania obsługi administracyjnej w **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

1. Wybierz **Zapisz** można rozpocząć synchronizacji początkowej z użytkowników przypisanych do Pingboard.

Początkowa synchronizacja trwa dłużej niż następujące synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Użyj **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności. Dzienniki opisano wszystkie akcje podejmowane przez usługę aprowizacji w aplikacji Pingboard.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [sporządzić raport na temat Inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](pingboard-tutorial.md)
