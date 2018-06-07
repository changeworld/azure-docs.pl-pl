---
title: Wdrażanie szablonów przy użyciu portalu Azure stosu | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania szablonów za pomocą portalu Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604237"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Wdrażanie szablonów przy użyciu portalu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Portal umożliwia wdrażanie szablonów usługi Azure Resource Manager stos Azure.

## <a name="to-deploy-a-template"></a>Aby wdrożyć szablon

1. Zaloguj się do portalu, wybierz opcję **nowy**, a następnie wybierz **niestandardowy**.
2. Wybierz **wdrażania szablonu**.
3. Wybierz **Edytuj szablon**, a następnie wklej kod JSON szablonu do okna kodu. Wybierz pozycję **Zapisz**.
4. Wybierz **Edytuj parametry**, podaj wartości parametrów, które są wyświetlane, a następnie wybierz **OK**.
5. Wybierz **subskrypcji**. Wybierz subskrypcję, którą chcesz użyć, a następnie wybierz **OK**.
6. Wybierz **grupy zasobów**. Wybierz istniejącą grupę zasobów lub Utwórz nowy, a następnie wybierz **OK**.
7. Wybierz pozycję **Utwórz**. Nowy Kafelek na pulpicie nawigacyjnym śledzi postęp procesu wdrożenia szablonu.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą programu PowerShell](azure-stack-deploy-template-powershell.md)
