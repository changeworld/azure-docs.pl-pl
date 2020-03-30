---
title: Jak zarządzać kontami użytkowników w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć lub zapraszać użytkowników w usłudze Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072466"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Zarządzanie kontami użytkowników w usłudze Azure API Management

W usłudze API Management deweloperzy są użytkownikami interfejsów API udostępnianych przy użyciu usługi API Management. W tym przewodniku pokazano, jak tworzyć i zapraszać deweloperów do korzystania z interfejsów API i produktów, które udostępniasz im za pomocą wystąpienia usługi API Management. Aby uzyskać informacje na temat zarządzania kontami użytkowników programowo, zobacz dokumentację [jednostki użytkownika](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) w odwołaniu [REST zarządzania interfejsem API.](/rest/api/apimanagement/)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj zadania w tym artykule: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Tworzenie nowego dewelopera

Aby dodać nowego użytkownika, wykonaj czynności opisane w tej sekcji:

1. Wybierz kartę **Użytkownicy** po lewej stronie ekranu.
2. Naciśnij **+Dodaj**.
3. Wprowadź odpowiednie informacje dla użytkownika.
4. Kliknij przycisk **Dodaj**.

    ![Dodawanie nowego użytkownika](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Domyślnie nowo utworzone konta deweloperów są **aktywne**i skojarzone z grupą **Deweloperzy.** Konta deweloperów, które są w stanie **aktywnym** może służyć do dostępu do wszystkich interfejsów API, dla których mają subskrypcje. Aby skojarzyć nowo utworzonego dewelopera z dodatkowymi grupami, zobacz [Jak skojarzyć grupy z deweloperami][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Zaproś dewelopera
Aby zaprosić dewelopera, wykonaj czynności opisane w tej sekcji:

1. Wybierz kartę **Użytkownicy** po lewej stronie ekranu.
2. Naciśnij **+Zaproś**.

Zostanie wyświetlony komunikat potwierdzający, ale nowo zaproszony deweloper nie pojawi się na liście, dopóki nie zaakceptuje zaproszenia. 

Gdy deweloper zostanie zaproszony, do dewelopera zostanie wysłana wiadomość e-mail. Ten e-mail jest generowany przy użyciu szablonu i można go dostosować. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szablonów wiadomości e-mail][Configure email templates].

Po zaakceptowaniu zaproszenia konto staje się aktywne.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Dezaktywowanie lub ponowne aktywowanie konta dewelopera

Domyślnie nowo utworzone lub zaproszone konta deweloperów są **aktywne**. Aby dezaktywować konto dewelopera, kliknij przycisk **Zablokuj**. Aby ponownie aktywować zablokowane konto dewelopera, kliknij pozycję **Aktywuj**. Zablokowane konto dewelopera nie może uzyskać dostępu do portalu dewelopera ani wywoływać żadnych interfejsów API. Aby usunąć konto użytkownika, kliknij przycisk **Usuń**.

Aby zablokować użytkownika, wykonaj następujące kroki.

1. Wybierz kartę **Użytkownicy** po lewej stronie ekranu.
2. Kliknij użytkownika, którego chcesz zablokować.
3. Naciśnij **klawisz Block**.

## <a name="reset-a-user-password"></a>Resetowanie hasła użytkownika

Aby programowo pracować z kontami użytkowników, zobacz dokumentację jednostki użytkownika w odwołaniu interfejsu [API REST zarządzania interfejsem API](/rest/api/apimanagement/) interfejsu API. Aby zresetować hasło konta użytkownika do określonej wartości, można użyć operacji [Aktualizuj użytkownika](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) i określić żądane hasło.

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki
Po utworzeniu konta dewelopera można skojarzyć je z rolami i subskrybować je do produktów i interfejsów API. Aby uzyskać więcej informacji, zobacz [Jak tworzyć i używać grup][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
