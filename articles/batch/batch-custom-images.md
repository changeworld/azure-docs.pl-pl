---
title: Aprowizowanie puli Azure Batch za pomocą niestandardowego obrazu | Dokumentacja firmy Microsoft
description: Tworzenie puli za pomocą niestandardowego obrazu dla aprowizacji funkcji obliczeniowych węzły, które zawierają oprogramowanie i dane potrzebne dla aplikacji usługi Batch. Obrazy niestandardowe są wydajny sposób konfigurowania węzłów obliczeniowych do uruchamiania obciążeń usługi Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 233b26b330fabe7da8664114ba1857f74feea4bc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764284"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Używanie niestandardowego obrazu, aby utworzyć pulę maszyn wirtualnych 

Podczas tworzenia puli usługi Azure Batch za pomocą konfiguracji maszyny wirtualnej, należy określić obraz maszyny Wirtualnej, który zawiera system operacyjny na każdym węźle obliczeniowym w puli. Można utworzyć puli maszyn wirtualnych, przy użyciu obsługiwanych obrazów portalu Azure Marketplace lub przy użyciu niestandardowego obrazu (obraz maszyny Wirtualnej zostanie utworzony i skonfigurowany samodzielnie). Niestandardowy obraz musi być *obrazu zarządzanego* zasobów w tej samej subskrypcji platformy Azure i regionie co konto usługi Batch.

## <a name="why-use-a-custom-image"></a>Dlaczego warto używać obrazu niestandardowego?

Jeśli podasz niestandardowego obrazu, masz kontrolę nad konfiguracji systemu operacyjnego i typ systemu operacyjnego i dysków z danymi ma być używany. Obraz niestandardowy może zawierać aplikacji i danych referencyjnych, które staną się dostępne we wszystkich węzłach puli usługi Batch, zaraz po ich udostępnieniu.

Przy użyciu niestandardowego obrazu zaoszczędzić czas, w ramach przygotowywania węzłów obliczeniowych w puli do uruchomienia obciążenia usługi Batch. Można użyć obrazu portalu Azure Marketplace i instalować oprogramowanie na każdym węźle obliczeniowym po zainicjowaniu obsługi administracyjnej, może być bardziej efektywne przy użyciu niestandardowego obrazu.

Przy użyciu niestandardowego obrazu skonfigurowany dla danego scenariusza można podać kilka dodatkowych korzyści:

- **Konfigurowanie systemu operacyjnego (OS)**. Można dostosować konfigurację dysku systemu operacyjnego obrazu. 
- **Przed rozpoczęciem instalacji aplikacji.** Przed instalacją aplikacje na dysku systemu operacyjnego, który jest bardziej wydajne i mniej podatne na błędy niż instalowania aplikacji po zainicjowaniu obsługi administracyjnej węzłów obliczeniowych za pomocą zadania uruchamiania.
- **Zapisz czas ponownego uruchomienia na maszynach wirtualnych.** Instalacja aplikacji zwykle wymaga ponownego uruchomienia maszyny Wirtualnej, czasochłonne. Aby zaoszczędzić czas ponownego uruchomienia, należy wstępnie instalowania aplikacji. 
- **Skopiuj jeden raz bardzo dużych ilości danych.** Należy dane statyczne część zarządzany obraz niestandardowy, kopiując go do dysków z danymi obrazu zarządzanego. To musi odbywać się jeden raz i tylko udostępnia dane w każdym węźle puli.
- **Wybór typów dysków.** Masz do wyboru używania usługi premium storage dla dysku systemu operacyjnego i dysk z danymi.
- **Zwiększanie puli do dużych rozmiarów.** Gdy używasz zarządzany obraz niestandardowy do tworzenia puli, pula można powiększać bez konieczności można utworzyć kopie obiektu blob obrazu vhd. 


## <a name="prerequisites"></a>Wymagania wstępne

