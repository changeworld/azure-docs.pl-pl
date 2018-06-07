---
title: Wdrażanie szablonów z programem Visual Studio w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania szablonów z programem Visual Studio w stosie Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 6cd722fedc0483e37ce6ee491d74a7c985111353
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605127"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Wdrażanie szablonów w stosie Azure przy użyciu programu Visual Studio

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Visual Studio umożliwia wdrażanie szablonów usługi Azure Resource Manager stos Azure.

## <a name="to-deploy-a-template"></a>Aby wdrożyć szablon

1. [Zainstaluj i połącz](azure-stack-install-visual-studio.md) stos Azure z programem Visual Studio.
2. Otwórz program Visual Studio.
3. Wybierz **pliku**, a następnie wybierz **nowy**. W **nowy projekt**, wybierz pozycję **grupy zasobów platformy Azure**.
4. Wprowadź **nazwa** dla nowego projektu, a następnie wybierz **OK**.
5. W **wybierz szablon Azure**, wybierz **Szybki Start Azure stosu** z listy rozwijanej.
6. Wybierz **101-create magazynu account**, a następnie wybierz **OK**.
7. W nowego projektu, rozwiń węzeł **szablony** w węźle **Eksploratora rozwiązań** Aby wyświetlić dostępne szablony.
8. W **Eksploratora rozwiązań**, wybierz nazwę projektu, a następnie wybierz **Wdróż**. Wybierz **nowe wdrożenie**.
9. W **wdrażanie w grupie zasobów**, użyj **subskrypcji** listy rozwijanej wybierz swoją subskrypcję Microsoft Azure stosu.
10. Z **grupy zasobów** listy, wybierz istniejącą grupę zasobów lub Utwórz nową.
11. Z **lokalizacja grupy zasobów** listy, wybierz lokalizację, a następnie wybierz **Wdróż**.
12. W **Edytuj parametry**, podaj wartości parametrów (różniących się przez szablon), a następnie wybierz **zapisać**.

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie szablonów za pomocą wiersza polecenia](azure-stack-deploy-template-command-line.md)
* [Tworzenie szablonów dla stosu Azure](azure-stack-develop-templates.md)
