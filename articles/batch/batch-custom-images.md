---
title: Udostępnianie puli Azure Batch z obrazu niestandardowego | Microsoft Docs
description: Utwórz pulę usługi Batch z obrazu niestandardowego, aby udostępnić węzły obliczeniowe zawierające oprogramowanie i dane potrzebne aplikacji. Obrazy niestandardowe są wydajnym sposobem konfigurowania węzłów obliczeniowych do uruchamiania obciążeń wsadowych.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/07/2019
ms.author: lahugh
ms.openlocfilehash: d8bda817231ec0a5a733d5e586e49639c62ea177
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882841"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Używanie obrazu niestandardowego do tworzenia puli maszyn wirtualnych

Podczas tworzenia puli Azure Batch przy użyciu konfiguracji maszyny wirtualnej należy określić obraz maszyny wirtualnej, który dostarcza system operacyjny dla każdego węzła obliczeniowego w puli. Można utworzyć pulę maszyn wirtualnych z obsługiwanym obrazem witryny Azure Marketplace lub utworzyć niestandardowy obraz za pomocą [galerii obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Zalety galerii obrazów udostępnionych

Gdy używasz galerii obrazów udostępnionych dla niestandardowego obrazu, masz kontrolę nad typem i konfiguracją systemu operacyjnego, a także typem dysków danych. Udostępniony obraz może zawierać aplikacje i dane referencyjne, które stają się dostępne we wszystkich węzłach puli partii, gdy tylko zostaną zainicjowane.

Istnieje również możliwość użycia wielu wersji obrazu w zależności od potrzeb danego środowiska. W przypadku tworzenia maszyny wirtualnej przy użyciu wersji obrazu, wersja obrazu służy do tworzenia nowych dysków dla maszyny wirtualnej. 

Za pomocą udostępnionego obrazu można zaoszczędzić czas podczas przygotowywania węzłów obliczeniowych puli do uruchamiania obciążenia usługi Batch. Istnieje możliwość użycia obrazu portalu Azure Marketplace i zainstalowania oprogramowania w każdym węźle obliczeniowym po zainicjowaniu obsługi, ale użycie obrazu udostępnionego jest zwykle bardziej wydajne. Dodatkowo można określić wiele replik dla obrazu udostępnionego, tak aby podczas tworzenia pul z wieloma maszynami wirtualnymi (więcej niż 600 maszyn wirtualnych) zaoszczędzić czas podczas tworzenia puli.

Używanie udostępnionego obrazu skonfigurowanego dla danego scenariusza może zapewnić kilka korzyści:

* **Używaj tych samych obrazów w różnych regionach.** Można utworzyć udostępnione repliki obrazów w różnych regionach, aby wszystkie pule używały tego samego obrazu.
* **Skonfiguruj system operacyjny (OS).** Można dostosować konfigurację dysku systemu operacyjnego obrazu.
* **Aplikacje przed instalacją.** Wstępne Instalowanie aplikacji na dysku systemu operacyjnego jest wydajniejsze i mniej podatne na błędy niż instalowanie aplikacji po zainicjowaniu obsługi administracyjnej węzłów obliczeniowych za pomocą zadania podrzędnego.
* **Kopiuj duże ilości danych raz.** Utwórz statyczny element danych w zarządzanym obrazie udostępnionym, kopiując go do dysków danych w zarządzanym obrazie. Należy to zrobić tylko raz i udostępnić dane dla każdego węzła puli.
* **Zwiększaj pule do większych rozmiarów.** Za pomocą galerii obrazów udostępnionych można tworzyć większe pule przy użyciu dostosowanych obrazów wraz z bardziej udostępnionymi replikami obrazu.
* **Lepsza wydajność niż obraz niestandardowy.** Przy użyciu obrazów udostępnionych czas trwania puli do osiągnięcia stanu stałego jest do 25% szybszy, a opóźnienie maszyny wirtualnej jest krótsze niż 30%.
* **Przechowywanie wersji obrazów i grupowanie w celu łatwiejszego zarządzania.** Definicja grupowania obrazów zawiera informacje na temat przyczyny utworzenia obrazu, jego systemu operacyjnego oraz informacji o korzystaniu z obrazu. Grupowanie obrazów umożliwia łatwiejsze zarządzanie obrazami. Aby uzyskać więcej informacji, zobacz [definicje obrazu](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi Azure Batch.** Aby utworzyć konto usługi Batch, zobacz Przewodnik Szybki Start w usłudze Batch przy użyciu [Azure Portal](quick-create-portal.md) lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

* **Obraz udostępnionej galerii obrazów**. Aby uzyskać więcej informacji i kroków przygotowywania udostępnionego obrazu, zobacz [Tworzenie udostępnionej galerii obrazów za pomocą interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/shared-images.md) lub [Tworzenie galerii obrazów udostępnionych przy użyciu Azure Portal](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> Udostępniony obraz musi znajdować się w tej samej subskrypcji co konto usługi Batch. Udostępniony obraz może znajdować się w różnych regionach, o ile ma repliki w tym samym regionie, co konto w usłudze Batch.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć pulę na podstawie udostępnionego obrazu przy użyciu interfejsu wiersza `az batch pool create` polecenia platformy Azure, użyj polecenie. Określ identyfikator obrazu udostępnionego w `--image` polu. Upewnij się, że typ systemu operacyjnego i jednostka SKU są zgodne z wersjami określonymi przez`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Tworzenie puli na podstawie udostępnionego obrazu przy użyciuC#

Alternatywnie można utworzyć pulę z udostępnionego obrazu przy użyciu C# zestawu SDK.

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

## <a name="considerations-for-large-pools"></a>Zagadnienia dotyczące dużych pul

Jeśli planujesz utworzenie puli z setkami lub tysiącami maszyn wirtualnych lub więcej przy użyciu udostępnionego obrazu, Skorzystaj z poniższych wskazówek.

* **Numery replik galerii obrazów udostępnionych.**  W przypadku każdej puli z maksymalnie 600 wystąpieniami zalecamy zachowanie co najmniej jednej repliki. Na przykład, jeśli tworzysz pulę z 3000 maszyn wirtualnych, należy pozostawić co najmniej 5 replik obrazu. Zawsze sugerujemy przechowywanie większej liczby replik niż minimalne wymagania w celu uzyskania lepszej wydajności.

* **Limit czasu zmiany rozmiaru** Jeśli pula zawiera stałą liczbę węzłów (jeśli nie ma automatycznego skalowania), zwiększ `resizeTimeout` Właściwość puli w zależności od rozmiaru puli. Dla każdego 1000 maszyn wirtualnych zalecany limit rozmiaru wynosi co najmniej 15 minut. Na przykład zalecany limit rozmiaru dla puli z maszynami wirtualnymi 2000 wynosi co najmniej 30 minut.

## <a name="next-steps"></a>Następne kroki

* Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
