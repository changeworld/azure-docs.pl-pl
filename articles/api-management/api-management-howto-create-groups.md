---
title: Zarządzanie kontami deweloperów przy użyciu grup w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie kontami deweloperów przy użyciu grup w usłudze Azure API Management
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
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658441"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Jak utworzyć i używanie grup do zarządzania kontami deweloperów w usłudze Azure API Management

W usłudze API Management grupy służą do zarządzania widocznością produktów dla deweloperów. Produkty najpierw są one widoczne dla grup, a następnie deweloperzy w tych grupach można wyświetlać i subskrybować produkty, które są skojarzone z grupami. 

Usługa API Management ma następujące niezmienne grupy systemowe:

* **Administratorzy** — do tej grupy należą administratorzy subskrypcji platformy Azure. Administratorzy zarządzają wystąpieniami usługi API Management, tworząc interfejsy API, operacje i produkty, które są używane przez deweloperów.
* **Deweloperzy** — do tej grupy należą uwierzytelnieni użytkownicy portalu dla deweloperów. Deweloperzy to klienci, którzy tworzą aplikacje przy użyciu interfejsów API. Deweloperzy otrzymują dostęp do portalu dla deweloperów i tworzą aplikacje, które wywołują operacje interfejsów API.
* **Goście** — do tej grupy należą nieuwierzytelnieni użytkownicy portalu dla deweloperów, tacy jak potencjalni klienci odwiedzający portal dla deweloperów wystąpienia usługi API Management. Mogą mieć przyznany pewien zakres dostępu tylko do odczytu, czyli np. możliwość wyświetlania interfejsów API, ale nie ich wywoływania.

Oprócz tych grup systemowych Administratorzy mogą tworzyć niestandardowe grupy lub [wykorzystać zewnętrzne grupy w skojarzonych dzierżawach usługi Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Niestandardowe i zewnętrzne grupy mogą być używane razem z grupami systemowymi, zapewniając deweloperom widoczność produktów interfejsu API i dostęp do nich. Można na przykład utworzyć jedną grupę niestandardową dla deweloperów powiązanych z konkretną organizacją partnera i zezwolić im na dostęp do interfejsów API z produktów zawierających tylko odpowiednie interfejsy API. Użytkownik może należeć do więcej niż jednej grupy.

W tym przewodniku przedstawiono, jak dodać nowe grupy Administratorzy wystąpienia usługi API Management i skojarzyć je z produktami i deweloperów.

Oprócz tworzenia grup i zarządzanie nimi w portalu wydawcy, można tworzyć i zarządzać grupami przy użyciu interfejsu API REST zarządzania API [grupy](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) jednostki.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie zadań w tym artykule: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Utwórz grupę

W tej sekcji pokazano, jak dodać nową grupę do swojego konta usługi API Management.

1. Wybierz **grup** karty po lewej stronie ekranu.
2. Kliknij przycisk **+ Dodaj**.
3. Wprowadź unikatową nazwę grupy i opcjonalny opis.
4. Kliknij przycisk **Utwórz**.

    ![Dodaj nową grupę](./media/api-management-howto-create-groups/groups001.png)

Po utworzeniu grupy zostanie dodany do **grup** listy. <br/>Aby edytować **nazwa** lub **opis** grupy, kliknij nazwę grupy i **ustawienia**.<br/>Aby usunąć grupę, kliknij nazwę grupy i naciśnij klawisz **Usuń**.

Teraz, gdy taka grupa zostanie utworzona, może być skojarzony z produktami i deweloperów.

## <a name="associate-group-product"> </a>Grupa jest kojarzona z produktem

1. Wybierz **produktów** karty po lewej stronie.
2. Kliknij nazwę żądany produkt.
3. Naciśnij klawisz **kontroli dostępu**.
4. Kliknij przycisk **+ Dodaj grupę**.

    ![Grupa jest kojarzona z produktem](./media/api-management-howto-create-groups/groups002.png)
5. Wybierz grupę, którą chcesz dodać.

    ![Grupa jest kojarzona z produktem](./media/api-management-howto-create-groups/groups003.png)

    Aby usunąć grupę z produktu, kliknij przycisk **Usuń**.

    ![Usuwanie grupy](./media/api-management-howto-create-groups/groups004.png)

Gdy produkt jest skojarzony z grupą, deweloperzy w tej grupie można wyświetlać i subskrybować produkt.

> [!NOTE]
> Aby dodać grupy usługi Azure Active Directory, zobacz [jak autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory w usłudze Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Skojarzyć grupy z deweloperami

W tej sekcji pokazano, jak skojarzyć grupy z elementów członkowskich.

1. Wybierz **grup** karty po lewej stronie ekranu.
2. Wybierz **członków**.

    ![Dodaj członka](./media/api-management-howto-create-groups/groups005.png)
3. Naciśnij klawisz **+ Dodaj** i wybierz składnik.

    ![Dodaj członka](./media/api-management-howto-create-groups/groups006.png)
4. Naciśnij klawisz **wybierz**.

Po dodaniu skojarzenia między warstwą Deweloper a grupy można wyświetlić je w **użytkowników** kartę.

## <a name="next-steps"> </a>Następne kroki

* Po dodaniu dewelopera do grupy mogą wyświetlać i subskrybować produkty, skojarzone z tą grupą. Aby uzyskać więcej informacji, zobacz [jak tworzyć i publikować produkt w usłudze Azure API Management][How create and publish a product in Azure API Management],
* Oprócz tworzenia grup i zarządzanie nimi w portalu wydawcy, można tworzyć i zarządzać grupami przy użyciu interfejsu API REST zarządzania API [grupy](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) jednostki.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
