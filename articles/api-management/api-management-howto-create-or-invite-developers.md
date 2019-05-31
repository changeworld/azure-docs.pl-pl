---
title: Jak zarządzać kontami użytkowników w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć lub zaprosić użytkowników w usłudze Azure API Management
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
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: d9cc5da0cd2936c1ac79a308366b8164548cb202
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241722"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Jak zarządzać kontami użytkowników w usłudze Azure API Management

W usłudze API Management deweloperzy mają użytkownicy interfejsów API, które należy udostępnić za pomocą usługi API Management. W tym przewodniku przedstawiono sposób utworzyć i zaprosić deweloperów do użycia interfejsów API i produktów, możesz udostępnić je za pomocą wystąpienia usługi API Management. Aby uzyskać informacji na temat programowego zarządzania kontami użytkowników, zobacz [jednostki użytkownika](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) w dokumentacji [interfejsu API REST zarządzania](/rest/api/apimanagement/) odwołania.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie zadań w tym artykule: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Tworzenie nowego dla deweloperów

Aby dodać nowego użytkownika, wykonaj kroki opisane w tej sekcji:

1. Wybierz **użytkowników** karty po lewej stronie ekranu.
2. Naciśnij klawisz **+ Dodaj**.
3. Wprowadź odpowiednie informacje dla użytkownika.
4. Kliknij przycisk **Dodaj**.

    ![Dodawanie nowego użytkownika](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Domyślnie są kontami deweloperów nowo utworzony **Active**i skojarzonych z tą **deweloperów** grupy. Konta dewelopera, które znajdują się w **active** stanu można uzyskać dostęp do wszystkich interfejsów API, do którego mają zostać subskrypcji. Aby skojarzyć nowo utworzoną dla deweloperów z dodatkowych grup, zobacz [jak skojarzyć grupy z deweloperami][How to associate groups with developers].

## <a name="invite-developer"> </a>Zaproś dewelopera
Aby zaprosić deweloperów, wykonaj kroki opisane w tej sekcji:

1. Wybierz **użytkowników** karty po lewej stronie ekranu.
2. Naciśnij klawisz **+ zaprosić**.

Zostanie wyświetlony komunikat potwierdzenia, ale nowo zaproszonego dla deweloperów nie są wyświetlane na liście do momentu po zaakceptowaniu zaproszenia. 

Gdy deweloper otrzymał zaproszenie, zostanie wysłana wiadomość e-mail do deweloperów. Ta wiadomość e-mail jest generowana z użyciem szablonu i można dostosowywać. Aby uzyskać więcej informacji, zobacz [szablony wiadomości e-mail Konfiguruj][Configure email templates].

Po zaakceptowaniu zaproszenia konto stanie się aktywny.

## <a name="block-developer"> </a> Zdezaktywować ani ponownie aktywować konta dewelopera

Domyślnie są kontami deweloperów nowo utworzone lub zaproszonego **Active**. Aby zdezaktywować konta dewelopera, kliknij przycisk **bloku**. Aby ponownie uaktywnić konto dewelopera zablokowanych, kliknij przycisk **Aktywuj**. Konto dewelopera zablokowanych nie można uzyskać dostęp do portalu dla deweloperów lub wywoływania żadnych interfejsów API. Aby usunąć konto użytkownika, kliknij przycisk **Usuń**.

Aby uniemożliwić użytkownikowi, wykonaj poniższe kroki.

1. Wybierz **użytkowników** karty po lewej stronie ekranu.
2. Kliknij użytkownika, który chcesz zablokować.
3. Naciśnij klawisz **bloku**.

## <a name="reset-a-user-password"></a>Resetowanie hasła użytkownika

Aby programowo pracować z kontami użytkowników, zobacz dokumentacja jednostki użytkownika w [interfejsu API REST zarządzania API](/rest/api/apimanagement/) odwołania. Aby zresetować hasło do konta użytkownika do określonej wartości, można użyć [zaktualizowania użytkownika](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) operację i określ odpowiednie hasło.

## <a name="next-steps"> </a>Następne kroki
Po utworzeniu konta dewelopera możesz skojarzyć go z rolami i subskrybowania produktów i interfejsów API. Aby uzyskać więcej informacji, zobacz [sposób tworzenia i używania grup][How to create and use groups].

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
