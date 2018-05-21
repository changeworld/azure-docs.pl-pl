---
title: Utwórz zadania importowania platformy Azure importu/eksportu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć importu dla usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: ''
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: a80d2169f346238f997c727f0e9d82666897b608
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Tworzenie zadania importu dla usługi Import/Eksport Azure

Tworzenie zadania importu za pomocą interfejsu API REST usługi Import/Eksport Microsoft Azure obejmuje następujące kroki:

-   Przygotowywanie dysków za pomocą narzędzia importu/eksportu Azure.

-   Uzyskiwanie lokalizacji, do której należy wysłać dysku.

-   Tworzenie zadania importu.

-   Wysyłania dysków do firmy Microsoft za pośrednictwem usługi obsługiwane operatora.

-   Uaktualnianie zadania importu szczegóły dotyczące wysyłki.

 Zobacz [za pomocą usługi Import/Eksport Microsoft Azure przesyłanie danych do magazynu obiektów Blob](storage-import-export-service.md) Omówienie usługi Import/Eksport i samouczek, który demonstruje sposób użycia [portalu Azure](https://portal.azure.com/) do tworzenia i zarządzania importowanie i eksportowanie zadań.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Przygotowywanie dysków za pomocą narzędzia importu/eksportu Azure

Kroki, aby przygotować dyski dla zadania importu, są takie same, czy utworzyć jobvia portalu lub za pośrednictwem interfejsu API REST.

Poniżej znajduje się krótki przegląd Przygotowanie stacji. Zapoznaj się [odwołania ExportTool importu Azure](storage-import-export-tool-how-to-v1.md) Aby uzyskać pełne instrukcje. Narzędzie importu/eksportu Azure można pobrać [tutaj](http://go.microsoft.com/fwlink/?LinkID=301900).

Przygotowywanie dysku obejmuje:

-   Identyfikowanie dane do zaimportowania.

-   Identyfikowanie przeznaczenia obiekty BLOB w magazynie Windows Azure.

-   Za pomocą narzędzia importu/eksportu Azure, aby skopiować dane do jednego lub więcej dysków twardych.

 Narzędzie importu/eksportu Azure również spowoduje wygenerowanie pliku manifestu dla poszczególnych dysków, jak jest przygotowana. Plik manifestu zawiera:

-   Wyliczenie wszystkich plików przeznaczonych do przekazywania i mapowania z tych plików do obiektów blob.

-   Sum kontrolnych segmentów każdego pliku.

-   Informacje o właściwości do skojarzenia z każdy obiekt blob i metadanych.

-   Lista działanie w przypadku obiektów blob, który jest przekazywanych ma taką samą nazwę jak istniejącym obiektem blob w kontenerze. Dostępne są opcje:) zastępowania obiektu blob z plikiem, (b) aktualizowanie istniejących obiektów blob i Pomiń przekazywania pliku, c) dodać sufiks nazwy nie powoduje konfliktu z innymi plikami.

## <a name="obtaining-your-shipping-location"></a>Uzyskiwanie lokalizacji wysyłki

Przed utworzeniem zadania importu, należy uzyskać wysyłanie nazwy lokalizacji i adres wywołując [lokalizacje listy](/rest/api/storageimportexport/listlocations) operacji. `List Locations` Zwraca listę lokalizacje i ich adres wysyłkowy. Możesz wybrać lokalizację z listy zwróconych i wysłać dyskach twardych na ten adres. Można również użyć `Get Location` operacji bezpośrednio uzyskać adres wysyłkowy dla określonej lokalizacji.

 Wykonaj poniższe kroki, aby uzyskać lokalizacji wysyłki:

-   Określ nazwę lokalizacji konta magazynu. Tę wartość można znaleźć w **lokalizacji** na konto magazynu **pulpitu nawigacyjnego** na platformie Azure, w portalu lub, którego dotyczy kwerenda dla za pomocą operacji interfejsu API zarządzania usługami [Get właściwości konta magazynu](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Pobieranie lokalizacji, która jest dostępnych do przetworzenia tego konta magazynu przez wywołanie metody `Get Location` operacji.

-   Jeśli `AlternateLocations` właściwość lokalizacji zawiera samą lokalizację, a następnie można użyć tej lokalizacji. W przeciwnym razie wywołać `Get Location` ponownie operację, używając jednej z lokalizacji alternatywnej. Oryginalnej lokalizacji może być tymczasowo zamknięte konserwacji.

## <a name="creating-the-import-job"></a>Tworzenie zadania importu
Aby utworzyć zadanie importu, należy wywołać [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji. Należy podać następujące informacje:

-   Nazwa zadania.

-   Nazwa konta magazynu.

-   Wysyłanie nazwy lokalizacji, uzyskanych w poprzednim kroku.

-   Typ zadania (Importuj).

-   Adres zwrotny, gdzie dyski mają być wysyłane po zakończeniu zadania importu.

-   Lista dysków w zadaniu. Dla każdego dysku musi zawierać następujące informacje, które jest prefiksem uzyskanym w kroku przygotowania dysku:

    -   Identyfikator dysku

    -   Klucza funkcji BitLocker

    -   Względna ścieżka pliku manifestu na dysku twardym

    -   Base16 algorytmem wyznaczania wartości skrótu MD5 w pliku manifestu

## <a name="shipping-your-drives"></a>Wysyłanie dysków
Muszą dostarczać dysków na adres, który został uzyskany z poprzedniego kroku, a usługi Import/Eksport należy podać numer śledzenia pakietu.

> [!NOTE]
>  Muszą dostarczać dysków za pomocą usługi obsługiwane operatora, który dostarczy numer identyfikacyjny pakietu.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Aktualizowanie zadania importu z informacjami o wysyłki
Po utworzeniu numer śledzenia wywołań [właściwości zadania aktualizacji](/api/storageimportexport/jobs#Jobs_Update) Operacja aktualizowania wysyłanie Nazwa operatora, numer zadania i operatora numer konta do wysyłki zwracany. Opcjonalnie można określić liczbę dysków, a także data wysyłki.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
