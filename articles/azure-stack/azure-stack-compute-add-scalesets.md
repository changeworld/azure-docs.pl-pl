---
title: Udostępnianie zestawów skalowania maszyn wirtualnych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać zestawy skalowania maszyn wirtualnych w portalu Azure Marketplace stosu operator chmury
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 2c615dd781b40c3ebb78ae291453c5b4b2d2ef4d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971831"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Udostępnianie zestawów skalowania maszyn wirtualnych w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*
  
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowych usługi Azure Stack. Umożliwia ich wdrożenie zestawu identycznych maszyn wirtualnych oraz zarządzanie. Za pomocą wszystkie maszyny wirtualne skonfigurowane w taki sam sposób zestawów skalowania nie wymagają wstępna aprowizacja maszyn wirtualnych. Jest łatwiejsze tworzenie wielkoskalowych usług, przeznaczonych dla dużych wystąpień obliczeniowych, danych big data i obciążenia konteneryzowane.

Ten artykuł przeprowadzi Cię przez proces, aby udostępnić zestawy skalowania w witrynie Azure Marketplace stosu. Po wykonaniu tej procedury, użytkownicy mogą dodawać, czy zestawy skalowania maszyn wirtualnych do swojej subskrypcji.

Zestawy skalowania maszyn wirtualnych w usłudze Azure Stack są podobne do zestawów skalowania maszyn wirtualnych na platformie Azure. Aby uzyskać więcej informacji zobacz następujące filmy wideo:
* [Mark Russinovich omawia zestawy skalowania na platformie Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Zestawy skalowania maszyn wirtualnych według Guya Bowermana](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Zestawy skalowania maszyn wirtualnych nie obsługują automatyczne skalowanie w usłudze Azure Stack. Można dodać więcej wystąpień zestawu skalowania przy użyciu szablonów usługi Resource Manager, interfejsu wiersza polecenia lub programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- **Portal Marketplace:** rejestru usługi Azure Stack z globalnej platformy Azure, aby włączyć dostępność elementów w portalu Marketplace. Postępuj zgodnie z instrukcjami w [zarejestrować w usłudze Azure Stack z platformą Azure](azure-stack-registration.md).
- **Obraz systemu operacyjnego:** przed utworzeniem zestawu skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych), należy pobrać obrazów maszyn wirtualnych do użycia w zestawu skalowania maszyn wirtualnych z [usługi Azure Stack w portalu Marketplace](azure-stack-download-azure-marketplace-item.md). Obrazy muszą być już dostępne, zanim użytkownik może utworzyć nowego zestawu skalowania maszyn wirtualnych. 

## <a name="use-the-azure-stack-portal"></a>Używaj portalu Azure Stack 

