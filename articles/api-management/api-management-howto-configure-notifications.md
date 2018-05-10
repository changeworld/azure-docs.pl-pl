---
title: Konfigurowanie powiadomień i szablonów w usłudze Azure API Management wiadomości e-mail | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować powiadomienia i szablonów w usłudze Azure API Management wiadomości e-mail.
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
ms.openlocfilehash: 60788f76dac58ead10e43e892d587a86bdd3fcad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>How to configure notifications and email templates in Azure API Management
Zarządzanie interfejsami API pozwala, aby skonfigurować powiadomienia dla określonych zdarzeń i konfigurowania szablonów wiadomości e-mail, które są używane do komunikacji z Administratorzy i deweloperzy wystąpienia interfejsu API zarządzania. W tym artykule pokazano, jak skonfigurować powiadomienia o zdarzeniach dostępny i zawiera omówienie konfigurowania szablonów wiadomości e-mail używany dla tych zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Nie masz wystąpienie usługi API Management, należy wykonać następujące procedury szybkiego startu: [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="publisher-notifications"> </a>Konfigurowanie powiadomień

1. Wybierz użytkownika **zarządzanie interfejsami API** wystąpienia.
2. Kliknij przycisk **powiadomienia** do wyświetlania dostępnych powiadomień.

    ![Wydawcy powiadomień][api-management-publisher-notifications]

    Poniższa lista zdarzeń można skonfigurować dla powiadomienia.

    * **Żądania subskrypcji (wymaganie zatwierdzenia)** -adresatów wiadomości e-mail określonego i użytkowników będzie otrzymywał powiadomienia e-mail o żądaniach subskrypcji dla produktów interfejsu API, wymaganie zatwierdzenia.
    * **Nowe subskrypcje** -adresatów wiadomości e-mail określonego i użytkowników będzie otrzymywał powiadomienia e-mail o nowe subskrypcje produktu interfejsu API.
    * **Żądania galerii aplikacji** -adresatów wiadomości e-mail określonego i użytkowników będzie otrzymywał powiadomienia e-mail, gdy nowe aplikacje są przesyłane do galerii aplikacji.
    * **UDW** -adresatów wiadomości e-mail określonego i użytkownicy otrzymają wiadomość e-mail kopie wszystkich wiadomości e-mail wysyłanych do deweloperów.
    * **Nowy problem lub komentarz** — adresatów wiadomości e-mail określonego i użytkownicy będą otrzymywać powiadomienia e-mail, gdy nowy problem lub komentarz jest przesyłany w portalu dla deweloperów.
    * **Komunikat zamknięcia konta** -adresatów wiadomości e-mail określony i użytkownicy będą otrzymywać powiadomienia e-mail, po zamknięciu konta.
    * **Limit przydziału subskrypcji o** -następujących adresatów wiadomości e-mail i użytkowników będzie otrzymywał powiadomienia e-mail, gdy użycie subskrypcji pobiera możliwości wykorzystania przydziału użycia.

    Dla każdego zdarzenia można określić adresatów wiadomości e-mail w polu tekstowym adres e-mail lub użytkownicy mogą wybrać z listy.

3. Aby określić adresy e-mail, aby otrzymywać powiadomienia, wprowadź je w polu tekstowym adres e-mail. Jeśli masz wiele adresów e-mail, oddzielając je przecinkami.

    ![Adresatów powiadomień][api-management-email-addresses]
4. Kliknij przycisk **Dodaj**.

## <a name="email-templates"> </a>Konfigurowanie szablonów powiadomień
Zarządzanie interfejsami API udostępnia szablony powiadomień dla wiadomości e-mail, które są wysyłane w ramach zarządzania i korzystania z usługi. Znajdują się następujące szablony wiadomości e-mail.

* Zatwierdzone przesyłanie galerii aplikacji
* Litera farewell Developer
* Limit przydziału Developer zbliża się powiadomień
* Zaproś użytkownika
* Nowy komentarz dodany do problemu
* Nowy problem odebranych
* Nowa subskrypcja aktywowany
* Potwierdzenie odnowić subskrypcję
* Odrzuci to żądanie subskrypcji
* Odebrano żądanie subskrypcji

Te szablony mogą być modyfikowane pożądane.

Kliknij, aby wyświetlić i skonfigurować szablony wiadomości e-mail dla swojego wystąpienia usługi API Management **szablonów powiadomień**.

![Szablony wiadomości e-mail][api-management-email-templates]

Każdy szablon wiadomości e-mail ma podmiotu w postaci zwykłego tekstu i definicji treści w formacie HTML. Każdy element można dostosować pożądane.

![Edytor szablonu wiadomości e-mail][api-management-email-template]

**Parametry** lista zawiera listę parametrów, służący wstawione do tematu lub treści, zostanie zastąpiony wyznaczonej wartości, po wysłaniu wiadomości e-mail. Aby wstawić parametru, umieść kursor, który mają parametr, aby przejść, a następnie kliknij przycisk strzałki w lewo nazwę parametru.

> [!NOTE] 
> Parametry nie są zastępowane rzeczywistymi wartościami podczas przeglądania lub wysyłanie testu.

Aby zapisać zmiany szablonu wiadomości e-mail, kliknij polecenie **zapisać**, lub Anuluj zmiany kliknij **odrzucić**.
 

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
