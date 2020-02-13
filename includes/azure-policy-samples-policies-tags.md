---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166591"
---
|Name (Nazwa) |Opis |Efekt (s) |Wersja |
|---|---|---|---|
|[Dodawanie znacznika do grup zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |Dodaje określony tag i wartość, gdy wszystkie grupy zasobów, w których brakuje tego tagu, zostaną utworzone lub zaktualizowane. Istniejące grupy zasobów można skorygować, wyzwalając zadanie korygowania. Jeśli tag istnieje z inną wartością, nie zostanie on zmieniony. |Zmodyfikować |1.0.0 |
|[Dodawanie tagu do zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |Dodaje określony tag i wartość po utworzeniu lub zaktualizowaniu dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. Jeśli tag istnieje z inną wartością, nie zostanie on zmieniony. Tagi w grupach zasobów nie są modyfikowane. |Zmodyfikować |1.0.0 |
|[Dodawanie lub zastępowanie tagu w grupach zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |Dodaje lub zastępuje określony tag i wartość po utworzeniu lub zaktualizowaniu dowolnej grupy zasobów. Istniejące grupy zasobów można skorygować, wyzwalając zadanie korygowania. |Zmodyfikować |1.0.0 |
|[Dodawanie lub zastępowanie tagu dla zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |Dodaje lub zastępuje określony tag i wartość podczas tworzenia lub aktualizowania dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. Tagi w grupach zasobów nie są modyfikowane. |Zmodyfikować |1.0.0 |
|[Dołącz tag i jego wartość domyślną](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |Dołącza określony tag i wartość po utworzeniu lub zaktualizowaniu dowolnego zasobu, w którym brakuje tego tagu. Program nie modyfikuje tagów zasobów utworzonych przed zastosowaniem tych zasad do momentu zmiany tych zasobów. Nie dotyczy grup zasobów. Dostępne są nowe zasady "Modyfikowanie", które obsługują korygowanie tagów w istniejących zasobach (zobacz https://aka.ms/modifydoc). |łączono |1.0.0 |
|[Dołącz tag i jego wartość domyślną do grup zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |Dołącza określony tag i wartość, gdy w grupie zasobów, w której brakuje tego tagu, jest tworzony lub aktualizowany. Program nie modyfikuje tagów grup zasobów utworzonych przed zastosowaniem tych zasad do momentu zmiany tych grup zasobów. Dostępne są nowe zasady "Modyfikowanie", które obsługują korygowanie tagów w istniejących zasobach (zobacz https://aka.ms/modifydoc). |łączono |1.0.0 |
|[Dołącz tag i jego wartość z grupy zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |Dołącza określony tag do jego wartości z grupy zasobów, gdy zostanie utworzony lub zaktualizowany dowolny zasób, w którym brakuje tego tagu. Program nie modyfikuje tagów zasobów utworzonych przed zastosowaniem tych zasad do momentu zmiany tych zasobów. Dostępne są nowe zasady "Modyfikowanie", które obsługują korygowanie tagów w istniejących zasobach (zobacz https://aka.ms/modifydoc). |łączono |1.0.0 |
|[Dziedzicz tag z grupy zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |Dodaje lub zastępuje określony tag i wartość z nadrzędnej grupy zasobów po utworzeniu lub zaktualizowaniu dowolnego zasobu. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. |Zmodyfikować |1.0.0 |
|[Dziedzicz tag z grupy zasobów, jeśli brakuje](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |Dodaje określony tag wraz z jego wartością z nadrzędnej grupy zasobów, gdy żaden zasób nie ma tego tagu jest tworzony lub aktualizowany. Istniejące zasoby można skorygować, wyzwalając zadanie korygowania. Jeśli tag istnieje z inną wartością, nie zostanie on zmieniony. |Zmodyfikować |1.0.0 |
|[Wymagaj określonego tagu](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |Wymusza istnienie tagu. Nie dotyczy grup zasobów. |pozbawić |1.0.0 |
|[Wymagaj określonego tagu w grupach zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |Wymusza istnienie tagu w grupach zasobów. |pozbawić |1.0.0 |
|[Wymagaj tagu i jego wartości](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |Wymusza wymagany tag i jego wartość. Nie dotyczy grup zasobów. |pozbawić |1.0.0 |
|[Wymagaj tagu i jego wartości w grupach zasobów](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |Wymusza wymagany tag i jego wartość w grupach zasobów. |pozbawić |1.0.0 |
