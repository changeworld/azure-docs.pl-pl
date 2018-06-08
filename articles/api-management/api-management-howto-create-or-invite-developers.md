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
ms.openlocfilehash: 53ed914a3890aab1dd49536856feeb8975986e00
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "33934812"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Jak zarządzać kontami użytkowników w usłudze Azure API Management
W usłudze API Management Deweloperzy są użytkownicy interfejsów API, które można udostępnić przy użyciu interfejsu API zarządzania. Ten przewodnik przedstawia sposobu tworzenia i zapraszanie deweloperów do korzystania z interfejsów API i produktów czy należy udostępnić je z wystąpieniem usługi API Management. Informacje na programowe zarządzanie kontami użytkowników, zobacz [jednostki użytkownika](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentacji w [interfejsu API REST zarządzania](https://msdn.microsoft.com/library/azure/dn776326.aspx) odwołania.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie zadań w tym artykule: [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Utwórz nowy developer

Aby dodać nowego użytkownika, wykonaj kroki opisane w tej sekcji:

1. Wybierz **użytkowników** kartę do lewego rogu ekranu.
2. Naciśnij klawisz **+ Dodaj**.
3. Wprowadź odpowiednie informacje dla użytkownika.
4. Kliknij przycisk **Dodaj**.

    ![Dodaj nowego użytkownika](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Domyślnie developer nowo utworzonego konta są **Active**wraz ze skojarzonymi z **deweloperzy** grupy. Konta dewelopera, które znajdują się w **active** stanu można uzyskać dostęp do wszystkich interfejsów API, do którego mają zostać subskrypcji. Aby skojarzyć developer nowo utworzony z dodatkowych grup, zobacz [sposobu kojarzenia grupy z deweloperami][How to associate groups with developers].

## <a name="invite-developer"> </a>Zaproś dewelopera
Aby zaprosić dewelopera, wykonaj kroki opisane w tej sekcji:

1. Wybierz **użytkowników** kartę do lewego rogu ekranu.
2. Naciśnij klawisz **+ zaprosić**.

Zostanie wyświetlony komunikat potwierdzenia, ale Deweloper nowo zaproszonych nie znajdują się na liście dopiero po ich zaakceptowaniu zaproszenia. 

Gdy Projektant jest zaproszenie, zostanie wysłana wiadomość e-mail do deweloperów. Ten adres e-mail jest generowana z użyciem szablonu i można dostosowywać. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szablonów wiadomości e-mail][Configure email templates].

Po zaakceptowaniu zaproszenia konto staje się aktywny.

## <a name="block-developer"> </a> Zdezaktywować ani ponownie aktywować konto dewelopera

Domyślnie są konta dewelopera nowo utworzone lub zaproszonych **Active**. Aby zdezaktywować konta dewelopera, kliknij przycisk **bloku**. Aby ponownie uaktywnić konto dewelopera zablokowanych, kliknij przycisk **Aktywuj**. Konto dewelopera zablokowanych nie można uzyskać dostępu do portalu dla deweloperów lub wywołaj wszystkie interfejsy API. Aby usunąć konto użytkownika, kliknij przycisk **usunąć**.

Aby zablokować użytkownika, wykonaj następujące kroki.

1. Wybierz **użytkowników** kartę do lewego rogu ekranu.
2. Kliknij użytkownika, dla którego chcesz zablokować.
3. Naciśnij klawisz **bloku**.

## <a name="reset-a-user-password"></a>Resetowanie hasła użytkownika

Aby programowo pracować z kontami użytkowników, zobacz [jednostki użytkownika](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentacji w [interfejsu API REST zarządzania](https://msdn.microsoft.com/library/azure/dn776326.aspx) odwołania. Aby zresetować hasło do konta użytkownika na określoną wartość, można użyć [zaktualizowania użytkownika](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) operację i określ odpowiednie hasło.

## <a name="next-steps"> </a>Następne kroki
Po utworzeniu konta dewelopera, można ją skojarzyć z rolami i subskrybowania produktów i interfejsów API. Aby uzyskać więcej informacji, zobacz [sposobu tworzenia i używania grup][How to create and use groups].

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
