---
title: storage-files-create-storage-account-portal
description: Tworzenie konta magazynu dla usługi Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów, a udział może obejmować nieograniczoną liczbę plików, nieprzekraczającą limitów pojemności konta magazynu.

Aby utworzyć konto magazynu:

1. W menu po lewej stronie kliknij znak **+**, aby utworzyć zasób.
2. Wpisz ciąg **konto magazynu** w polu wyszukiwania (1), wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka** (2), a następnie kliknij przycisk **Utwórz**.
    ![Zrzut ekranu przedstawiający pozycję konta magazynu w oknie dialogowym wyszukiwania zasobów](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. W polu **Nazwa** wpisz ciąg *mystorageacct*, a po nim kilka losowych liczb, dopóki nie zostanie wyświetlony zielony znacznik wyboru wskazujący, że nazwa jest unikatowa. Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zanotuj nazwę konta magazynu, ponieważ będziesz używać jej później. 
4. W polu **Model wdrażania** pozostaw wartość domyślną **Menedżer zasobów**. Aby dowiedzieć się więcej na temat różnic między usługą Azure Resource Manager, a wdrożeniem klasycznym, zobacz [Understand deployment models and the state of your resources (Omówienie modeli wdrażania i stanu zasobów)](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. W polu **Rodzaj konta** wybierz wartość **StorageV2**. Aby dowiedzieć się więcej na temat różnych rodzajów kont magazynu, zobacz [Understanding Azure storage accounts (Omówienie kont usługi Azure Storage)](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. W polu **Wydajność** zachowaj wartość domyślną **Standard Storage**. Usługa Azure Files aktualnie obsługuje tylko magazyn Standard Storage. Nawet w przypadku wybrania magazynu Premium Storage udział plików będzie przechowywany w magazynie Standard Storage.
7. W polu **Replikacja** wybierz wartość *Magazyn lokalnie nadmiarowy (LRS)*. 
8. Dla opcji **Wymagany bezpieczny transfer** zalecamy zawsze wybranie wartości **Włączone**. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Understanding encryption in-transit (Omówienie szyfrowania podczas przesyłania danych)](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. W obszarze **Subskrypcja** wybierz subskrypcję, w której chcesz utworzyć konto magazynu. Jeśli masz tylko jedną subskrypcję, powinna być ona wartością domyślną.
10. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nowy** i wpisz ciąg *myResourceGroup* jako nazwę.
11. W polu **Lokalizacja** wybierz pozycję **Wschodnie stany USA 2**.
12. W polu **Sieci wirtualne** pozostaw opcję domyślną *Wyłączone*. 
13. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby ułatwić znajdowanie konta magazynu.
14. Po zakończeniu kliknij przycisk **Utwórz**, aby rozpocząć wdrażanie.