---
title: Migrowanie aplikacji logiki między subskrypcjami, grupami zasobów lub regionami
description: Migrowanie aplikacji logicznych lub kont integracji do innych subskrypcji platformy Azure, grup zasobów lub lokalizacji (regionów)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: f5944accb185f1311c811cf65a8ea8348fd569db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605612"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>Przenoszenie zasobów aplikacji logiki do innych subskrypcji platformy Azure, grup zasobów lub regionów

Aby przeprowadzić migrację aplikacji logiki lub powiązanych zasobów do innej subskrypcji platformy Azure, grupy zasobów lub regionu, masz różne sposoby wykonywania tych zadań, takich jak witryna Azure portal, Azure PowerShell, interfejs wiersza polecenia platformy Azure i interfejs API REST. Przed przeniesieniem zasobów zapoznaj się z tymi zagadnieniami: 

* Można przenosić tylko [określone typy zasobów aplikacji logiki](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) między grupami zasobów platformy Azure lub subskrypcjami.

* Sprawdź [limity](../logic-apps/logic-apps-limits-and-config.md) liczby zasobów aplikacji logiki, które można mieć w subskrypcji platformy Azure i w każdym regionie platformy Azure. Te limity mają wpływ na to, czy można przenosić określone typy zasobów, gdy region pozostaje taki sam w subskrypcjach lub grupach zasobów. Na przykład można mieć tylko jedno bezpłatne konto integracji warstwy dla każdego regionu platformy Azure w każdej subskrypcji platformy Azure.

* Po migracji aplikacji logiki między subskrypcjami, grupami zasobów lub regionami należy ponownie utworzyć lub ponownie autoryzować wszystkie połączenia wymagające uwierzytelniania otwartego (OAuth).

* Za każdym razem, gdy przenosisz zasoby, platforma Azure tworzy nowe identyfikatory zasobów. Dlatego upewnij się, że zamiast tego używasz nowych identyfikatorów i zaktualizuj wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami.

## <a name="prerequisites"></a>Wymagania wstępne

* Ta sama subskrypcja platformy Azure, która została użyta do utworzenia aplikacji logiki lub konta integracji, które chcesz przenieść