- **Zasób obrazu zarządzanego**. Tworzenie puli maszyn wirtualnych przy użyciu niestandardowego obrazu, należy mieć lub utworzyć zasób obrazu zarządzanego w tej samej subskrypcji platformy Azure i regionie co konto usługi Batch. Obraz, który powinien zostać utworzony z migawki dysku systemu operacyjnego maszyny Wirtualnej i opcjonalnie jego dołączonych dysków z danymi. Aby uzyskać więcej informacji i czynności, aby przygotować do zarządzanego obrazu zobacz następującą sekcję. 
  - Użyj unikatowy obrazu niestandardowego dla każdej puli, który tworzysz.
  - Aby utworzyć pulę przy użyciu obrazu przy użyciu interfejsów API usługi Batch, należy określić **identyfikator zasobu** obrazu, który ma postać `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Aby korzystać z portalu, użyj **nazwa** obrazu.  
  - Zasób obrazu zarządzanego powinna istnieć okres istnienia puli, aby umożliwić skalowanie w górę i może zostać usunięta po usunięciu puli.

- **Uwierzytelnianie usługi Azure Active Directory (AAD)**. Interfejs API klienta usługi Batch, należy użyć uwierzytelniania usługi AAD. Obsługa usługi Azure Batch dla usługi AAD jest udokumentowany w [uwierzytelnianie rozwiązań usługi Batch service z usługą Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Przygotowanie obrazu niestandardowego

Na platformie Azure można przygotować obrazu zarządzanego z migawek systemu operacyjnego w maszynie Wirtualnej platformy Azure i dysków z danymi, uogólnionej maszyny Wirtualnej platformy Azure z dyskami zarządzanymi lub lokalnego uogólnionego wirtualnego dysku twardego przekazane. Aby skalować pule usługi Batch niezawodnie przy użyciu niestandardowego obrazu, zaleca się utworzenie obrazu zarządzanego przy użyciu *tylko* pierwszej metody: przy użyciu migawek dysków maszyny Wirtualnej. Zobacz poniższe kroki, aby przygotować Maszynę wirtualną, Utwórz migawkę i tworzenie obrazu na podstawie migawki. 

### <a name="prepare-a-vm"></a>Przygotowywanie maszyny Wirtualnej

W przypadku tworzenia nowej maszyny Wirtualnej dla obrazu, należy użyć pierwszego obrazu portalu Azure Marketplace innych firm obsługiwane przy użyciu usługi Batch jako obrazu podstawowego dla obrazu zarządzanego. Tylko obrazy firmy Microsoft może służyć jako obrazu podstawowego. Aby uzyskać pełną listę obsługiwanych przez usługę Azure Batch odwołań do obrazu portalu Azure Marketplace, zobacz [jednostki SKU agenta węzła listy](/rest/api/batchservice/account/listnodeagentskus) operacji.

> [!NOTE]
> Nie można użyć obrazu innych firm, który ma dodatkową licencję i warunki zakupu jako obrazu podstawowego. Aby uzyskać informacji na temat tych obrazów z witryny Marketplace, zobacz wskazówki dotyczące [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) lub [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) maszyn wirtualnych.


* Upewnij się, że maszyna wirtualna jest tworzona w przypadku dysków zarządzanych. To domyślne ustawienie magazynu podczas tworzenia maszyny Wirtualnej.
* Nie należy instalować rozszerzeń platformy Azure, takich jak rozszerzenie niestandardowego skryptu na maszynie Wirtualnej. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, Azure, mogą wystąpić problemy podczas wdrażania puli usługi Batch.
* Upewnij się, że podstawowy obraz systemu operacyjnego, których udzielasz używa domyślnego dysku tymczasowego. Agent węzłów usługi Batch oczekuje domyślnego dysku tymczasowego.
* Gdy maszyna wirtualna jest uruchomiona, się z nim za pośrednictwem protokołu RDP (dla Windows) lub SSH (dla systemu Linux). Zainstaluj oprogramowanie niezbędne lub skopiuj żądanych danych.  

### <a name="create-a-vm-snapshot"></a>Tworzenie migawki maszyny Wirtualnej

Migawka jest pełna, tylko do odczytu kopię dysku VHD. Aby utworzyć migawkę maszyny Wirtualnej systemu operacyjnego ani dysków z danymi, można użyć witryny Azure portal lub narzędzi wiersza polecenia. Kroki i opcje, aby utworzyć migawkę, znajdują się wskazówki dotyczące [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) lub [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) maszyn wirtualnych.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Tworzenie obrazu na podstawie co najmniej jednej migawki

Aby utworzyć obrazu zarządzanego z migawki, użyj narzędzia wiersza polecenia platformy Azure takie jak [utworzyć obraz az](/cli/azure/image) polecenia. Aby utworzyć obraz, określając migawki dysku systemu operacyjnego i opcjonalnie co najmniej jeden migawki dysków danych.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Tworzenie puli za pomocą niestandardowego obrazu w portalu

Po zapisaniu z obrazem niestandardowym i znasz jego nazwę lub identyfikator zasobu, należy utworzyć pulę usługi Batch z tego obrazu. Poniższe kroki pokazują sposób tworzenia puli w witrynie Azure portal.

> [!NOTE]
> W przypadku tworzenia puli przy użyciu jednego z interfejsów API usługi Batch, upewnij się, że tożsamości używany do uwierzytelniania usługi AAD ma uprawnienia do zasobu obrazu. Zobacz [uwierzytelnianie rozwiązań usługi Batch service z usługą Active Directory](batch-aad-auth.md).
>
> Zasób obrazu zarządzanego, musi istnieć przez okres istnienia puli. Jeśli bazowego zasobu zostanie usunięty, nie można skalować w puli. 

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi być w tej samej subskrypcji i regionie co grupa zasobów zawierająca obraz niestandardowy. 
2. W **ustawienia** oknie po lewej stronie, wybierz opcję **pule** elementu menu.
3. W **pule** wybierz **Dodaj** polecenia.
4. Na **Dodaj pulę** wybierz **obraz niestandardowy (Linux/Windows)** z **typ obrazu** listy rozwijanej. Z **niestandardowy obraz maszyny Wirtualnej** listy rozwijanej wybierz nazwę obrazu (krótka identyfikator zasobu).
5. Wybierz poprawnego **wydawcę/ofertę/jednostkę Sku** dla obrazu niestandardowego.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane**, i **niskim priorytecie węzłów**, jak również dowolne żądane ustawienia opcjonalne.

    Na przykład obraz niestandardowy program Microsoft Windows Server 2016 Datacenter **Dodaj pulę** okno pojawia się, jak pokazano poniżej:

    ![Dodaj pulę za pomocą niestandardowego obrazu Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Aby sprawdzić, czy istniejącej puli jest oparta na obrazach niestandardowych, zobacz **systemu operacyjnego** właściwości w sekcji podsumowania zasobów **puli** okna. Jeśli pula została utworzona na podstawie niestandardowego obrazu, jest równa **niestandardowego obrazu maszyny Wirtualnej**.

Wszystkie obrazy niestandardowe skojarzone z pulą są wyświetlane w puli **właściwości** okna.

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych pul

Jeśli użytkownik chce utworzyć pulę przy użyciu setek maszyn wirtualnych lub więcej przy użyciu niestandardowego obrazu, należy zgodnie z wytycznymi poprzedni, aby użyć obrazu utworzonego na podstawie migawki maszyny Wirtualnej.

Również pamiętać o następujących kwestiach:

- **Limity rozmiaru** — Batch ogranicza rozmiar puli 2500 dedykowanych węzłów obliczeniowych lub 1000 węzłów o niskim priorytecie, korzystając z niestandardowego obrazu.

  Jeśli używasz tego samego obrazu (lub wiele obrazów, w oparciu o tę samą migawkę bazowego) można utworzyć wiele pul, węzły obliczeniowe całkowitą w pulach nie może przekroczyć poprzedniego limitów. Nie zaleca się przy użyciu obrazu lub jego migawkę bazowego dla więcej niż jedną pulę.

  Limity, może zostać ograniczona, w przypadku skonfigurowania puli z [przychodzących pul NAT](pool-endpoint-configuration.md).

- **Limit czasu zmiany rozmiaru** — Jeśli pula zawiera stałą liczbę węzłów automatyczne skalowanie nie zwiększ właściwość resizeTimeout puli, aby wartością, taką jak 20 – 30 minutach. Jeśli pula nie dociera do rozmiaru docelowego przed upływem limitu czasu, przeprowadzenie [operacja zmiany rozmiaru](/rest/api/batchservice/pool/resize).

  Jeśli planujesz puli za pomocą ponad 300 węzłów obliczeniowych, może być konieczne zmiany rozmiaru puli wielokrotnie nawiązać rozmiar docelowy.

## <a name="considerations-for-using-packer"></a>Zagadnienia dotyczące za pomocą narzędzia Packer

Tworzenie obrazu zarządzanego zasobu bezpośrednio za pomocą usługi Packer może być przeprowadzone wyłącznie z konta usługi Batch w trybie subskrypcji użytkownika. W przypadku kont tryb usługi Batch, musisz najpierw utworzyć wirtualny dysk twardy, a następnie importowanie dysku VHD do zasobu obrazu zarządzanego. Zależności od trybu alokacji puli (subskrypcja użytkownika lub usługi Batch) kroki w taki sposób, aby utworzyć zasób obrazu zarządzanego będą się różnić.

Upewnij się, czy zasób umożliwiający utworzenie obrazu zarządzanego istnieje dla czasu istnienia każdej puli odwołuje się do niestandardowego obrazu. Niewykonanie tej czynności może powodować błędy alokacji puli i/lub zmienić rozmiar błędów. 

Jeśli obraz lub bazowego zasobu zostanie usunięty, użytkownik może wystąpić błąd podobny do: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. W takim przypadku upewnij się, że bazowego zasobu nie został usunięty.

Aby uzyskać więcej informacji na temat za pomocą narzędzia Packer, aby utworzyć Maszynę wirtualną, zobacz [budowania obrazu systemu Linux za pomocą usługi Packer](../virtual-machines/linux/build-image-with-packer.md) lub [budowania obrazu Windows za pomocą usługi Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).
