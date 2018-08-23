---
title: Wdrażanie szablonów przy użyciu portalu w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażanie szablonów za pomocą portalu usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 931c3d8beb9f2ed12228c74f09f84bbdee1798b8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061681"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Wdrażanie szablonów przy użyciu portalu Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Portal służy do wdrażania szablonów usługi Azure Resource Manager do usługi Azure Stack.

## <a name="to-deploy-a-template"></a>Aby wdrożyć szablon

1. Zaloguj się do portalu, wybierz opcję **New**, a następnie wybierz pozycję **niestandardowe**.
2. Wybierz **wdrożenie szablonu**.
3. Wybierz **Edytuj szablon**, a następnie wklej kod JSON szablonu do okna kodu. Wybierz pozycję **Zapisz**.
4. Wybierz **Edytuj parametry**, podaj wartości parametrów, które są wyświetlane, a następnie wybierz **OK**.
5. Wybierz **subskrypcji**. Wybierz subskrypcję, którą chcesz użyć, a następnie wybierz **OK**.
6. Wybierz **grupy zasobów**. Wybierz istniejącą grupę zasobów lub Utwórz nową, a następnie wybierz **OK**.
7. Wybierz pozycję **Utwórz**. Nowy Kafelek na pulpicie nawigacyjnym śledzi postęp wdrożenia szablonu.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
