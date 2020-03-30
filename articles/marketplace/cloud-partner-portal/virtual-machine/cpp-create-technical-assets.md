---
title: Tworzenie zasobów technicznych dla oferty maszyny wirtualnej dla portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć zasoby techniczne dla oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: 57f56a341cfc3db6a5f0664503809e6ab6cf3d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278028"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Tworzenie zasobów technicznych dla oferty maszyny wirtualnej

W tej sekcji można przejść przez tworzenie i konfigurowanie zasobów technicznych dla oferty maszyny wirtualnej (VM) dla portalu Azure Marketplace.  Maszyna wirtualna zawiera dwa składniki: wirtualny dysk twardy rozwiązania (VHD) i opcjonalne skojarzone dyski danych.  

- *Wirtualne dyski twarde (VHD)*, zawierające system operacyjny i rozwiązanie, które można wdrożyć z ofertą portalu Azure Marketplace. Proces przygotowywania dysku wirtualnego różni się w zależności od tego, czy jest to maszyna wirtualna oparta na systemie Linux, windows, czy niestandardowa.
- *Dyski danych reprezentują dedykowany,* trwały magazyn dla maszyny wirtualnej. *Nie* używaj rozwiązania VHD (na `C:` przykład dysku) do przechowywania trwałych informacji.

Obraz maszyny Wirtualnej zawiera jeden dysk systemu operacyjnego i zero lub więcej dysków z danymi. Na dysk potrzebny jest jeden dysk WIRTUALNY. Nawet puste dyski danych wymagają utworzenia dysku VHD.
Należy skonfigurować system operacyjny maszyny Wirtualnej, rozmiar maszyny Wirtualnej, porty do otwarcia i maksymalnie 15 dołączonych dysków z danymi.

> [!TIP] 
> Niezależnie od używanego systemu operacyjnego dodaj tylko minimalną liczbę dysków danych potrzebnych przez jednostkę SKU. Klienci nie mogą usuwać dysków, które są częścią obrazu w czasie wdrażania, ale zawsze mogą dodawać dyski podczas lub po wdrożeniu. 

> [!IMPORTANT]
> *Nie należy zmieniać liczby dysków w nowej wersji obrazu.* Jeśli należy ponownie skonfigurować dyski danych na obrazie, zdefiniuj nową jednostkę SKU. Publikowanie nowej wersji obrazu z różnych liczników dysków będzie mieć potencjał podziału nowego wdrożenia na podstawie nowej wersji obrazu w przypadku automatycznego skalowania, automatyczne wdrażanie rozwiązań za pośrednictwem szablonów usługi Azure Resource Manager i innych scenariuszy.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty. Oprócz domeny rozwiązania zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft: 
-   Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/) 
-   Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-   Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i sieci [azure](https://azure.microsoft.com/services/?filter=networking)
-   Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Wiedza robocza [json](https://www.json.org/)


## <a name="suggested-tools"></a>Sugerowane narzędzia 

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie wirtualnymi i maszynami wirtualnymi:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zaleca się dodanie następujących narzędzi do środowiska programistycznego: 

-   [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Kod programu Visual Studio](https://code.visualstudio.com/)
    *   Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerujemy również przejrzenie dostępnych narzędzi na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) oraz, jeśli używasz programu Visual Studio, portalu Visual Studio [Marketplace.](https://marketplace.visualstudio.com/)


## <a name="next-steps"></a>Następne kroki

Kolejne artykuły w tej sekcji przenikają przez kroki tworzenia i rejestrowania tych zasobów maszyn wirtualnych:

1. [Utwórz wirtualny dysk twardy zgodny z platformą Azure](./cpp-create-vhd.md) wyjaśnia, jak utworzyć dysk VHD oparty na systemie Linux lub Windows, który jest zgodny z platformą Azure.  Zawiera najlepsze rozwiązania, takie jak zmiana rozmiaru, poprawki i przygotowanie maszyny Wirtualnej do przekazywania.

2. [Połącz się z maszyną wirtualną](./cpp-connect-vm.md) wyjaśniono, jak zdalnie połączyć się z nowo utworzoną maszyną wirtualną i zalogować się do niej.  W tym artykule wyjaśniono również, jak zatrzymać maszynę wirtualną, aby zaoszczędzić na kosztach użycia.

3. [Skonfiguruj maszynę wirtualną,](./cpp-configure-vm.md) wyjaśniając, jak wybrać odpowiedni rozmiar dysku VHD, uogólnić obraz, zastosować najnowsze aktualizacje (poprawki) i zaplanować konfiguracje niestandardowe.

4. [Wdrażanie maszyny wirtualnej z wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md) wyjaśnia, jak zarejestrować maszynę wirtualną z dysku wirtualnego wdrożonego w usłudze Azure.  Zawiera listę wymaganych narzędzi i jak ich używać do tworzenia obrazu maszyny Wirtualnej użytkownika, a następnie wdrożyć go na platformie Azure przy użyciu [witryny Microsoft Azure portal](https://ms.portal.azure.com/) lub skryptów programu PowerShell. 

5. [Certyfikuj obraz maszyny wirtualnej](./cpp-certify-vm.md) wyjaśnia, jak przetestować i przesłać obraz maszyny wirtualnej do certyfikacji usługi Azure Marketplace. Wyjaśniono, gdzie można uzyskać *narzędzie do testowania certyfikacji dla narzędzia z certyfikatem platformy Azure* i jak użyć tego narzędzia do certyfikacji obrazu maszyny Wirtualnej. 

6. [Pobierz identyfikator URI sygnatury dostępu](./cpp-get-sas-uri.md) Współdzielonego wyjaśnia, jak uzyskać identyfikator URI podpisu dostępu współdzielonego (SAS) dla obrazu maszyny Wirtualnej.
 
Jako artykuł pomocniczy [typowe problemy z adresem URL podpisu dostępu współdzielonego](./cpp-common-sas-url-issues.md) zawiera listę niektórych typowych problemów, które mogą wystąpić przy użyciu identyfikatorów URI sygnatury dostępu Współdzielonego i odpowiednich możliwych rozwiązań.

Po wykonaniu wszystkich tych kroków, będzie można opublikować [ofertę maszyny Wirtualnej](./cpp-publish-offer.md) do portalu Azure Marketplace.
