---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a9a837d8a486e10ea2a62be69b889e657b1dbc05
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72601071"
---
Znaczniki są stosowane do zasobów platformy Azure, dzięki czemu metadane są logicznie organizowane w taksonomii. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Po zastosowaniu tagów można pobrać wszystkie zasoby w subskrypcji o nazwie i wartości konkretnego tagu. Tagi umożliwiają pobieranie pokrewnych zasobów z różnych grup zasobów. To pomocne rozwiązanie, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Taksonomia powinna uwzględniać samoobsługową strategię tagowania metadanych oprócz strategii automatycznego tagowania, aby zmniejszyć obciążenie użytkownikami i zwiększyć dokładność.

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](../articles/azure-resource-manager/tag-support.md).
* Każdy zasób lub Grupa zasobów może mieć maksymalnie 50 par nazwa/wartość tagu. Obecnie konta magazynu obsługują tylko 15 tagów, ale ten limit zostanie podniesiony do 50 w przyszłej wersji. Jeśli musisz zastosować więcej tagów niż maksymalna dozwolona liczba, użyj ciągu JSON dla wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów może zawierać wiele zasobów, dla których każda z nich ma 50 par nazwa/wartość.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Uogólnione maszyny wirtualne nie obsługują tagów.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.
* Nie można zastosować tagów do zasobów klasycznych, takich jak Cloud Services.
* Nazwy tagów nie mogą zawierać następujących znaków: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Obecnie Azure DNS strefy i usługi Traffic Manager nie umożliwiają również używania spacji w tagu. 
