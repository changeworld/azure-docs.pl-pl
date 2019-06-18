---
title: Tworzenie zadania importu usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć import dla usługi Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483081"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Tworzenie zadania importu dla usługi Azure Import/Export

Tworzenie zadania importu dla usługi Microsoft Azure Import/Export, za pomocą interfejsu API REST obejmuje następujące czynności:

- Przygotowywanie dysków za pomocą narzędzia Azure Import/Export.

- Uzyskiwanie lokalizacji, do którego należy dostarczyć dysk.

- Tworzenie zadania importu.

- Wysyłanie dysków do firmy Microsoft za pośrednictwem usługi obsługiwane operatora.

- Aktualizowanie zadania importu o szczegółach wysyłki.

  Zobacz [przy użyciu usługi Microsoft Azure Import/Export przesyłanie danych do magazynu obiektów Blob](storage-import-export-service.md) z omówieniem usługi Import/Export a samouczek, w którym pokazano, jak używać [witryny Azure portal](https://portal.azure.com/) do utworzenia Zarządzanie Importuj i Eksportuj zadania.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Przygotowywanie dysków za pomocą narzędzia Azure Import/Export

Kroki, aby przygotować dyski do zadania importu, są takie same, czy tworzyć zadania w portalu lub za pośrednictwem interfejsu API REST.

Poniżej przedstawiono krótkie omówienie przygotowania dysku. Zapoznaj się [odwołanie do usługi Azure Import ExportTool](storage-import-export-tool-how-to-v1.md) pełne instrukcje. Narzędzie importu/eksportu platformy Azure można pobrać [tutaj](https://go.microsoft.com/fwlink/?LinkID=301900).

Przygotowywanie dysku obejmuje:

- Identyfikowanie danych do zaimportowania.

- Identyfikowanie przeznaczenia obiektów blob w Windows Azure Storage.

- Za pomocą narzędzie importu/eksportu platformy Azure, aby skopiować dane do jednego lub więcej dysków twardych.

  Narzędzie importu/eksportu platformy Azure także generuje plik manifestu dla każdego z stacje podczas jego przygotowywania. Zawiera plik manifestu:

- Wyliczenie wszystkich plików, które są przeznaczone do przekazywania i mapowania tych plików do obiektów blob.

- Sumy kontrolne segmentów każdego pliku.

- Informacje o metadanych i właściwości do skojarzenia z każdego obiektu blob.

- Lista akcję do wykonania, jeśli obiekt blob, który jest przekazywany ma taką samą nazwę jak istniejący obiekt blob w kontenerze. Możliwe opcje to:) zastąpić obiekt blob z plikiem, (b) Zachowaj istniejący obiekt blob i Pomiń przekazywania pliku, c) Dodaj sufiks do nazwy, aby nie powoduje konfliktu z innymi plikami.

## <a name="obtaining-your-shipping-location"></a>Uzyskiwanie lokalizacji wysyłki

Przed utworzeniem zadania importu, należy uzyskać nazwę lokalizacji wysyłki i adres, wywołując [listy lokalizacji](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) operacji. `List Locations` Spowoduje to zwrócenie listy lokalizacje i swoje adresy pocztowe. Można wybrać lokalizację z listy zwracane i wysłania dysków twardych do tego adresu. Można również użyć `Get Location` operacji bezpośrednio uzyskać adres wysyłkowy dla określonej lokalizacji.

 Wykonaj poniższe kroki, aby uzyskać lokalizacji wysyłki:

-   Określ nazwę lokalizacji konta magazynu. Tę wartość można znaleźć w obszarze **lokalizacji** pola na koncie magazynu **pulpit nawigacyjny** w witrynie Azure portal lub kwerendy dla za pomocą operacji interfejsu API zarządzania usługi [pobrać konta magazynu Właściwości](/rest/api/storagerp/storageaccounts).

-   Pobieranie lokalizacji, która jest dostępny do przetworzenia tego konta magazynu przez wywołanie metody `Get Location` operacji.

-   Jeśli `AlternateLocations` właściwość lokalizacji zawiera sama lokalizacja, a następnie można użyć tej lokalizacji. W przeciwnym razie wywołanie `Get Location` ponownie operację, używając jednej z lokalizacji alternatywnej. Oryginalnej lokalizacji może być tymczasowo zamknięte w celu przeprowadzenia konserwacji.

## <a name="creating-the-import-job"></a>Tworzenie zadania importu
Aby utworzyć zadanie importu, wywołaj [umieścić zadania](/rest/api/storageimportexport/jobs) operacji. Należy podać następujące informacje:

-   Nazwa zadania.

-   Nazwa konta magazynu.

-   Wysyłanie nazwy lokalizacji, uzyskanego w poprzednim kroku.

-   Typ zadania (Importuj).

-   Adres zwrotny, których dyski mają być wysyłane po zakończeniu zadania importu.

-   Na liście dysków w ramach zadania. Dla każdego dysku musi zawierać następujące informacje, które zostały pobrane podczas wykonywania kroku przygotowania dysku:

    -   Identyfikator napędu

    -   Klucz funkcji BitLocker

    -   Ścieżka względna pliku manifestu na dysku twardym

    -   Base16 zakodowane wyznaczania wartości skrótu MD5 w pliku manifestu

## <a name="shipping-your-drives"></a>Wysyłanie z stacje dysków
Należy dostarczyć dysków na adres, który został uzyskany z poprzedniego kroku i musi świadczyć usługi Import/Export za pomocą numeru śledzenia pakietu.

> [!NOTE]
>  Należy dostarczyć dysków za pośrednictwem usługi obsługiwane operatora, który dostarczy numer śledzenia dla pakietu.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Trwa aktualizowanie zadania importu o informacje dotyczące wysyłki
Po umieszczeniu numeru śledzenia wywołań [Aktualizuj właściwości zadania](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) operację, aby zaktualizować wysyłki Nazwa operatora, numer śledzenia dla zadania i numer konta operatora dla wysyłki zwrotnej. Opcjonalnie możesz określić liczbę dysków i także data wysyłki.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
