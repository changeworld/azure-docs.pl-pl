---
title: Przenoszenie maszyn wirtualnych usług AWS Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś wystąpienie Windows usługi EC2 usług Amazon Web Services (AWS) na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: d6a4c5b2b6d9818dffdb1c1fee8c4c0df7cad77c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61456210"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Przenoszenie maszyny Wirtualnej z systemem Windows z usługi Amazon Web Services (AWS) na maszynie wirtualnej platformy Azure

Jeśli dokonujesz oceny maszyn wirtualnych platformy Azure do hostowania obciążeń, można wyeksportować istniejącego wystąpienia maszyny Wirtualnej Windows usługi EC2 usług Amazon Web Services (AWS), następnie przekazywanie wirtualnego dysku twardego (VHD) na platformę Azure. Po przekazaniu plików wirtualnego dysku twardego można utworzyć nową maszynę Wirtualną na platformie Azure z dysku VHD. 

W tym artykule opisano, przejście na jednej maszynie Wirtualnej od usług AWS na platformę Azure. Jeśli chcesz przenieść maszyny wirtualne od usług AWS na platformę Azure na dużą skalę, zobacz [migracji maszyn wirtualnych w Amazon Web Services (AWS) na platformie Azure przy użyciu usługi Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej 
 
Możesz przekazać zarówno uogólniony, wyspecjalizowana wirtualnych dysków twardych na platformie Azure. Każdy typ wymaga, aby przygotować maszyny Wirtualnej przed eksportem od usług AWS. 

- **Uogólniony wirtualny dysk twardy** — uogólnionego wirtualnego dysku twardego miał wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. Jeśli zamierzasz używać wirtualnego dysku twardego jako obraz, aby tworzyć nowe maszyny wirtualne z, należy: 
 
    * [Przygotowanie Windows VM](prepare-for-upload-vhd-image.md).  
    * Uogólnianie maszyny wirtualnej za pomocą programu Sysprep.  

 
- **Wyspecjalizowane wirtualnego dysku twardego** -wyspecjalizowanego wirtualnego dysku twardego przechowuje konta użytkowników, aplikacji i inne dane o stanie z oryginalną maszynę Wirtualną. Jeśli zamierzasz używać wirtualnego dysku twardego jako — jest, aby utworzyć nową maszynę Wirtualną, upewnij się, są wykonywane następujące czynności.  
    * [Przygotowywanie wirtualnego dysku twardego Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). **Nie** uogólnianie maszyny Wirtualnej za pomocą programu Sysprep. 
    * Usuń wszelkie gościa wirtualizacji narzędzi i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware). 
    * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania adresu IP i ustawienia DNS za pośrednictwem protokołu DHCP. Daje to gwarancję, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania.  


## <a name="export-and-download-the-vhd"></a>Eksportowanie i Pobierz dysk VHD 

Eksportuj wystąpienia usługi EC2 do wirtualnego dysku twardego w zasobniku Amazon S3. Postępuj zgodnie z instrukcjami w artykule dokumentacji Amazon [eksportowanie wystąpienia jako maszyny Wirtualnej przy użyciu maszyn wirtualnych Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) i uruchom [Tworzenie wystąpienia — export zadań](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) polecenie, aby wyeksportować wystąpienia usługi EC2 z plikiem wirtualnego dysku twardego. 

Wyeksportowany plik wirtualnego dysku twardego jest zapisywany w zasobniku Amazon S3, które określisz. Podstawowa składnia dla eksportowanie wirtualny dysk twardy znajduje się poniżej, wystarczy zastąpić tekst symbolu zastępczego w \<nawiasy kwadratowe > z informacjami.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po wyeksportowaniu wirtualny dysk twardy, postępuj zgodnie z instrukcjami [jak pobrać obiekt z przedział S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) można pobrać pliku wirtualnego dysku twardego z przedział S3. 

> [!IMPORTANT]
> Dane opłat za usługi AWS transferu opłatom za pobieranie wirtualnego dysku twardego. Zobacz [Amazon S3 ceny](https://aws.amazon.com/s3/pricing/) Aby uzyskać więcej informacji.


## <a name="next-steps"></a>Kolejne kroki

Możesz teraz przekazanie dysku VHD na platformie Azure i Utwórz nową maszynę Wirtualną. 

- W przypadku uruchomienia programu Sysprep na źródła **generalize** go przed eksportem, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i użyć go do utworzenia nowych maszyn wirtualnych na platformie Azure](upload-generalized-managed.md)
- Jeśli nie uruchomiono narzędzia Sysprep przed wyeksportowaniem, wirtualny dysk twardy jest uważany za **wyspecjalizowane**, zobacz [przekazywanie wyspecjalizowanego wirtualnego dysku twardego do systemu Azure i Utwórz nową maszynę Wirtualną](create-vm-specialized.md)

 
