---
title: Zarządzanie kontami dewelopera przy użyciu grup w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać kontami dewelopera przy użyciu grup w usłudze Azure API Management
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
ms.openlocfilehash: 3986b07c3568c3dcbb4077361d38f74d658458cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151063"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Tworzenie i używanie grup do zarządzania konta dewelopera usługi Azure API Management
W usłudze API Management grupy służą do zarządzania widocznością produktów dla deweloperów. Produkty są najpierw stają się widoczne dla grup, a następnie deweloperów w tych grupach można wyświetlać i subskrybować produktów, które są skojarzone z grupami. 

Usługa API Management ma następujące niezmienne grupy systemowe:

* **Administratorzy** — do tej grupy należą administratorzy subskrypcji platformy Azure. Administratorzy zarządzają wystąpieniami usługi API Management, tworząc interfejsy API, operacje i produkty, które są używane przez deweloperów.
* **Deweloperzy** — do tej grupy należą uwierzytelnieni użytkownicy portalu dla deweloperów. Deweloperzy to klienci, którzy tworzą aplikacje przy użyciu interfejsów API. Deweloperzy otrzymują dostęp do portalu dla deweloperów i tworzą aplikacje, które wywołują operacje interfejsów API.
* **Goście** — do tej grupy należą nieuwierzytelnieni użytkownicy portalu dla deweloperów, tacy jak potencjalni klienci odwiedzający portal dla deweloperów wystąpienia usługi API Management. Mogą mieć przyznany pewien zakres dostępu tylko do odczytu, czyli np. możliwość wyświetlania interfejsów API, ale nie ich wywoływania.

Oprócz tych grup systemu, Administratorzy mogą tworzyć niestandardowe grupy lub [korzystać z zewnętrznej grupy w skojarzonych dzierżaw usługi Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Niestandardowe i zewnętrzne grupy mogą być używane razem z grupami systemowymi, zapewniając deweloperom widoczność produktów interfejsu API i dostęp do nich. Można na przykład utworzyć jedną grupę niestandardową dla deweloperów powiązanych z konkretną organizacją partnera i zezwolić im na dostęp do interfejsów API z produktów zawierających tylko odpowiednie interfejsy API. Użytkownik może należeć do więcej niż jednej grupy.

W tym przewodniku pokazano, jak Administratorzy wystąpienia interfejsu API zarządzania mogą dodawać nowe grupy i skojarzyć je z produktami i deweloperów.

Oprócz tworzenia grup i zarządzanie nimi w portalu wydawcy, można tworzyć i zarządzać przy użyciu interfejsu API REST API zarządzania grupami [grupy](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) jednostki.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonywanie zadań w tym artykule: [utworzenia wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Utwórz grupę

W tej sekcji przedstawiono sposób dodawania nowej grupy z kontem usługi API Management.

1. Wybierz **grup** kartę do lewego rogu ekranu.
2. Kliknij przycisk **+ Dodaj**.
3. Wprowadź unikatową nazwę grupy i opcjonalny opis.
4. Kliknij przycisk **Utwórz**.

    ![Dodaj nową grupę](./media/api-management-howto-create-groups/groups001.png)

Po utworzeniu grupy jest ona dodawana do **grup** listy. <br/>Aby edytować **nazwa** lub **opis** grupy, kliknij nazwę grupy i **ustawienia**.<br/>Aby usunąć grupę, kliknij nazwę grupy i naciśnij klawisz **usunąć**.

Teraz, gdy grupa nie zostanie utworzona, można ją skojarzyć z produktami i deweloperów.

## <a name="associate-group-product"> </a>Skojarz grupę z produktem

1. Wybierz **produktów** karcie po lewej stronie.
2. Kliknij nazwę żądanego produktu.
3. Naciśnij klawisz **kontrola dostępu**.
4. Kliknij przycisk **+ Dodaj grupę**.

    ![Skojarz grupę z produktem](./media/api-management-howto-create-groups/groups002.png)
5. Wybierz grupę, którą chcesz dodać.

    ![Skojarz grupę z produktem](./media/api-management-howto-create-groups/groups003.png)

    Aby usunąć grupę z produktu, kliknij przycisk **usunąć**.

    ![Usuwanie grupy](./media/api-management-howto-create-groups/groups004.png)

Jeśli produkt jest skojarzona z grupą, deweloperzy w tej grupie można wyświetlać i subskrybować produktu.

> [!NOTE]
> Aby dodać grupy usługi Azure Active Directory, zobacz [sposób autoryzowania konta dewelopera przy użyciu usługi Azure Active Directory w usłudze Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Skojarz grupy z deweloperami

W tej sekcji przedstawiono sposób skojarzenia z elementami członkowskimi grupy.

1. Wybierz **grup** kartę do lewego rogu ekranu.
2. Wybierz **członków**.

    ![Dodawanie elementu członkowskiego](./media/api-management-howto-create-groups/groups005.png)
3. Naciśnij klawisz **+ Dodaj** i wybierz członka.

    ![Dodawanie elementu członkowskiego](./media/api-management-howto-create-groups/groups006.png)
4. Naciśnij klawisz **wybierz**.

Po dodaniu skojarzenia między projektanta i grupy, możesz je wyświetlić w **użytkowników** kartę.

## <a name="next-steps"> </a>Następne kroki

* Po dodaniu do grupy Deweloperzy mogą wyświetlać i subskrybować produktów skojarzonych z tej grupy. Aby uzyskać więcej informacji, zobacz [jak tworzyć i publikować w usłudze Azure API Management produktu][How create and publish a product in Azure API Management],
* Oprócz tworzenia grup i zarządzanie nimi w portalu wydawcy, można tworzyć i zarządzać przy użyciu interfejsu API REST API zarządzania grupami [grupy](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) jednostki.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
