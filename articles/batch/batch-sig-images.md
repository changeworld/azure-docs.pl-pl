---
title: Tworzenie puli niestandardowej — Usługa Azure Batch | Dokumenty firmy Microsoft
description: Utwórz pulę partii z Galerią obrazów udostępnionych, aby aprowizować obrazy niestandardowe do węzłów obliczeniowych zawierających oprogramowanie i dane potrzebne dla aplikacji. Obrazy niestandardowe są skutecznym sposobem konfigurowania węzłów obliczeniowych do uruchamiania obciążeń usługi Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 95f27d913cd288c186bae1a6375212b072f50bb4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422433"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Tworzenie puli niestandardowej za pomocą Galerii obrazów udostępnionych

Podczas tworzenia puli usługi Azure Batch przy użyciu konfiguracji maszyny wirtualnej, należy określić obraz maszyny wirtualnej, który zapewnia system operacyjny dla każdego węzła obliczeniowego w puli. Można utworzyć pulę maszyn wirtualnych za pomocą obsługiwanego obrazu portalu Azure Marketplace lub utworzyć obraz niestandardowy za pomocą [galerii obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Zalety galerii obrazów udostępnionych

Korzystając z galerii obrazów udostępnionych dla obrazu niestandardowego, masz kontrolę nad typem i konfiguracją systemu operacyjnego, a także nad typem dysków z danymi. Obraz udostępniony może zawierać aplikacje i dane referencyjne, które stają się dostępne we wszystkich węzłach puli usługi Batch, gdy tylko zostaną udostępnione.

W zależności od środowiska można również mieć wiele wersji obrazu. Podczas tworzenia maszyny Wirtualnej używanej przez wersję obrazu wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej.

Za pomocą obrazu udostępnionego oszczędza czas w przygotowaniu węzłów obliczeniowych puli do uruchomienia obciążenia usługi Batch. Istnieje możliwość użycia obrazu portalu Azure Marketplace i zainstalowania oprogramowania w każdym węźle obliczeniowym po inicjowaniu obsługi administracyjnej, ale przy użyciu obrazu udostępnionego jest zazwyczaj bardziej wydajne. Ponadto można określić wiele replik dla obrazu udostępnionego, więc podczas tworzenia pul z wieloma maszynami wirtualnymi (więcej niż 600 maszyn wirtualnych), można zaoszczędzić czas podczas tworzenia puli.

Użycie obrazu udostępnionego skonfigurowanego dla scenariusza może zapewnić kilka zalet:

* **Użyj tych samych obrazów w różnych regionach.** Repliki obrazów udostępnionych można tworzyć w różnych regionach, aby wszystkie pule korzystały z tego samego obrazu.
* **Skonfiguruj system operacyjny (OS).** Można dostosować konfigurację dysku systemu operacyjnego obrazu.
* **Preinstaluj aplikacje.** Wstępna instalacja aplikacji na dysku systemu operacyjnego jest bardziej wydajna i mniej podatna na błędy niż instalowanie aplikacji po zainicjowaniu obsługi administracyjnej węzłów obliczeniowych za pomocą zadania początkowego.
* **Skopiuj duże ilości danych raz.** Wydziel dane statyczne częścią zarządzanego obrazu udostępnionego, kopiując go na dyski danych obrazu zarządzanego. To tylko musi być wykonane raz i udostępnia dane do każdego węzła puli.
* **Rozwijaj baseny do większych rozmiarów.** Dzięki Galerii obrazów udostępnionych można tworzyć większe pule z dostosowanymi obrazami wraz z większą liczoną repliką obrazów udostępnionych.
* **Lepsza wydajność niż obraz niestandardowy.** Przy użyciu obrazów udostępnionych czas potrzebny do puli, aby osiągnąć stan stacjonarny jest do 25% szybsze, a opóźnienie bezczynności maszyny Wirtualnej jest do 30% krótszy.
* **Przechowywanie wersji obrazu i grupowanie w celu łatwiejszego zarządzania.** Definicja grupowania obrazów zawiera informacje o tym, dlaczego obraz został utworzony, do jakiego systemu operacyjnego jest przeznaczony, oraz informacje o używaniu obrazu. Grupowanie obrazów ułatwia zarządzanie obrazami. Aby uzyskać więcej informacji, zobacz [Definicje obrazów](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Musisz uwierzytelnić przy użyciu usługi Azure AD. Jeśli używasz shared-key-auth, pojawi się błąd uwierzytelniania.  

* **Konto usługi Azure Batch.** Aby utworzyć konto usługi Batch, zobacz Szybki start usługi Batch przy użyciu [witryny Azure portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

* **Obraz galerii obrazów udostępnionych**. Aby utworzyć obraz udostępniony, musisz mieć lub utworzyć zasób obrazu zarządzanego. Obraz powinien być tworzony z migawek dysku systemu operacyjnego maszyny Wirtualnej i opcjonalnie jego dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [Przygotowywanie obrazu zarządzanego](#prepare-a-managed-image).

> [!NOTE]
> Obraz udostępniony musi znajdować się w tej samej subskrypcji co konto usługi Batch. Obraz udostępniony może znajdować się w różnych regionach, o ile ma repliki w tym samym regionie co konto usługi Batch.

## <a name="prepare-a-managed-image"></a>Przygotowywanie obrazu zarządzanego

Na platformie Azure można przygotować zarządzany obraz z:

* Migawki systemu operacyjnego i dysków danych maszyny Wirtualnej platformy Azure
* Uogólniona maszyna wirtualna platformy Azure z dyskami zarządzanymi
* Uogólniony lokalny dysk VHD przesłany do chmury

Aby skalować pule usługi Batch niezawodnie przy użyciu obrazu niestandardowego, zaleca się utworzenie obrazu zarządzanego przy użyciu *tylko* pierwszej metody: przy użyciu migawek dysków maszyny Wirtualnej. Zobacz następujące kroki, aby przygotować maszynę wirtualną, zrobić migawkę i utworzyć obraz z migawki.

### <a name="prepare-a-vm"></a>Przygotowywanie maszyny Wirtualnej

Jeśli tworzysz nową maszynę wirtualną dla obrazu, użyj obrazu pierwszej strony portalu Azure Marketplace obsługiwanego przez usługę Batch jako obrazu podstawowego dla obrazu zarządzanego. Tylko obrazy pierwszej strony mogą być używane jako obraz podstawowy. Aby uzyskać pełną listę odwołań do obrazów usługi Azure Marketplace obsługiwanych przez usługę Azure Batch, zobacz operację [jednostek SKU agenta węzła listy.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Nie można użyć obrazu innej firmy, który ma dodatkowe warunki licencji i zakupu jako obrazu podstawowego. Aby uzyskać informacje na temat tych obrazów w portalu Marketplace, zobacz wskazówki dotyczące maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) lub [Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

* Upewnij się, że maszyna wirtualna jest tworzona z dyskiem zarządzanym. Jest to domyślne ustawienie magazynu podczas tworzenia maszyny Wirtualnej.
* Nie należy instalować rozszerzeń platformy Azure, takich jak rozszerzenie skryptu niestandardowego, na maszynie wirtualnej. Jeśli obraz zawiera wstępnie zainstalowane rozszerzenie, platforma Azure może napotkać problemy podczas wdrażania puli usługi Batch.
* Podczas korzystania z dołączonych dysków danych, należy zainstalować i sformatować dyski z wewnątrz maszyny Wirtualnej, aby z nich korzystać.
* Upewnij się, że obraz systemu operacyjnego, który podasz, używa domyślnego dysku tymczasowego. Agent węzła batch obecnie oczekuje domyślnego dysku tymczasowego.
* Gdy maszyna wirtualna jest uruchomiona, połącz się z nią za pośrednictwem protokołu RDP (dla windows) lub SSH (dla systemu Linux). Zainstaluj niezbędne oprogramowanie lub skopiuj żądane dane.  

### <a name="create-a-vm-snapshot"></a>Tworzenie migawki maszyny Wirtualnej

Migawka jest pełną, tylko do odczytu kopię dysku VHD. Aby utworzyć migawkę systemu operacyjnego maszyny Wirtualnej lub dysków z danymi, można użyć witryny Azure portal lub narzędzi wiersza polecenia. Aby uzyskać kroki i opcje tworzenia migawki, zobacz wskazówki dotyczące maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) lub [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Tworzenie obrazu z jednej lub więcej migawek

Aby utworzyć obraz zarządzany z migawki, użyj narzędzi wiersza polecenia platformy Azure, takich jak polecenie [tworzenie obrazu az.](/cli/azure/image) Utwórz obraz, określając migawkę dysku systemu operacyjnego i opcjonalnie jedną lub więcej migawek dysku danych.

### <a name="create-a-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Po pomyślnym utworzeniu zarządzanego obrazu należy utworzyć galerię obrazów udostępnionych, aby udostępnić obraz niestandardowy. Aby dowiedzieć się, jak utworzyć galerię obrazów udostępnionych dla obrazów, zobacz [Tworzenie galerii obrazów udostępnionych za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/shared-images.md) lub Tworzenie [galerii obrazów udostępnionych przy użyciu portalu Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Tworzenie puli na podstawie obrazu udostępnionego przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć pulę z obrazu udostępnionego `az batch pool create` przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia. Określ identyfikator udostępnionego `--image` obrazu w tym polu. Upewnij się, że typ systemu operacyjnego i jednostka SKU są zgodne z wersjami określonymi`--node-agent-sku-id`

> [!NOTE]
> Musisz uwierzytelnić przy użyciu usługi Azure AD. Jeśli używasz shared-key-auth, pojawi się błąd uwierzytelniania.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Tworzenie puli na podstawie obrazu udostępnionego przy użyciu języka C #

Alternatywnie można utworzyć pulę z udostępnionego obrazu przy użyciu SDK języka C#.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Tworzenie puli na podstawie obrazu udostępnionego przy użyciu portalu Azure

Poniższe kroki można wykonać, aby utworzyć pulę z obrazu udostępnionego w witrynie Azure portal.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do **konta usługi Batch** i wybierz swoje konto.
1. Wybierz **pozycję Pule,** a następnie **dodaj,** aby utworzyć nową pulę.
1. W sekcji **Typ obrazu** wybierz pozycję Shared **Image Gallery**.
1. Uzupełnij pozostałe sekcje informacjami o zarządzanym obrazie.
1. Kliknij przycisk **OK**.

![Utwórz pulę z udostępnionego obrazu z portalu.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych basenów

Jeśli planujesz utworzyć pulę z setkami lub tysiącami maszyn wirtualnych lub więcej przy użyciu obrazu udostępnionego, użyj następujących wskazówek.

* **Numery replik galerii obrazów udostępnionych.**  Dla każdej puli z maksymalnie 600 wystąpień zaleca się zachować co najmniej jedną replikę. Na przykład w przypadku tworzenia puli z 3000 maszyn wirtualnych, należy zachować co najmniej 5 replik obrazu. Zawsze zalecamy zachowanie większej liczby replik niż minimalne wymagania dla lepszej wydajności.

* **Przeliń limitu czasu rozmiaru.** Jeśli pula zawiera stałą liczbę węzłów (jeśli nie jest `resizeTimeout` skalowana automatycznie), zwiększ właściwość puli w zależności od rozmiaru puli. Na każde 1000 maszyn wirtualnych zalecany limit czasu rozmiaru zmieniania wynosi co najmniej 15 minut. Na przykład zalecany limit czasu rozmiaru rozmiaru dla puli z 2000 maszyn wirtualnych wynosi co najmniej 30 minut.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).
