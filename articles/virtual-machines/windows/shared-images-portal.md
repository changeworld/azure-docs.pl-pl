---
title: Tworzenie udostępnionego obrazów maszyn wirtualnych platformy Azure dla Windows przy użyciu portalu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i udostępniać obrazów maszyn wirtualnych za pomocą witryny Azure portal.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 50714f3fe6d30b4b93a0b42383b6f4634c86eafa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148044"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Utwórz galerię udostępnionego obrazu przy użyciu witryny Azure portal

A [galerii obrazów współdzielona](shared-image-galleries.md) upraszcza obraz niestandardowy do udostępniania całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe może służyć do uruchamiania wdrożenia zadania, takie jak wstępnego ładowania aplikacji, konfiguracje aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów współdzielona umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych z innymi osobami w organizacji, w ramach lub w wielu regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony mają być dostępne w nich i, którym chcesz udostępnić je za pomocą. Możesz utworzyć wiele galerii, dzięki czemu można grupować logicznie udostępnianych obrazów. 

Galeria jest zasobem najwyższego poziomu, który zapewnia kontrolę dostępu pełnej opartej na rolach (RBAC). Obrazy mogą być poddany kontroli wersji, a użytkownik może replikować każdej wersji obrazu z innym zestawem regiony platformy Azure. Galeria działa tylko z zarządzanych obrazów.

Funkcja galerii obrazów współdzielona ma wiele typów zasobów. Firma Microsoft będzie przy użyciu lub kompilowania w tym artykule:

| Resource | Opis|
|----------|------------|
| **Zarządzany obraz** | Jest to podstawowy obraz, które mogą być używane autonomicznie lub użyty do utworzenia **wersję obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionego maszyn wirtualnych. Zarządzany obraz to specjalny typ wirtualnego dysku twardego, który można utworzyć wiele maszyn wirtualnych i może teraz służyć do tworzenia wersji udostępnionego obrazu. |
| **Galeria obrazów** | W portalu Azure Marketplace, takich jak **galerii obrazów** to repozytorium do zarządzania i udostępniania obrazów, ale możesz kontrolować, kto ma dostęp. |
| **Definicję obrazu** | Obrazy w galerii są zdefiniowane i zawierają informacje dotyczące obrazu i wymagania dotyczące korzystania z niego wewnętrznie. Dotyczy to również, czy obraz jest Windows lub Linux, informacje o wersji i wymagań dotyczących minimalnej i maksymalnej pamięci. Jest definicja typu obrazu. |
| **Wersja obrazu** | **Wersję obrazu** , które jest używane do utworzenia maszyny Wirtualnej, podczas korzystania z galerii. Może mieć wiele wersji obrazu, zgodnie z potrzebami w danym środowisku. Gdy używasz, takie jak zarządzany obraz **wersję obrazu** tworzenie maszyny Wirtualnej, wersja obrazu jest używany do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów można wielokrotnie. |


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego. Możesz wykonać [samouczka: Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure przy użyciu programu Azure PowerShell](tutorial-custom-images.md) aby je utworzyć, jeśli to konieczne. Podczas pracy nad tym artykule, Zamień grupę zasobów i maszynę Wirtualną przypadkach nazwy.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Tworzenie maszyn wirtualnych z obrazu

Po zakończeniu wersję obrazu, można utworzyć jeden lub więcej nowych maszyn wirtualnych. 

W tym przykładzie utworzono maszynę Wirtualną o nazwie *myVMfromImage*w *myResourceGroup* w *wschodnie stany USA* centrum danych.

1. Na stronie dla używanej wersji obrazu, wybierz **Utwórz maszynę Wirtualną** menu w górnej części strony.
1. Dla **grupy zasobów**, wybierz opcję **Utwórz nową** i typ *myResourceGroup* dla nazwy.
1. W **nazwę maszyny wirtualnej**, typ *myVM*.
1. Aby uzyskać **Region**, wybierz opcję *wschodnie stany USA*.
1. Aby uzyskać **opcji dostępności**, pozostaw wartość domyślną *nadmiarowość nie infrastruktury, wymagane*.
1. Wartość **obraz** powinny zostać wypełnione automatycznie przypadku pracy ze strony wersję obrazu.
1. Aby uzyskać **rozmiar**, wybierz rozmiar maszyny Wirtualnej z listy dostępnych rozmiarów, a następnie kliknij przycisk "Wybierz".
1. W obszarze **Konto administratora** podaj nazwę użytkownika, taką jak *azureuser*, oraz hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Jeśli chcesz zezwolić na dostęp zdalny do maszyny Wirtualnej, w obszarze **publiczne porty wejściowe**, wybierz **Zezwalaj na wybranych portach** , a następnie wybierz **protokołu RDP (3389)** z listy rozwijanej. Jeśli nie chcesz zezwolić na dostęp zdalny do maszyny Wirtualnej, należy pozostawić **Brak** wybrany dla **publiczne porty wejściowe**.
1. Gdy skończysz, wybierz pozycję **przeglądu + Utwórz** znajdujący się u dołu strony.
1. Po maszyny Wirtualnej pozytywnie przejdą weryfikację, wybierz **Utwórz** w dolnej części strony Aby rozpocząć wdrażanie.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

Jeśli chcesz usunąć poszczególne zasoby, należy usunąć je w odwrotnej kolejności. Na przykład aby usunąć definicję obrazu, należy usunąć wszystkie wersje obrazu utworzonego na podstawie tego obrazu.

## <a name="next-steps"></a>Kolejne kroki

Można również utworzyć zasób galerii obrazów udostępnione za pomocą szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie udostępnionego galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Utwórz definicję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Utwórz wersję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Utwórz Maszynę wirtualną z obrazu wersji](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnione, galerie obrazów zobacz [Przegląd](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów udostępnione galerie obrazów](troubleshooting-shared-images.md).

