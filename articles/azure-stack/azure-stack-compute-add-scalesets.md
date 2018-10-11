---
title: Udostępnianie zestawów skalowania maszyn wirtualnych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać zestawy skalowania maszyn wirtualnych w portalu Azure Marketplace stosu operator chmury
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 37122f11990d292e250c0a0bc42c0527731f599a
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076396"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Udostępnianie zestawów skalowania maszyn wirtualnych w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*
  
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowych usługi Azure Stack. Umożliwia ich wdrożenie zestawu identycznych maszyn wirtualnych oraz zarządzanie. Za pomocą wszystkich maszyn wirtualnych skonfigurowane tak samo, zestawów skalowania nie wymagają wstępnego inicjowania obsługi administracyjnej maszyn wirtualnych. Jest łatwiejsze tworzenie wielkoskalowych usług, przeznaczonych dla dużych wystąpień obliczeniowych, danych big data i obciążenia konteneryzowane.

Ten artykuł przeprowadzi Cię przez proces, aby udostępnić zestawy skalowania w witrynie Azure Marketplace stosu. Po wykonaniu tej procedury, użytkownicy mogą dodawać, czy zestawy skalowania maszyn wirtualnych do swojej subskrypcji.

Zestawy skalowania maszyn wirtualnych w usłudze Azure Stack są podobne zestawy skalowania maszyn wirtualnych na platformie Azure. Aby uzyskać więcej informacji zobacz następujące filmy wideo:
* [Mark Russinovich omawia zestawy skalowania na platformie Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Zestawy skalowania maszyn wirtualnych według Guya Bowermana](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

W usłudze Azure Stack zestawów skalowania maszyn wirtualnych nie obsługuje automatycznego skalowania. Można dodać więcej wystąpień zestawu skalowania przy użyciu szablonów usługi Resource Manager, interfejsu wiersza polecenia lub programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

- **Portal Marketplace**  
    Zarejestruj usługę Azure Stack globalnej platformy Azure, aby włączyć dostępność elementów w portalu Marketplace. Postępuj zgodnie z instrukcjami w [zarejestrować w usłudze Azure Stack z platformą Azure](azure-stack-registration.md).
- **Obraz systemu operacyjnego**  
  Przed utworzeniem zestawu skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych), należy pobrać obrazów maszyn wirtualnych do użycia w zestawu skalowania maszyn wirtualnych z [usługi Azure Stack w portalu Marketplace](azure-stack-download-azure-marketplace-item.md). Obrazy muszą być już dostępne, zanim użytkownik może utworzyć nowego zestawu skalowania maszyn wirtualnych. 


## <a name="use-the-azure-stack-portal"></a>Używaj portalu Azure Stack 

