---
title: Utwórz zasoby techniczne dla oferty maszyny wirtualnej w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak utworzyć zasoby techniczne dla oferty maszyny wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 6f1a93c3d3059e612d8c309b263e263dbb84c67f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050105"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Utwórz zasoby techniczne dla oferty maszyny wirtualnej

W tej sekcji opisano proces tworzenia i konfigurowania zasoby techniczne dla oferty maszyny wirtualnej (VM) w portalu Azure Marketplace.  Maszyna wirtualna zawiera dwa składniki: rozwiązanie wirtualnego dysku twardego (VHD) i opcjonalnych skojarzone dysków danych.  

- *Wirtualne dyski twarde (VHD)*, zawierający system operacyjny i rozwiązania, zostanie wdrożony z ofertą Azure Marketplace. Proces przygotowywania wirtualnego dysku twardego, który różni się w zależności od tego, czy jest oparty na systemie Linux, systemem Windows lub maszyny Wirtualnej na podstawie niestandardowego.
- *Dyski z danymi* dedykowane, trwałe reprezentować pamięć masową dla maszyny wirtualnej. Czy *nie* korzystanie z odpowiedniego rozwiązania wirtualnego dysku twardego (na przykład `C:` dysku) do przechowywania informacji o trwałych.

Obraz maszyny Wirtualnej zawiera dysk z jednego systemu operacyjnego i zero lub więcej dysków danych. Jeden wirtualny dysk twardy jest wymagany na dysku. Puste dyski z danymi wymagają wirtualny dysk twardy ma zostać utworzony.
Należy skonfigurować system operacyjny maszyny Wirtualnej, rozmiar maszyny Wirtualnej, portów, otwieranych i maksymalnie 15 dołączonymi dyskami danych.

> [!TIP] 
> Niezależnie od używanego systemu operacyjnego należy dodać minimalną liczbę dysków z danymi, które są wymagane przez jednostkę SKU. Klienci nie mogą usuwać dysków, które należą do obrazu w czasie wdrażania, ale są zawsze dodawaj dysków podczas lub po wdrożeniu. 

> [!IMPORTANT]
> *Nie zmieniaj liczba dysków w nowej wersji obrazu.* Jeśli musisz ponownie skonfigurować dyski z danymi w obrazie, należy zdefiniować nowej jednostki SKU. Publikowanie nowej wersji obrazu z dużymi liczbami inny dysk będzie miał potencjał istotne nowe wdrożenie w oparciu o nową wersję obrazu w przypadku automatycznego skalowania, automatyczne wdrożeń rozwiązań za pomocą szablonów usługi Azure Resource Manager i innych scenariuszy.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Podstawową wiedzę techniczną

Projektowania, tworzenia i testowania te zasoby potrwać i wymaga wiedzę techniczną dotyczącą platformy Azure i technologii umożliwiających tworzenie oferty. Oprócz domenę rozwiązania zespołowi inżynierów powinien mieć wiedzy na następujących technologiach firmy Microsoft: 
-   Podstawową wiedzę na temat [usług platformy Azure](https://azure.microsoft.com/services/) 
-   Jak [projektowania i architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktyczną wiedzę na temat [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/), [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktyczną wiedzę na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Praktyczną wiedzę na temat [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Sugerowane narzędzia 

Wybierz jedną lub obie następujące środowiska skryptów ułatwiających zarządzanie maszyny wirtualne i wirtualne dyski twarde:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Ponadto zaleca się dodanie następujących narzędzi do swojego środowiska projektowego: 

-   [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Numer wewnętrzny: [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Numer wewnętrzny: [Upiększanie](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Numer wewnętrzny: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Także podpowiedzieć dostępne narzędzia w [narzędzi deweloperskich platformy Azure](https://azure.microsoft.com/tools/) strony i, jeśli używasz programu Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Kolejne kroki

Kolejne artykuły w tej sekcji opisano kroki tworzenia i rejestrowania te zasoby maszyny Wirtualnej:

1. [Utwórz wirtualny dysk twardy zgodnych z platformą Azure](./cpp-create-vhd.md) wyjaśnia sposób tworzenia, albo Linux lub Windows-na podstawie wirtualnego dysku twardego, który jest zgodny z platformą Azure.  Obejmuje ona najlepszych rozwiązań, takich jak zmiany rozmiaru, poprawiania i przygotowywanie maszyny Wirtualnej do przekazywania.

2. [Łączenie z maszyną wirtualną](./cpp-connect-vm.md) wyjaśnia, jak zdalnie połączyć się do nowo utworzonej maszyny Wirtualnej i zaloguj się do niego.  Również w tym artykule wyjaśniono, jak zatrzymać maszynę Wirtualną, aby zmniejszyć koszty użycia.

3. [Skonfiguruj maszynę wirtualną](./cpp-configure-vm.md) wyjaśnia, jak wybrać odpowiedni rozmiar wirtualnego dysku twardego, Uogólnij obraz, zastosuj najnowsze aktualizacje (poprawek) oraz planować konfiguracje niestandardowe.

4. [Wdróż maszynę wirtualną z wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md) wyjaśnia, jak zarejestrować Maszynę wirtualną z wirtualnego dysku twardego wdrożonych przez usługę Azure.  Wyświetla listę wymagane narzędzia i jak ich używać do tworzenia obrazu maszyny Wirtualnej użytkownika, a następnie wdrożyć ją na platformie Azure przy użyciu [portalu Microsoft Azure](https://ms.portal.azure.com/) lub skryptów programu PowerShell. 

5. [Certyfikowanie obraz maszyny wirtualnej](./cpp-certify-vm.md) opisano sposób testowania i przesłać obraz maszyny Wirtualnej do certyfikacji w portalu Azure Marketplace. Wyjaśnia, skąd uzyskać *narzędzie Test certyfikacji do certyfikatu platformy Azure* narzędzie i jak certyfikować swój obraz maszyny Wirtualnej za pomocą tego narzędzia. 

6. [Pobierz identyfikator URI sygnatury dostępu Współdzielonego](./cpp-get-sas-uri.md) wyjaśniono sposób uzyskiwania sygnatury dostępu współdzielonego (SAS) identyfikator URI dla usługi obrazów maszyn wirtualnych.
 
Jako pomocnicze artykuł [typowych udostępnionych problemów dotyczących adresu URL sygnatury dostępu](./cpp-common-sas-url-issues.md) wymieniono niektóre typowe problemy, które mogą wystąpić przy użyciu identyfikatorów URI sygnatury dostępu Współdzielonego i odpowiednie możliwych rozwiązań.

Po wykonaniu wszystkich tych kroków, wszystko będzie gotowe do [publikowanie oferty maszyny Wirtualnej](./cpp-publish-offer.md) w portalu Azure Marketplace.
