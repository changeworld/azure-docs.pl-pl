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
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562279"
---
# <a name="storage-account-container-recovery"></a>Odzyskiwanie kontenera konta magazynu

Usługa Azure Storage zapewnia odporność danych za pomocą zautomatyzowanych replik. Nie powoduje to jednak, że kod aplikacji ani użytkownicy mogą uszkodzić dane, przypadkowo czy w sposób złośliwy. Zachowanie dokładności danych w przypadku awarii aplikacji lub użytkownika wymaga bardziej zaawansowanych technik, takich jak kopiowanie danych do pomocniczej lokalizacji magazynu z dziennikiem inspekcji.

## <a name="checking-azure-storage-account-type"></a>Sprawdzanie typu konta usługi Azure Storage

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Odszukaj konto magazynu.

3. W sekcji **Przegląd** Sprawdź pozycję **replikacja**.

   ![Image (Obraz)](media/storage-account-container-recovery/1.png)

4. Jeśli typ replikacji to **GRS/RA-GRS**, możliwe jest odzyskanie kontenera kont bez gwarancji. Dla wszystkich innych typów replikacji nie jest możliwe.

5. Zbierz poniższe informacje i Zastąp żądanie pomocy technicznej pomoc techniczna firmy Microsoft.

   * Nazwa konta magazynu:
   * Nazwa kontenera:
   * Godzina usunięcia:

   W poniższej tabeli przedstawiono omówienie zakresu odzyskiwania kontenera kont magazynu w zależności od strategii replikacji.

   |Typ zawartości|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Kontener magazynu|Nie|Nie|Yes|Yes| 

   * Możemy spróbować przywrócić kontener konta magazynu, ale bez żadnej gwarancji. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Nie należy robić, aby odzyskiwanie zakończyło się pomyślnie

* Nie należy ponownie tworzyć kontenera (o tej samej nazwie).  
* Jeśli kontener został już utworzony ponownie, należy go usunąć przed zażądaniem obsługi odzyskiwania.

## <a name="steps-to-prevent-this-in-the-future"></a>Procedura zapobiegania tym w przyszłości

1. Aby uniknąć tego w przyszłości, najbardziej zalecaną funkcją do użycia jest [nietrwałe usunięcie](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Zalecamy również korzystanie z funkcji [Snapshot](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) .
 
## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono dwa przykładowe kody dotyczące funkcji:

  * [Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Używanie migawek obiektów blob z programem PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

