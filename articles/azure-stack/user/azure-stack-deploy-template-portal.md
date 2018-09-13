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
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: b2f4fbca2dcbdd537302746c7b6109e467ea31d7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720229"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Wdrażanie szablonów przy użyciu portalu Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Portal służy do wdrażania szablonów usługi Azure Resource Manager do usługi Azure Stack.

## <a name="to-deploy-a-template"></a>Aby wdrożyć szablon

1. Zaloguj się do portalu, wybierz opcję **+ Utwórz zasób**, a następnie wybierz pozycję **niestandardowe**.
2. Wybierz **wdrożenie szablonu**.
3. Wybierz **Edytuj szablon**, a następnie wklej kod JSON szablonu do okna kodu. Wybierz pozycję **Zapisz**.
4. Wybierz **Edytuj parametry**, podaj wartości parametrów, które są wyświetlane, a następnie wybierz **OK**.
5. Wybierz **subskrypcji**. Wybierz subskrypcję, którą chcesz użyć, a następnie wybierz **OK**.
6. Wybierz **grupy zasobów**. Wybierz istniejącą grupę zasobów lub Utwórz nową, a następnie wybierz **OK**.
7. Wybierz pozycję **Utwórz**. Nowy Kafelek na pulpicie nawigacyjnym śledzi postęp wdrożenia szablonu.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
