---
title: 'Samouczek: Konfigurowanie Asana dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w usłudze Asana.
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
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10d5ceedfd1f0fad0c6f03feba2150968aecf02b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964227"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Asana dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w usłudze Asana i usługi Azure Active Directory (Azure AD) do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do Asana.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawa usługi Asana z [Enterprise](https://www.asana.com/pricing) plan lub lepiej jest włączona
* Konto użytkownika w usłudze Asana z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji z usługi Azure AD opiera się na [interfejsu API w usłudze Asana](https://asana.com/developers/api-reference/users), który jest dostępny dla Asana.

## <a name="assign-users-to-asana"></a>Przypisywanie użytkowników do Asana

Usługa Azure AD używa koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście aprowizacja kont użytkowników tylko do użytkowników, które są przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączyć usługę aprowizacji, należy zdecydować, użytkowników, którzy w usłudze Azure AD muszą mieć dostęp do aplikacji Asana. Następnie można przypisać tych użytkowników do aplikacji Asana, zgodnie z instrukcjami w tym miejscu:

[Przypisz użytkownika do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Asana

Firma Microsoft zaleca, aby przypisać jeden Asana, aby przetestować konfigurację aprowizacji użytkownika usługi Azure AD. Później można przypisać dodatkowych użytkowników.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurowanie aprowizacji użytkownika Asana

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika Asana aprowizujący interfejs API. Możesz również skonfigurować inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączania kont przypisanych użytkowników w usłudze Asana, w oparciu o przypisania użytkownika w usłudze Azure AD.

> [!TIP]
> Aby włączyć opartej na SAML logowania jednokrotnego dla Asana, postępuj zgodnie z instrukcjami podanymi w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do Asana w usłudze Azure AD

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano Asana dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Asana, przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Asana** w galerii aplikacji. Wybierz **Asana** z wyników wyszukiwania i dodaj go do listy aplikacji.

1. Wybierz wystąpienie usługi w usłudze Asana, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej w usłudze Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, wykonaj te instrukcje, aby wygenerować token, a następnie wprowadź go w **klucz tajny tokenu**:

    a. Zaloguj się do [Asana](https://app.asana.com) przy użyciu konta administratora.

    b. Wybierz zdjęcie profilowe z paskiem górnym, a następnie wybierz bieżące ustawienia nazwę organizacji.

    c. Przejdź do **kont usług** kartę.

    d. Wybierz **Dodaj konto usługi**.

    e. Aktualizacja **nazwa** i **o** i zdjęcie profilowe, zgodnie z potrzebami. Skopiuj token w **tokenu**, a następnie wybierz ją w **Zapisz zmiany**.

1. W witrynie Azure portal wybierz **Testuj połączenie** do upewnij się, że połączenie usługi Azure AD do swojej aplikacji Asana. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto w usłudze Asana ma uprawnienia administratora i spróbuj **Testuj połączenie** krok ponownie.

1. Wprowadź adres e-mail osoby lub grupy, które chcesz otrzymywać inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

1. Wybierz pozycję **Zapisz**.

1. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Asana**.

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do Asana. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Asana dla operacji aktualizacji. Wybierz **Zapisz** aby zatwierdzić zmiany. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkownika](../manage-apps/customize-application-attributes.md).

1. Aby włączyć usługi Azure AD w inicjowania obsługi usługi Asana, **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

1. Wybierz pozycję **Zapisz**.

Po rozpoczęciu synchronizacji początkowej dla wszystkich użytkowników przypisanych do Asana w **użytkowników** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Użyj **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności. Dzienniki inspekcji opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Asana.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [sporządzić raport na temat Inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](asana-tutorial.md)