* Uprawnienia właściciela zasobów do przenoszenia i konfigurowania zasobów, które chcesz. Dowiedz się więcej o [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/built-in-roles.md#owner)

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Przenoszenie zasobów między subskrypcjami

Aby przenieść zasób, taki jak aplikacja logiki lub konto integracji, do innej subskrypcji platformy Azure, można użyć witryny Azure Portal, Azure PowerShell, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Te kroki obejmują witrynę Azure portal, której można użyć, gdy region zasobu pozostaje taki sam. Aby uzyskać inne kroki i ogólne przygotowanie, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz zasób aplikacji logiki, który chcesz przenieść.

1. Na stronie **Przegląd** zasobu obok pozycji **Subskrypcja**wybierz łącze **zmiany.**

1. Na stronie **Przenoszenie zasobów** wybierz zasób aplikacji logiki i wszystkie powiązane zasoby, które chcesz przenieść.

1. Z listy **Subskrypcja** wybierz subskrypcję docelową.

1. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę**.

1. Aby potwierdzić, że wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizujesz ich za pomocą nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz **przycisk OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Przenoszenie zasobów między grupami zasobów

Aby przenieść zasób, taki jak aplikacja logiki lub konto integracji, do innej grupy zasobów platformy Azure, można użyć witryny Azure Portal, Azure PowerShell, interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Te kroki obejmują witrynę Azure portal, której można użyć, gdy region zasobu pozostaje taki sam. Aby uzyskać inne kroki i ogólne przygotowanie, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Przed faktycznym przeniesieniem zasobów między grupami można sprawdzić, czy można pomyślnie przenieść zasób do innej grupy. Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności ruchu](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz zasób aplikacji logiki, który chcesz przenieść.

1. Na stronie **Przegląd** zasobu obok **pozycji Grupa zasobów**wybierz łącze **zmiany.**

1. Na stronie **Przenoszenie zasobów** wybierz zasób aplikacji logiki i wszystkie powiązane zasoby, które chcesz przenieść.

1. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę**.

1. Aby potwierdzić, że wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizujesz ich za pomocą nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz **przycisk OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Przenoszenie zasobów między regionami

Jeśli chcesz przenieść aplikację logiki do innego regionu, opcje zależą od sposobu, w jaki utworzono aplikację logiki. Na podstawie opcji, które wybierzesz, należy ponownie utworzyć lub ponownie autoryzować połączenia w aplikacji logiki.

* W witrynie Azure portal ponownie utworzyć aplikację logiki w nowym regionie i ponownie skonfigurować ustawienia przepływu pracy. Aby zaoszczędzić czas, można skopiować podstawową definicję przepływu pracy i połączenia z aplikacji źródłowej do aplikacji docelowej. Aby wyświetlić "kod" za aplikacją logiki, na pasku narzędzi Projektanta aplikacji logiki wybierz **widok Kod**.

* Korzystając z programu Visual Studio i narzędzia Azure Logic Apps Tools for Visual Studio, można [otworzyć i pobrać aplikację logiki](../logic-apps/manage-logic-apps-with-visual-studio.md) z witryny Azure portal jako szablon Usługi Azure Resource [Manager.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Ten szablon jest w większości gotowy do wdrożenia i zawiera definicje zasobów dla aplikacji logiki, w tym samego przepływu pracy i połączeń. Szablon deklaruje również parametry wartości do użycia podczas wdrażania. W ten sposób można łatwiej zmienić miejsce i sposób wdrażania aplikacji logiki, w zależności od potrzeb. Aby określić lokalizację i inne informacje niezbędne do wdrożenia, można użyć oddzielnego pliku parametrów.

* Jeśli aplikacja logiki została utworzona i wdrożona przy użyciu narzędzi ciągłej integracji (CI) i ciągłego dostarczania (CD), takich jak potoki platformy Azure w usłudze Azure DevOps, możesz wdrożyć aplikację w innym regionie przy użyciu tych narzędzi.

Aby uzyskać więcej informacji na temat szablonów wdrażania aplikacji logiki, zobacz następujące tematy:

* [Omówienie: automatyzacja wdrażania aplikacji logiki platformy Azure przy użyciu szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Znajdowanie, otwieranie i pobieranie aplikacji logiki z witryny Azure portal do programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Tworzenie szablonów usługi Azure Resource Manager dla aplikacji logiki azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Wdrażanie szablonów usługi Azure Resource Manager dla aplikacji logiki azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Powiązane zasoby

Niektóre zasoby platformy Azure, takie jak lokalne zasoby bramy danych na platformie Azure, mogą istnieć w regionie, który różni się od aplikacji logiki, które używają tych zasobów. Jednak inne zasoby platformy Azure, takie jak połączone konta integracji, muszą istnieć w tym samym regionie co aplikacje logiki. Na podstawie scenariusza upewnij się, że aplikacje logiki można uzyskać dostęp do zasobów, które aplikacje oczekują istnieć w tym samym regionie.

Na przykład, aby połączyć aplikację logiki z kontem integracji, oba zasoby muszą istnieć w tym samym regionie. W scenariuszach, takich jak odzyskiwanie po awarii, zwykle chcesz kont integracji, które mają taką samą konfigurację i artefakty. W innych scenariuszach może być konieczne kont integracji z różnych konfiguracji i artefaktów.

Łączniki niestandardowe w usłudze Azure Logic Apps są widoczne dla autorów i użytkowników łączników, którzy mają tę samą subskrypcję platformy Azure i tę samą dzierżawę usługi Azure Active Directory. Te łączniki są dostępne w tym samym regionie, w którym są wdrażane aplikacje logiki. Aby uzyskać więcej informacji, zobacz [Share custom connectors in your organization (Udostępnianie łączników niestandardowych w organizacji)](https://docs.microsoft.com/connectors/custom-connectors/share).

Szablon, który można uzyskać z programu Visual Studio zawiera tylko definicje zasobów dla aplikacji logiki i jej połączeń. Dlatego jeśli aplikacja logiki używa innych zasobów, na przykład konta integracji i artefaktów B2B, takich jak partnerzy, umowy i schematy, należy wyeksportować szablon tego konta integracji przy użyciu witryny Azure portal. Ten szablon zawiera definicje zasobów dla konta integracji i artefaktów. Jednak szablon nie jest w pełni sparametryzowany. Dlatego należy ręcznie parametryzować wartości, które mają być używane do wdrożenia.

### <a name="export-templates-for-integration-accounts"></a>Eksportowanie szablonów dla kont integracji

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz swoje konto integracji.

1. W menu konta integracyjnego w obszarze **Ustawienia**wybierz pozycję **Eksportuj szablon**.

1. Na pasku narzędzi wybierz pozycję **Pobierz**i zapisz szablon.

1. Otwórz i edytuj szablon, aby sparametryzować wartości niezbędne do wdrożenia.

## <a name="next-steps"></a>Następne kroki

[Przenoszenie zasobów platformy Azure do nowych grup zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