>[!IMPORTANT]  
> Informacje przedstawione w tej sekcji ma zastosowanie, gdy używasz usługi Azure Stack w wersji 1808 lub nowszej. Jeśli używana wersja jest 1807 lub starszym, zobacz [Dodaj zestaw skalowania maszyn wirtualnych (przed 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Zaloguj się do portalu usługi Azure Stack. Następnie przejdź do **wszystkich usług**, następnie **zestawy skalowania maszyn wirtualnych**, a następnie w obszarze **obliczenia**, wybierz opcję **zestawy skalowania maszyn wirtualnych**. 
   ![Zestawy skalowania maszyn wirtualnych wybierz](media/azure-stack-compute-add-scalesets/all-services.png)

2. Wybierz pozycję Utwórz ***zestawy skalowania maszyn wirtualnych***.
   ![Tworzenie zestawu skalowania maszyn wirtualnych](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Wypełnij puste pola, wybierz z menu rozwijane na potrzeby **obraz dysku systemu operacyjnego**, **subskrypcji**, i **rozmiaru wystąpienia**. Wybierz **tak** dla **Użyj usługi managed disks**. Następnie kliknij pozycję **Utwórz**.
    ![Konfigurowanie i tworzenie](media/azure-stack-compute-add-scalesets/create.png)

4. Aby zobaczyć nowe skalowania maszyn wirtualnych zestawu, przejdź do **wszystkie zasoby**, wyszukaj nazwę zestawu skalowania maszyn wirtualnych, a następnie wybierz jego nazwę w wyszukiwaniu. 
   ![Wyświetlanie zestawu skalowania](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Dodaj zestaw skalowania maszyn wirtualnych (wcześniejszymi niż wersja 1808)

>[!IMPORTANT]  
> Informacje przedstawione w tej sekcji ma zastosowanie, gdy używasz usługi Azure Stack wersji wcześniejszej niż 1808. Jeśli używasz wersji 1808 lub nowszej, zobacz [za pomocą portalu usługi Azure Stack](#use-the-azure-stack-portal).

1. Otwórz w portalu Azure Marketplace stosu i łączenie z platformą Azure. Wybierz **zarządzania Marketplace**, następnie kliknij przycisk **+ Dodaj na platformie Azure**.

    ![Zarządzanie w portalu Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Dodawanie i pobieranie elementów portalu marketplace zestawu skalowania maszyn wirtualnych.

    ![Zestaw skalowania maszyn wirtualnych](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizowanie obrazów w zestawie skalowania maszyn wirtualnych

Po utworzeniu zestawu skalowania maszyn wirtualnych, użytkownicy mogą zaktualizować obrazów w skali bez zestawu konieczności można odtworzyć skalowania. Proces aktualizacji obrazu, zależy od następujących scenariuszy:

1. Określa szablon wdrożenia zestawu skalowania maszyn wirtualnych **najnowsze** dla **wersji**:  

   Gdy `version` jest ustawiona na **najnowsze** w `imageReference` części szablonu skalowania ustawiona, skalowanie w górę operacje przy użyciu zestawu skalowania dostępna najnowszą wersję obrazu dla wystąpień zestawu skalowania. Po zakończeniu skalowanie w górę, możesz usunąć starsze wystąpień zestawów skalowania maszyn wirtualnych. Wartości `publisher`, `offer`, i `sku` pozostają niezmienione. 

   W poniższym przykładzie JSON `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Skalowanie w górę można korzystać z nowego obrazu, należy pobrać tego nowego obrazu:  

   - W przypadku obrazów w portalu Marketplace jest dostępna nowsza wersja niż obrazu w zestawie skalowania, Pobierz nowy obraz, który zastępuje starsze obrazu. Po obrazu zostanie zastąpiony, użytkownik można przejść do skalowania w górę. 

   - Gdy wersja obrazu w portalu Marketplace jest taka sama jak obrazu w zestawie skalowania, Usuń obraz, który jest używany w zestawie skalowania, a następnie Pobierz nowy obraz. W czasie między usunięcie oryginalnego obrazu i pobieranie obrazu nie skalowanie w górę. 
      
     Proces ten jest wymagany do resyndicate obrazy, wchodzące używają formatu pliku rozrzedzonego, wprowadzonym w wersji 1803. 
 
2. Szablon wdrożenia zestawu skalowania maszyn wirtualnych **nie określa najnowsza wersja** dla **wersji** i zamiast tego Określa numer wersji:  

    Jeśli pobierzesz obraz za pomocą nowszej wersji (co powoduje zmianę dostępnej wersji), zestaw skalowania nie można skalować w górę. To jest celowe, jak wersja obrazu określonego w szablonie zestawu skalowania muszą być dostępne.  

Aby uzyskać więcej informacji, zobacz [dyski systemu operacyjnego i obrazy](./user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Skalowanie zestawu skalowania maszyn wirtualnych

Możesz skalować rozmiar *zestawu skalowania maszyn wirtualnych* być większy lub mniejszy.  

1. W portalu, wybierz zestaw skalowania, a następnie wybierz pozycję **skalowanie**.

2. Użyj paska suwaka, aby ustawić nowy poziom skalowania dla tego zestawu skalowania maszyn wirtualnych, a następnie kliknij przycisk **Zapisz**.
     ![Zestaw skalowania](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Usuń zestaw skalowania maszyn wirtualnych

Aby usunąć element galerii zestawu skalowania maszyn wirtualnych, uruchom następujące polecenie programu PowerShell:

```PowerShell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Nie można usunąć natychmiast elementu galerii. Noc musisz odświeżyć portal kilka razy, zanim element będzie wyświetlany jako usunięty z witryny Marketplace.

## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack](azure-stack-download-azure-marketplace-item.md)