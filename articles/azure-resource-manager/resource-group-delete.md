---
title: Usuń grupę zasobów i zasoby — usługi Azure Resource Manager
description: W tym artykule opisano, jak usługi Azure Resource Manager porządkuje usuwania zasobów, podczas usuwania grupy zasobów. Opisano w nim kodów odpowiedzi i jak Menedżera zasobów obsługuje je, aby określić, jeśli usunięcie powiodło się.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363663"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Usuwanie grupy zasobów w usłudze Azure Resource Manager

W tym artykule opisano, jak usługi Azure Resource Manager porządkuje usuwania zasobów, podczas usuwania grupy zasobów.

## <a name="determine-order-of-deletion"></a>Określić kolejność usuwania

Podczas usuwania grupy zasobów usługi Resource Manager, określa kolejność, aby usunąć zasoby. Używa następującej kolejności:

1. Wszystkie zasoby podrzędne (zagnieżdżona) są usuwane.

2. Zasoby, które zarządzają inne zasoby są następnie usuwane. Zasób może mieć `managedBy` właściwość ustawioną na wskazywanie, czy inny zasób zarządza nim. Gdy ta właściwość jest ustawiona, zasobu, który zarządza inne zasoby zostaną usunięte przed inne zasoby.

3. Pozostałe zasoby są usuwane po poprzednich dwóch kategorii.

## <a name="resource-deletion"></a>Usuwanie zasobów

Po kolejność jest określana, Menedżer zasobów wystawia operację usuwania dla każdego zasobu. Czeka on na wszystkie zależności zakończyć działanie przed kontynuowaniem.

W przypadku operacji synchronicznych kody Oczekiwana odpowiedź oznaczająca Powodzenie to:

* 200
* 204
* 404

Dla operacji asynchronicznych Oczekiwana odpowiedź oznaczająca Powodzenie to 202. Menedżer zasobów śledzi nagłówek lokalizacji lub nagłówek operacji asynchronicznych platformy azure, można ustalić stanu operacji asynchronicznych usuwania.
  
### <a name="errors"></a>Błędy

Podczas operacji usuwania zwraca błąd, Menedżer zasobów ponawia próbę wywołania usunięcia. 5xx, 429 i kodów stanu 408 się zdarzyć ponownych prób. Domyślnie okres czasu ponawiania prób to 15 minut.

## <a name="after-deletion"></a>Po usunięciu

Menedżer zasobów wystawia wywołanie GET dla każdego zasobu, do którego próbowano usunąć. Odpowiedź to wywołanie GET powinien być 404. Gdy Menedżera zasobów odbiera odpowiedź 404, które uzna za usuwania zostały ukończone pomyślnie. Menedżer zasobów spowoduje usunięcie zasobu z pamięci podręcznej.

Jednak jeśli wywołanie GET dla zasobu zwraca 200 lub 201, Menedżer zasobów odtwarza zasobu.

### <a name="errors"></a>Błędy

Jeśli operacja GET zwróci błąd, Menedżer zasobów ponawia próbę GET dla następujący kod błędu:

* Mniej niż 100
* 408
* 429
* Większe niż 500

Dla innych kodów błędu usługi Resource Manager nie usunięcia zasobu.

## <a name="next-steps"></a>Kolejne kroki

* Aby zrozumieć pojęcia usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).
* Usuwanie poleceń można znaleźć [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [wiersza polecenia platformy Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete), i [interfejsu API REST](/rest/api/resources/resourcegroups/delete).
