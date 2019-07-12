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
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841492"
---
Stosowanie tagów do Twoich zasobów platformy Azure, zapewniając metadanych umożliwia ich logiczne zorganizowanie w taksonomii. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Po zastosowaniu tagów można pobrać wszystkie zasoby w subskrypcji o nazwie i wartości konkretnego tagu. Tagi umożliwiają pobranie powiązanych zasobów z różnych grup zasobów. To pomocne rozwiązanie, gdy trzeba zorganizować zasoby w celach rozliczeniowych lub zarządzania.

Taksonomii należy wziąć pod uwagę samoobsługi metadane tagowania strategii oprócz strategii znakowanie automatycznie w celu zmniejszenia obciążenia użytkowników i zwiększyć dokładność.

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują tagów. Aby określić, jeśli tag można zastosować do typu zasobu, zobacz [obsługę dla zasobów platformy Azure tagów](../articles/azure-resource-manager/tag-support.md).
* Każdy zasób lub grupa zasobów może mieć maksymalnie 50 par nazwa/wartość tagu. Obecnie jest obsługiwany tylko kont magazynu, liczba tagów wynosząca 15, ale ten limit zostanie wygenerowany, 50 w przyszłej wersji. Jeśli potrzebujesz więcej tagów niż maksymalna liczba dozwolonych, użyj ciągu JSON jako wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów może zawierać wiele zasobów, w których każdy może mieć 50 par nazwa/wartość tagu.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Tagi uogólnionej maszyny wirtualne nie są obsługiwane.
* Tagi zastosowane do grupy zasobów nie są dziedziczone przez zasoby należące do tej grupy.
* Nie można zastosować znaczniki do klasycznych zasobów, takich jak usługi w chmurze.
* Nazwy tagów nie może zawierać następujących znaków: `<`, `>`, `%`, `&`, `\`, `?`, `/`
