---
title: Konfigurowanie powiadomień i szablonów w usłudze Azure API Management wiadomości e-mail | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Konfigurowanie powiadomień i szablonów w usłudze Azure API Management wiadomości e-mail.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 2a959c9d131c6aa0bdc99450cf2b6f09a5d8bfa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60528382"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>How to configure notifications and email templates in Azure API Management
API Management udostępnia możliwość skonfigurowania powiadomień dla określonych zdarzeń i skonfigurować szablony wiadomości e-mail, które są używane do komunikacji z administratorów i deweloperów wystąpienia usługi API Management. W tym artykule pokazano, jak skonfigurować powiadomienia o zdarzeniach dostępny i zawiera omówienie konfigurowania szablonów wiadomości e-mail używany dla tych zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz wystąpienie usługi API Management, wykonaj następujące procedury szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Konfigurowanie powiadomień

1. Wybierz swoje **usługi API MANAGEMENT** wystąpienia.
2. Kliknij przycisk **powiadomienia** do wyświetlania dostępnych powiadomień.

    ![Wydawcy powiadomień][api-management-publisher-notifications]

    Poniższa lista zdarzeń można skonfigurować dla powiadomień.

   * **Żądania subskrypcji (wymagające zatwierdzenia)** -podany adres e-mail odbiorcy i użytkownicy będą otrzymywać powiadomienia e-mail dotyczące żądań subskrypcji dla produktów interfejsu API, wymaganie zatwierdzenia.
   * **Nowe subskrypcje** -podany adres e-mail odbiorcy i użytkownicy będą otrzymywać powiadomienia e-mail o nowych subskrypcjach produktu interfejsu API.
   * **Żądania w galerii aplikacji** -podany adres e-mail odbiorcy oraz użytkowników będzie otrzymywał powiadomienia e-mail, gdy nowe aplikacje są przesyłane do galerii aplikacji.
   * **UDW** -podany adres e-mail odbiorcy i użytkownicy będą otrzymywać wiadomości e-mail kopie wszystkich wiadomości e-mail wysyłane do deweloperów.
   * **Nowy problem lub komentarz** — podany adres e-mail odbiorcy i użytkownicy będą otrzymywać powiadomienia e-mail, gdy nowy problem lub komentarz jest przesyłany w portalu dla deweloperów.
   * **Zamknij konto komunikat** -podany adres e-mail odbiorcy oraz użytkowników będzie otrzymywał powiadomienia e-mail, gdy konto jest zamknięty.
   * **Limit przydziału bliskim** -następujących adresatów wiadomości e-mail i użytkowników będzie otrzymywał powiadomienia e-mail, gdy użycie subskrypcji zbliży limit przydziału użycia.

     Dla każdego zdarzenia można określić adresatów wiadomości e-mail w polu tekstowym adresu e-mail lub użytkownicy mogą wybrać z listy.

3. Aby określić adresy e-mail, aby otrzymywać powiadomienia, należy wprowadzić je w polu tekstowym adresu e-mail. Jeśli masz wiele adresów e-mail, oddziel je przecinkami.

    ![Adresatów powiadomień][api-management-email-addresses]
4. Kliknij przycisk **Dodaj**.

## <a name="email-templates"> </a>Konfigurowanie szablonów powiadomień
API Management udostępnia szablony powiadomień dla wiadomości e-mail, które są wysyłane w ramach zarządzania i korzystania z usługi. Znajdują się następujące szablony wiadomości e-mail.

* Zatwierdzone przesłanie galerii aplikacji
* Litera farewell dla deweloperów
* Limit przydziału Deweloper zbliża się do powiadomień
* Zaproś użytkownika
* Nowy komentarz dodany do problemu
* Nowy problem odebrane
* Nowa subskrypcja aktywowany
* Potwierdzenie odnowienia subskrypcji
* Odrzuci żądanie subskrypcji
* Odebrano żądanie subskrypcji

Szablony te można modyfikować zgodnie z potrzebami.

Kliknij, aby wyświetlić i skonfigurować szablony wiadomości e-mail dla swojego wystąpienia usługi API Management **szablonów powiadomień**.

![Szablony wiadomości e-mail][api-management-email-templates]

Każdy szablon wiadomości e-mail ma podmiotu w postaci zwykłego tekstu i definicji treści w formacie HTML. Każdy element, który można dostosować zgodnie z potrzebami.

![Edytor szablonów wiadomości e-mail][api-management-email-template]

**Parametry** lista zawiera listę parametrów, który, gdy wstawiony temat lub treść, zostanie zastąpiony wyznaczonej wartości, po wysłaniu wiadomości e-mail. Aby wstawić parametru, umieść kursor, w którym chcesz parametru, aby przejść, a następnie kliknij strzałkę na lewo od nazwy parametru.

> [!NOTE] 
> Parametry nie są zastępowane wartości rzeczywiste do przeglądania lub wysyłania testu.

Aby zapisać zmiany do szablonu wiadomości e-mail, kliknij przycisk **Zapisz**, lub do anulowania kliknij zmiany **odrzucić**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
