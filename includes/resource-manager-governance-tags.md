---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199142"
---
Znaczniki są stosowane do zasobów platformy Azure, dzięki czemu metadane są logicznie organizowane w taksonomii. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Po zastosowaniu tagów można pobrać wszystkie zasoby w subskrypcji o nazwie i wartości konkretnego tagu. Tagi umożliwiają pobieranie pokrewnych zasobów z różnych grup zasobów. To pomocne rozwiązanie, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Taksonomia powinna uwzględniać samoobsługową strategię tagowania metadanych oprócz strategii automatycznego tagowania, aby zmniejszyć obciążenie użytkownikami i zwiększyć dokładność.

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](../articles/azure-resource-manager/tag-support.md).
* Każdy zasób lub Grupa zasobów może mieć maksymalnie 50 par nazwa/wartość tagu. Jeśli musisz zastosować więcej tagów niż maksymalna dozwolona liczba, użyj ciągu JSON dla wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów może zawierać wiele zasobów, dla których każda z nich ma 50 par nazwa/wartość.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Uogólnione maszyny wirtualne nie obsługują tagów.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.
* Nie można zastosować tagów do zasobów klasycznych, takich jak Cloud Services.
* Nazwy tagów nie mogą zawierać następujących znaków: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Obecnie Azure DNS strefy i usługi Traffic Manager nie umożliwiają również używania spacji w tagu. 
