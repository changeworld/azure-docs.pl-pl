---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 06/18/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5358fe387d2a371d96d46d8546ce0f20b47ca54b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206487"
---
Stosowanie tagów do Twoich zasobów platformy Azure, zapewniając metadanych umożliwia ich logiczne zorganizowanie w taksonomii. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Po zastosowaniu tagów można pobrać wszystkie zasoby w subskrypcji o nazwie i wartości konkretnego tagu. Tagi umożliwiają pobranie powiązanych zasobów z różnych grup zasobów. To pomocne rozwiązanie, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Taksonomii należy wziąć pod uwagę samoobsługi metadane tagowania strategii oprócz strategii znakowanie automatycznie w celu zmniejszenia obciążenia użytkowników i zwiększyć dokładność.

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują tagów. Aby określić, jeśli tag można zastosować do typu zasobu, zobacz [obsługę dla zasobów platformy Azure tagów](../articles/azure-resource-manager/tag-support.md).
* Każdy zasób lub grupa zasobów może mieć co najwyżej 15 par nazwa/wartość tagu. To ograniczenie dotyczy tylko tagów stosowanych bezpośrednio do grupy zasobów lub zasobu. Grupa zasobów może zawierać wiele zasobów, z których każdy może mieć 15 par nazwa/wartość tagu. Jeśli masz więcej niż 15 wartości, które należy skojarzyć z zasobem, użyj ciągu JSON jako wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. W tym artykule przedstawiono przykład przypisywania ciągu JSON do tagu.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Virtual Machines i Virtual Machine Scale Sets są ograniczone do 2048 znaków, aby uzyskać wszystkie nazwy i wartości tagów w sumie. Tagi uogólnionej maszyny wirtualne nie są obsługiwane.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.
* Nie można zastosować znaczniki do klasycznych zasobów, takich jak usługi w chmurze.
* Nazwy tagów nie może zawierać następujących znaków: `<`, `>`, `%`, `&`, `\`, `?`, `/`
