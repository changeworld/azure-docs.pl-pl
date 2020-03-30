---
title: Odzyskiwanie kontenera konta magazynu
description: Odzyskiwanie kontenera konta magazynu
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562279"
---
# <a name="storage-account-container-recovery"></a>Odzyskiwanie kontenera konta magazynu

Usługa Azure Storage zapewnia odporność danych za pośrednictwem replik automatycznych. Nie uniemożliwia to jednak kodowi aplikacji ani użytkownikom uszkadzania danych, przypadkowo lub złośliwie. Zachowanie wierności danych w obliczu błędu aplikacji lub użytkownika wymaga bardziej zaawansowanych technik, takich jak kopiowanie danych do dodatkowej lokalizacji magazynu za pomocą dziennika inspekcji.

## <a name="checking-azure-storage-account-type"></a>Sprawdzanie typu konta usługi Azure Storage

1. Przejdź do [witryny Azure portal](https://portal.azure.com/).

2. Odszukaj konto magazynu.

3. W sekcji **Przegląd** sprawdź, czy **nie ma replikacji**.

   ![Image (Obraz)](media/storage-account-container-recovery/1.png)

4. Jeśli typem replikacji jest **GRS/RA-GRS,** odzyskiwanie kontenera konta jest możliwe bez gwarancji. Dla wszystkich innych typów replikacji nie jest możliwe.

5. Zbierz następujące informacje i złóż żądanie pomocy technicznej za pomocą pomocy technicznej firmy Microsoft.

   * Nazwa konta magazynu:
   * Nazwa kontenera:
   * Czas usunięcia:

   Poniższa tabela zawiera omówienie zakresu odzyskiwania kontenerów kont magazynu w zależności od strategii replikacji.

   |Typ zawartości|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Kontener do przechowywania|Nie|Nie|Tak|Tak| 

   * Możemy spróbować przywrócić kontener konta magazynu, ale bez żadnej gwarancji. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Rzeczy, których nie należy robić, aby odzyskać, aby odnieść sukces

* Proszę nie odtwarzać kontenera (o tej samej nazwie).  
* Jeśli kontener został już odtworzony, należy usunąć kontener przed złożeniem żądania pomocy technicznej do odzyskania.

## <a name="steps-to-prevent-this-in-the-future"></a>Kroki, aby temu zapobiec w przyszłości

1. Aby tego uniknąć w przyszłości, najbardziej zalecaną funkcją jest [usuwanie trędowa .](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)

2. Firma Microsoft również zaleca [funkcję migawka.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Następne kroki

Oto dwa przykładowe kody na tej funkcji:

  * [Tworzenie migawki obiektu blob i zarządzanie nim w pliku .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Używanie migawek obiektów blob za pomocą programu PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

