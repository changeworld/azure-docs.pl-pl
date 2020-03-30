---
title: Konfigurowanie powiadomień i szablonów wiadomości e-mail
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować powiadomienia i szablony wiadomości e-mail w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244071"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>How to configure notifications and email templates in Azure API Management

Usługa API Management umożliwia konfigurowanie powiadomień dla określonych zdarzeń oraz konfigurowanie szablonów wiadomości e-mail używanych do komunikowania się z administratorami i deweloperami wystąpienia usługi API Management. W tym artykule pokazano, jak skonfigurować powiadomienia dla dostępnych zdarzeń i zawiera omówienie konfigurowania szablonów wiadomości e-mail używanych dla tych zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz wystąpienia usługi zarządzania interfejsami API, wykonaj następujące przewodniki Szybki start: [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Konfigurowanie powiadomień

1.  Wybierz **wystąpienie API MANAGEMENT.**
2.  Kliknij **pozycję Powiadomienia,** aby wyświetlić dostępne powiadomienia.

    ![Powiadomienia wydawcy][api-management-publisher-notifications]

    Poniższą listę zdarzeń można skonfigurować dla powiadomień.

    -   **Żądania subskrypcji (wymagające zatwierdzenia)** — określone adresaci wiadomości e-mail i użytkownicy będą otrzymywać powiadomienia e-mail o żądaniach subskrypcji produktów interfejsu API wymagających zatwierdzenia.
    -   **Nowe subskrypcje** — określone adresaci wiadomości e-mail i użytkownicy będą otrzymywać powiadomienia e-mail o nowych subskrypcjach produktów interfejsu API.
    -   **Żądania galerii aplikacji** — określone adresaci wiadomości e-mail i użytkownicy będą otrzymywać powiadomienia e-mail po przesłaniu nowych aplikacji do galerii aplikacji.
    -   **BCC** - Określone adresaci wiadomości e-mail i użytkownicy otrzymają e-mail blind kopie węgla wszystkich e-maili wysyłanych do programistów.
    -   **Nowy problem lub komentarz** — określone adresaci wiadomości e-mail i użytkownicy otrzymają powiadomienia e-mail, gdy nowy problem lub komentarz zostanie przesłany w portalu dla deweloperów.
    -   **Zamknij wiadomość o koncie** — określone adresaci wiadomości e-mail i użytkownicy będą otrzymywać powiadomienia e-mail po zamknięciu konta.
    -   **Zbliża się limit przydziału subskrypcji** — następujący adresaci wiadomości e-mail i użytkownicy będą otrzymywać powiadomienia e-mail, gdy użycie subskrypcji zbliży się do przydziału użycia.

        > [!NOTE]
        > Powiadomienia są wyzwalane przez przydział tylko według zasad [subskrypcji.](api-management-access-restriction-policies.md#SetUsageQuota) [Przydział według kluczowych](api-management-access-restriction-policies.md#SetUsageQuotaByKey) zasad nie generuje powiadomień.

    Dla każdego zdarzenia można określić adresatów wiadomości e-mail przy użyciu pola tekstowego adresu e-mail lub wybrać użytkowników z listy.

3.  Aby określić adresy e-mail, które mają być powiadamiane, wprowadź je w polu tekstowym adresu e-mail. Jeśli masz wiele adresów e-mail, oddziel je przecinkami.

    ![Odbiorcy powiadomień][api-management-email-addresses]

4.  Kliknij przycisk **Dodaj**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Konfigurowanie szablonów powiadomień

Usługa API Management udostępnia szablony powiadomień dla wiadomości e-mail wysyłanych w trakcie administrowania i korzystania z usługi. Dostępne są następujące szablony wiadomości e-mail.

-   Zatwierdzone nadsyłanie galerii wniosków
-   List pożegnalny dla deweloperów
-   Powiadomienie o zbliżającym się limicie przydziału dewelopera
-   Zaproś użytkownika
-   Nowy komentarz dodany do problemu
-   Otrzymano nowy problem
-   Aktywowano nową subskrypcję
-   Potwierdzenie odnowienia subskrypcji
-   Odrzucenie żądania subskrypcji
-   Odebrano żądanie subskrypcji

Szablony te można modyfikować zgodnie z potrzebami.

Aby wyświetlić i skonfigurować szablony wiadomości e-mail dla wystąpienia usługi API Management, kliknij pozycję **Szablony powiadomień**.

![Szablony wiadomości e-mail][api-management-email-templates]

Każdy szablon wiadomości e-mail ma temat w postaci zwykłego tekstu i definicję treści w formacie HTML. Każdy element można dostosować zgodnie z potrzebami.

![Edytor szablonów wiadomości e-mail][api-management-email-template]

Lista **Parametry** zawiera listę parametrów, które po wstawieniu do tematu lub treści zostaną zastąpione wyznaczoną wartością podczas wysyłania wiadomości e-mail. Aby wstawić parametr, umieść kursor w miejscu, w którym chcesz przejść parametr, a następnie kliknij strzałkę po lewej stronie nazwy parametru.

> [!NOTE]
> Parametry nie są zastępowane rzeczywistymi wartościami podczas wyświetlania podglądu lub wysyłania testu.

Aby zapisać zmiany w szablonie wiadomości e-mail, kliknij przycisk **Zapisz**lub aby anulować zmiany, kliknij przycisk **Odrzuć**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
