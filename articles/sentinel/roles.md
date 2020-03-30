---
title: Uprawnienia w usłudze Azure Sentinel | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak usługa Azure Sentinel używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikuje dozwolone akcje dla każdej roli.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587179"
---
# <a name="permissions-in-azure-sentinel"></a>Uprawnienia w usłudze Azure Sentinel

Usługa Azure Sentinel używa [kontroli dostępu opartej na rolach (RBAC),](../role-based-access-control/role-assignments-portal.md)aby zapewnić [wbudowane role,](../role-based-access-control/built-in-roles.md) które można przypisać do użytkowników, grup i usług na platformie Azure.

Za pomocą RBAC, można użyć i utworzyć role w zespole operacji zabezpieczeń, aby udzielić odpowiedniego dostępu do usługi Azure Sentinel. Na podstawie ról masz szczegółową kontrolę nad tym, co użytkownicy z dostępem do usługi Azure Sentinel można zobaczyć. Role RBAC można przypisać bezpośrednio w obszarze roboczym usługi Azure Sentinel lub do grupy subskrypcji lub zasobów, do której należy obszar roboczy.

Istnieją trzy określone wbudowane role usługi Azure Sentinel.  
**Wszystkie wbudowane role usługi Azure Sentinel zapewniają dostęp do odczytu danych w obszarze roboczym usługi Azure Sentinel.**
- [Czytnik wartownii platformy Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Obiekt odpowiadający usługi Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Współautor usługi Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Oprócz ról dedykowanych funkcji RBAC usługi Azure Sentinel istnieją role usługi Azure i log analytics RBAC, które mogą udzielić szerszego zestawu uprawnień, które obejmują dostęp do obszaru roboczego usługi Azure Sentinel i innych zasobów:

- **Role platformy Azure:** [właściciel,](../role-based-access-control/built-in-roles.md#owner) [współautor](../role-based-access-control/built-in-roles.md#contributor)i [czytnik](../role-based-access-control/built-in-roles.md#reader). Role platformy Azure zapewniają dostęp do wszystkich zasobów platformy Azure, w tym obszarów roboczych usługi Log Analytics i zasobów usługi Azure Sentinel.

-   **Role analizy dzienników:** [współautor usługi Log Analytics,](../role-based-access-control/built-in-roles.md#log-analytics-contributor)czytnik analizy [dzienników](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role usługi Log Analytics zapewniają dostęp we wszystkich obszarach roboczych usługi Log Analytics. 

> [!NOTE]
> Role usługi Log Analytics również udzielić dostępu do odczytu we wszystkich zasobach platformy Azure, ale tylko przypisać uprawnienia do zapisu do zasobów usługi Log Analytics.


Na przykład użytkownik, który jest przypisany z **czytnikiem wartownika platformy Azure** i **usługi Azure contributor** (nie współautor azure **sentinel)** role, będzie mógł edytować dane w usłudze Azure Sentinel, chociaż mają tylko uprawnienia **czytnika wartownika.** W związku z tym jeśli chcesz udzielić uprawnień tylko w usłudze Azure Sentinel, należy starannie usunąć wcześniejsze uprawnienia tego użytkownika, upewniając się, że nie przerwać żadnej potrzebnej roli uprawnień dla innego zasobu.

> [!NOTE]
>- Usługa Azure Sentinel używa podręczników do automatycznej odpowiedzi na zagrożenia. Podręczniki wykorzystują usługi Azure Logic Apps i są oddzielnym zasobem platformy Azure. Można przypisać określonych członków zespołu operacji zabezpieczeń z opcją używania aplikacji logiki do aranżacji zabezpieczeń, automatyzacji i odpowiedzi (SOAR) operacji. Za pomocą roli [współautora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor) lub roli [operatora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator) można przypisać jawne uprawnienia do używania podręczników.
>- Aby dodać łączniki danych, niezbędne role dla każdego łącznika są dla każdego typu łącznika i są wyświetlane na odpowiedniej stronie łącznika. Ponadto w celu połączenia dowolnego źródła danych, musisz mieć uprawnienia do zapisu w obszarze roboczym usługi Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli są wyświetlane role i dozwolone akcje w usłudze Azure Sentinel. X wskazuje, że akcja jest dozwolona dla tej roli.

| Rola | Tworzenie i uruchamianie podręczników| Tworzenie i edytowanie pulpitów nawigacyjnych, reguł analitycznych i innych zasobów usługi Azure Sentinel | Zarządzanie zdarzeniami (odrzucanie, przypisywanie itp.) | Wyświetlanie danych, zdarzeń, pulpitów nawigacyjnych i innych zasobów usługi Azure Sentinel |
|--- |---|---|---|---|
| Czytnik wartownii platformy Azure | -- | -- | -- | X |
| Obiekt odpowiadający usługi Azure Sentinel|--|--| X | X |
| Współautor usługi Azure Sentinel | -- | X | X | X |
| Współautor usługi Azure Sentinel + współautor aplikacji logiki | X | X | X | X |


> [!NOTE]
> - Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład przypisz rolę współautora usługi Azure Sentinel tylko do użytkowników, którzy muszą tworzyć reguły lub pulpity nawigacyjne.
> - Zaleca się ustawienie uprawnień dla usługi Azure Sentinel w zakresie grupy zasobów, dzięki czemu użytkownik może mieć dostęp do wszystkich obszarów roboczych usługi Azure Sentinel w tej samej grupie zasobów.
>
## <a name="building-custom-rbac-roles"></a>Tworzenie niestandardowych ról RBAC

Oprócz lub zamiast przy użyciu wbudowanych ról RBAC, można utworzyć niestandardowe role RBAC dla usługi Azure Sentinel. Niestandardowe role RBAC dla usługi Azure Sentinel są tworzone w taki sam sposób, w jaki tworzysz inne niestandardowe role [usługi Azure RBAC,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) na podstawie [określonych uprawnień do usługi Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) i zasobów usługi Azure Log [Analytics.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Zaawansowana kontrola RBAC danych przechowywanych w usłudze Azure Sentinel
  
Zaawansowanej kontroli dostępu opartej na rolach usługi Log Analytics można używać w przypadku danych w obszarze roboczym usługi Azure Sentinel. Obejmuje to zarówno kontroli dostępu na podstawie ról na typ danych i zasobów zorientowanych na role kontroli dostępu. Aby uzyskać więcej informacji na temat ról usługi Log Analytics, zobacz [Zarządzanie danymi dziennika i obszarami roboczymi w usłudze Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak pracować z rolami dla użytkowników usługi Azure Sentinel i co każda rola umożliwia użytkownikom.

* [Blog usługi Azure Sentinel](https://aka.ms/azuresentinelblog). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