>[!NOTE]  
> Informacje przedstawione w tej sekcji ma zastosowanie, gdy używasz usługi Azure Stack w wersji 1808 lub nowszej. Jeśli używana wersja jest 1807 lub starszym, zobacz [Dodaj zestaw skalowania maszyn wirtualnych (przed 1808)](#add-the-virtual-machine-scale-set-(prior-to-version-1808)).

1. Zaloguj się do portalu usługi Azure Stack. Następnie przejdź do **wszystkich usług** > **zestawy skalowania maszyn wirtualnych**, a następnie w obszarze *obliczenia*, wybierz opcję **zestawówskalowaniamaszynwirtualnych**. 
   ![Zestawy skalowania maszyn wirtualnych wybierz](media/azure-stack-compute-add-scalesets/all-services.png)

2. Wybierz pozycję Utwórz ***zestawy skalowania maszyn wirtualnych***.
   ![Tworzenie zestawu skalowania maszyn wirtualnych](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Wypełnij puste pola, wybierz z menu rozwijane na potrzeby *obraz dysku systemu operacyjnego*, *subskrypcji*, i *rozmiaru wystąpienia*. Wybierz **tak** dla *Użyj usługi managed disks*. Następnie wybierz przycisk **Create** (Utwórz).
    ![Konfigurowanie i tworzenie](media/azure-stack-compute-add-scalesets/create.png)

4. Aby zobaczyć nowe skalowania maszyn wirtualnych zestawu, przejdź do **wszystkie zasoby**, wyszukaj nazwę zestawu skalowania maszyn wirtualnych, a następnie wybierz jego nazwę w wyszukiwaniu. 
   ![Wyświetlanie zestawu skalowania](media/azure-stack-compute-add-scalesets/search.png)



## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Dodaj zestaw skalowania maszyn wirtualnych (wcześniejszymi niż wersja 1808)
>[!NOTE]  
> Informacje przedstawione w tej sekcji ma zastosowanie, gdy używasz usługi Azure Stack wersji wcześniejszej niż 1808. Jeśli używasz wersji 1808 lub nowszej, zobacz [za pomocą portalu usługi Azure Stack](#use-the-azure-stack-portal).

1. Otwórz w portalu Azure Marketplace stosu i łączenie z platformą Azure. Wybierz **zarządzania Marketplace**> **+ Dodaj na platformie Azure**.

    ![Zarządzanie w portalu Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Dodawanie i pobieranie elementów portalu marketplace zestawu skalowania maszyn wirtualnych.

    ![Zestaw skalowania maszyn wirtualnych](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizowanie obrazów w zestawie skalowania maszyn wirtualnych

Po utworzeniu zestawu skalowania maszyn wirtualnych, użytkownicy mogą zaktualizować obrazów w skali bez zestawu konieczności można odtworzyć skalowania. Proces aktualizacji obrazu, zależy od następujących scenariuszy:

1. Szablon wdrożenia zestawu skalowania maszyn wirtualnych **określa najnowsza wersja** dla *wersji*:  

   Gdy *wersji* jest ustawiony jako **najnowsze** w *imageReference* części szablonu skalowania ustawiona, skalowanie operacji przy użyciu zestawu skalowania najnowszej dostępnej wersji Obraz skali zestawu wystąpień. Po zakończeniu skalowania w górę, możesz usunąć starsze wystąpień zestawów skalowania maszyn wirtualnych.  (Wartości *wydawcy*, *oferują*, i *jednostki sku* pozostają bez zmian). 

   W poniższym przykładzie JSON *najnowsze*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Skalowanie w górę można korzystać z nowego obrazu, należy pobrać tego nowego obrazu:  

   - Gdy obraz w witrynie Marketplace jest dostępna nowsza wersja niż obrazu w zestawie skalowania: Pobierz nowy obraz, który zastępuje starsze obrazu. Po obrazu zostanie zastąpiony, użytkownik można przejść do skalowania w górę. 

   - Gdy wersja obrazu w portalu Marketplace jest taka sama jak obrazu w zestawie skalowania: Usuń obraz, który jest używany w zestawie skalowania, a następnie Pobierz nowy obraz. W czasie między usunięcie oryginalnego obrazu i pobieranie obrazu nie skalowanie w górę. 
      
     Proces ten jest wymagany do resyndicate obrazy, wchodzące używają formatu pliku rozrzedzonego, wprowadzonym w wersji 1803. 
 

2. Szablon wdrożenia zestawu skalowania maszyn wirtualnych **nie określa najnowsza wersja** dla *wersji* i zamiast tego Określa numer wersji:  

    Jeśli pobierzesz obraz za pomocą nowszej wersji (co powoduje zmianę dostępnej wersji), zestaw skalowania nie można skalować w górę. Jest to celowe wersję obrazu określonego w szablonie zestawu skalowania muszą być dostępne.  

Aby uzyskać więcej informacji, zobacz [dyski systemu operacyjnego i obrazy](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="scale-a-virtual-machine-scale-set"></a>Skalowanie zestawu skalowania maszyn wirtualnych
Możesz skalować rozmiar *zestawu skalowania maszyn wirtualnych* być większy lub mniejszy.  

1. W portalu, wybierz zestaw skalowania, a następnie wybierz pozycję **skalowanie**.
2. Użyj paska suwaka, aby ustawić nowy poziom skalowania dla tego zestawu skalowania maszyn wirtualnych, a następnie wybierz **Zapisz**.
     ![Zestaw skalowania](media/azure-stack-compute-add-scalesets/scale.png)





## <a name="remove-a-virtual-machine-scale-set"></a>Usuń zestaw skalowania maszyn wirtualnych

Aby usunąć maszynę wirtualną elementu galerii w zestawie skalowania, uruchom następujące polecenie programu PowerShell:

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> Nie można usunąć natychmiast elementu galerii. Noc musisz odświeżyć portal kilka razy, zanim element będzie wyświetlany jako usunięty z witryny Marketplace.

## <a name="next-steps"></a>Kolejne kroki
[Często zadawane pytania dotyczące usługi Azure Stack](azure-stack-faq.md)