---
title: Utwórz eksport zadania dla usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zadanie eksportu dla usługi Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e0513bc18f1cf14beb4c1becfc1835235a5ddc96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483098"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Tworzenie zadania eksportu dla usługi Azure Import/Export
Tworzenie zadania eksportu dla usługi Microsoft Azure Import/Export, za pomocą interfejsu API REST obejmuje następujące czynności:

- Wybieranie obiektów blob do wyeksportowania.

- Uzyskiwanie lokalizacji wysyłki.

- Tworzenie zadania eksportu.

- Wysyłka pustych dysków do firmy Microsoft za pośrednictwem usługi obsługiwane operatora.

- Aktualizowanie zadania eksportu informacje o pakiecie.

- Odbieranie dyski powrót po awarii z firmy Microsoft.

  Zobacz [przy użyciu usługi Windows Azure Import/Export przesyłanie danych do magazynu obiektów Blob](storage-import-export-service.md) z omówieniem usługi Import/Export a samouczek, w którym pokazano, jak używać [witryny Azure portal](https://portal.azure.com/) do tworzenia i Zarządzaj Importuj i Eksportuj zadania.

## <a name="selecting-blobs-to-export"></a>Wybieranie obiektów blob do wyeksportowania
 Aby utworzyć zadanie eksportu, musisz podać listę obiektów blob, które mają zostać wyeksportowane z konta magazynu. Istnieje kilka sposobów, aby wybrać obiekty BLOB do wyeksportowania:

- Ścieżka względna obiektów blob umożliwia wybierz pojedynczy obiekt blob i wszystkie jego migawek.

- Ścieżka względna obiektów blob umożliwia wybierz pojedynczy obiekt blob, z wyłączeniem jego migawek.

- Ścieżka względna obiektów blob i godzina migawki służy do wybierz pojedynczej migawki.

- Prefiks obiektu blob można użyć, aby zaznaczyć wszystkie obiekty BLOB i migawki za pomocą danego prefiksu.

- Możesz wyeksportować wszystkie obiekty BLOB i migawki na koncie magazynu.

  Aby uzyskać więcej informacji na temat określania obiekty BLOB do wyeksportowania, zobacz [umieścić zadania](/rest/api/storageimportexport/jobs) operacji.

## <a name="obtaining-your-shipping-location"></a>Uzyskiwanie lokalizacji wysyłki
Przed utworzeniem przez zadanie eksportu, należy uzyskać nazwę lokalizacji wysyłki i adres, wywołując [Pobieranie lokalizacji](https://portal.azure.com) lub [listy lokalizacji](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operacji. `List Locations` Spowoduje to zwrócenie listy lokalizacje i swoje adresy pocztowe. Można wybrać lokalizację z listy zwracane i wysłania dysków twardych do tego adresu. Można również użyć `Get Location` operacji bezpośrednio uzyskać adres wysyłkowy dla określonej lokalizacji.

Wykonaj poniższe kroki, aby uzyskać lokalizacji wysyłki:

-   Określ nazwę lokalizacji konta magazynu. Tę wartość można znaleźć w obszarze **lokalizacji** pola na koncie magazynu **pulpit nawigacyjny** w witrynie Azure portal lub kwerendy dla za pomocą operacji interfejsu API zarządzania usługi [pobrać konta magazynu Właściwości](/rest/api/storagerp/storageaccounts).

-   Pobieranie lokalizacji, które są dostępne do przetworzenia tego konta magazynu przez wywołanie metody `Get Location` operacji.

-   Jeśli `AlternateLocations` właściwość lokalizacji zawiera sama lokalizacja, a następnie można użyć tej lokalizacji. W przeciwnym razie wywołanie `Get Location` ponownie operację, używając jednej z lokalizacji alternatywnej. Oryginalnej lokalizacji może być tymczasowo zamknięte w celu przeprowadzenia konserwacji.

## <a name="creating-the-export-job"></a>Tworzenie zadania eksportu
 Aby utworzyć zadanie eksportu, wywołaj [umieścić zadania](/rest/api/storageimportexport/jobs) operacji. Należy podać następujące informacje:

-   Nazwa zadania.

-   Nazwa konta magazynu.

-   Wysyłanie nazwy lokalizacji, uzyskanego w poprzednim kroku.

-   Typ zadania (eksportu).

-   Adres zwrotny, których dyski mają być wysyłane po zakończeniu zadania eksportu.

-   Lista obiektów blob (lub prefiksy obiektów blob) do wyeksportowania.

## <a name="shipping-your-drives"></a>Wysyłanie z stacje dysków
 Następnie narzędzie Azure Import/Export do określenia liczby dysków, które należy wysłać, na podstawie obiektów blob, które zostały wybrane do wyeksportowania i rozmiar dysku. Zobacz [odwołanie do usługi Azure Import/Export narzędzie](storage-import-export-tool-how-to-v1.md) Aby uzyskać szczegółowe informacje.

 Pakiet z stacje w jednym pakiecie i odeślemy je do adresu uzyskanego w poprzednim kroku. Zanotuj numer śledzenia pakietu do kolejnego kroku.

> [!NOTE]
>  Należy dostarczyć dysków za pośrednictwem usługi obsługiwane operatora, który dostarczy numer śledzenia dla pakietu.

## <a name="updating-the-export-job-with-your-package-information"></a>Trwa aktualizowanie zadania eksportu z informacjami o pakietu
 Po umieszczeniu numeru śledzenia wywołań [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs) operację, aby zaktualizować Nazwa operatora i śledzenie liczby dla zadania. Opcjonalnie możesz określić liczbę dysków, adres zwrotny, a także data wysyłki.

## <a name="receiving-the-package"></a>Odbieranie pakietu
 Po przetworzeniu zadania eksportu dysków zostaną zwrócone do użytkownika za pomocą zaszyfrowanych danych. Możesz pobrać klucza funkcji BitLocker dla wszystkich dysków przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs) operacji. Następnie można odblokować dysku przy użyciu klucza. Plik manifestu dysku na każdym dysku, zawiera listę plików na dysku, a także oryginalnego adresu obiektu blob dla każdego pliku.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
