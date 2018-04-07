---
title: Przenoszenie maszyn wirtualnych systemu Windows usług AWS na platformie Azure | Dokumentacja firmy Microsoft
description: Przenieś wystąpienie EC2 Amazon Web Services (AWS) w systemie Windows na maszynach wirtualnych platformy Azure przy użyciu programu Azure PowerShell.
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
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 4b7e794cb08647dde6fe59b6d4b06a9cbfab06e1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Przenieś Maszynę wirtualną systemu Windows z Amazon Web Services (AWS) na platformie Azure przy użyciu programu PowerShell

Jeśli dokonujesz oceny maszyn wirtualnych platformy Azure do obsługi obciążeń, można wyeksportować istniejącego wystąpienia maszyny Wirtualnej systemu Windows EC2 Amazon Web Services (AWS). następnie przekazywanie wirtualnego dysku twardego (VHD) na platformie Azure. Po przekazaniu plików wirtualnego dysku twardego można utworzyć nowej maszyny Wirtualnej na platformie Azure z dysku VHD. 

W tym temacie omówiono przeniesienie jednej maszyny Wirtualnej z usług AWS na platformie Azure. Jeśli chcesz przenieść maszyny wirtualne z usług AWS Azure na dużą skalę, zobacz [Migrowanie maszyn wirtualnych w Amazon Web Services (AWS) na platformie Azure za pomocą usługi Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej 
 
Możesz przekazać zarówno ogólnych i specjalnych wirtualne dyski twarde na platformie Azure. Każdy typ wymaga, aby przygotować maszyny Wirtualnej przed wykonaniem operacji eksportowania z usług AWS. 

- **Uogólniony wirtualny dysk twardy** -uogólniony wirtualny dysk twardy ma wszystkie informacje osobiste Konto usunięte za pomocą programu Sysprep. Jeśli planujesz używać wirtualnego dysku twardego jako obraz do tworzenia nowych maszyn wirtualnych z, wykonaj następujące czynności: 
 
    * [Przygotowywanie maszyny Wirtualnej systemu Windows](prepare-for-upload-vhd-image.md).  
    * Generalize maszyny wirtualnej za pomocą programu Sysprep.  

 
- **Specjalizowany wirtualnego dysku twardego** -specjalne wirtualnego dysku twardego przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalnego maszyny Wirtualnej. Jeśli zamierzasz używać wirtualnego dysku twardego jako — jest, aby utworzyć nową maszynę Wirtualną, upewnij się, wykonywane są następujące czynności.  
    * [Przygotowywanie dysku VHD systemu Windows, aby przekazać do usługi Azure](prepare-for-upload-vhd-image.md). **Nie** generalize maszyny Wirtualnej za pomocą programu Sysprep. 
    * Usuń wszystkie narzędzia wirtualizacji gościa i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware). 
    * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania jego adres IP i ustawienia DNS za pośrednictwem protokołu DHCP. Daje to pewność, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania.  


## <a name="export-and-download-the-vhd"></a>Eksportowanie i Pobierz wirtualnego dysku twardego 

Wyeksportuj wystąpienia EC2 do dysku VHD w zasobniku Amazon S3. Wykonaj kroki opisane w temacie dokumentacji Amazon [eksportowanie wystąpienia jako maszyny Wirtualnej przy użyciu maszyny Wirtualnej importu/eksportu](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) i uruchom [tworzenie — wystąpienie export zadania](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) polecenie, aby wyeksportować do pliku VHD EC2 wystąpienia. 

Wyeksportowany plik wirtualnego dysku twardego jest zapisany w zasobniku Amazon S3, które określisz. Podstawowa składnia eksportowanie wirtualny dysk twardy jest poniżej, po prostu zastąpić tekst zastępczy w <brackets> z informacjami.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po wyeksportowaniu wirtualnego dysku twardego, postępuj zgodnie z instrukcjami [sposób pobrać obiektu z przedział S3?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) można pobrać pliku wirtualnego dysku twardego z przedział S3. 

> [!IMPORTANT]
> Usług AWS danych opłat za transfer opłat pobierania wirtualnego dysku twardego. Zobacz [Amazon S3 cennik](https://aws.amazon.com/s3/pricing/) Aby uzyskać więcej informacji.


## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przekazanie dysku VHD na platformę Azure i utworzyć nową maszynę Wirtualną. 

- W przypadku uruchomienia programu Sysprep na źródła **generalize** go przed wykonaniem operacji eksportowania, zobacz [przekazywanie uogólniony wirtualny dysk twardy i umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure](upload-generalized-managed.md)
- Jeśli nie zostało uruchomione narzędzie Sysprep przed wykonaniem operacji eksportowania, wirtualny dysk twardy jest traktowany jako **specjalne**, zobacz [przekazywanie specjalne wirtualnego dysku twardego na platformie Azure i utworzyć nową maszynę Wirtualną](create-vm-specialized.md)

 
