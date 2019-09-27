---
title: Przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać dysk VHD do dysku zarządzanego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: bc8932a9904a3e4e671edc3e624ff15e7253e1ed
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326819"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób przekazywania dysku VHD z komputera lokalnego do dysku zarządzanego platformy Azure. Wcześniej musiałeś postępować zgodnie z większym procesem, który obejmuje przemieszczenie danych na konto magazynu, i zarządzanie tym kontem magazynu. Teraz nie trzeba już zarządzać kontem magazynu ani przemieszczać danych w celu przekazania dysku VHD. Zamiast tego należy utworzyć pusty dysk zarządzany i bezpośrednio przekazać do niego dysk VHD. Upraszcza to przekazywanie lokalnych maszyn wirtualnych na platformę Azure i umożliwia przekazywanie wirtualnego dysku twardego do 32 TiB bezpośrednio do dysku zarządzanego.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie funkcji bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. W przypadku przekazywania wirtualnego dysku twardego z maszyny zewnętrznej na platformę Azure szybkości z zależą od przepustowości lokalnej. W przypadku korzystania z maszyny wirtualnej platformy Azure przepustowość będzie taka sama jak standardowa HDD.

Obecnie bezpośrednie przekazywanie jest obsługiwane dla standardowego dysku twardego, standardowego SSD i dysków zarządzanych w warstwie Premium SSD. Nie jest jeszcze obsługiwane dla Ultra dysków SSD.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Plik VHD zapisany lokalnie

## <a name="create-an-empty-managed-disk"></a>Utwórz pusty dysk zarządzany

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany, który jest skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z tych dysków należy zapoznać się z dodatkowymi informacjami.

Ten rodzaj dysku zarządzanego ma dwa unikatowe Stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odebrania przekazania, ale nie Wygenerowano [sygnatury bezpiecznego dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, co oznacza, że dysk jest gotowy do odebrania przekazywania i Wygenerowano sygnaturę dostępu współdzielonego.

W każdym z tych stanów dysk zarządzany jest rozliczany zgodnie ze [standardowymi cenami](https://azure.microsoft.com/pricing/details/managed-disks/)dysków twardych, niezależnie od rzeczywistego typu dysku. Na przykład P10 będzie rozliczany jako S10. Ta wartość będzie prawdziwa do `revoke-access` momentu wywołania na dysku zarządzanym, który jest wymagany w celu dołączenia dysku do maszyny wirtualnej.

Przed utworzeniem pustego standardowego dysku twardego do przekazania należy mieć rozmiar pliku wirtualnego dysku twardego, który ma zostać przekazany, w bajtach. Aby to zrobić, możesz użyć opcji `wc -c <yourFileName>.vhd` lub `ls -al <yourFileName>.vhd`. Ta wartość jest używana podczas określania parametru **--upload-size-Bytes** .

Tworzenie pustego standardowego dysku twardego do przekazywania przez określenie zarówno parametru **--for-upload** , jak i parametru **--upload-size-Bytes** w poleceniu cmdlet [Create Disk](/cli/azure/disk#az-disk-create) :

```azurecli-interactive
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przekazać dysk SSD Premium lub standardowy dysk SSD, Zastąp **standard_lrs** wartością **premium_LRS** lub **standardssd_lrs**. SSD w warstwie Ultra nie jest jeszcze obsługiwana.

Utworzono pusty dysk zarządzany skonfigurowany dla procesu przekazywania. Aby przekazać dysk VHD do dysku, musisz mieć zapisywalne sygnatury dostępu współdzielonego, aby można było odwołać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalne SAS pustego dysku zarządzanego, użyj następującego polecenia:

```azurecli-interactive
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Przykładowa zwracana wartość:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego dla polecenia przekazywania.

Za pomocą AzCopy v10 Przekaż swój lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu współdzielonego.

To przekazywanie ma taką samą przepływność jak odpowiednik [standardowego dysku twardego](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który jest równa S4, będziesz mieć przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który jest równa S70, będziesz mieć przepływność do 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Jeśli Twoje sygnatury dostępu współdzielonego wygasną podczas przekazywania `revoke-access` , a jeszcze nie wywołano, możesz uzyskać nowe sygnatury dostępu `grant-access`współdzielonego, aby kontynuować przekazywanie przy użyciu programu.

Po zakończeniu przekazywania i nie musisz już pisać więcej danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwoływanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

```azurecli-interactive
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wirtualny dysk twardy został pomyślnie przekazany do dysku zarządzanego, możesz dołączyć dysk do maszyny wirtualnej i zacząć z niego korzystać.

Aby dowiedzieć się, jak dołączyć dysk do maszyny wirtualnej, zapoznaj się z artykułem na temat: [Dodaj dysk do maszyny wirtualnej z systemem Linux](add-disk.md).
