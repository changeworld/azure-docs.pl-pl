---
title: Utwórz zadania dla Import/Eksport Azure Eksport | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zadanie eksportu dla usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3fb3f2af5e5cebcac21f4372bc9d9dc9ee837202
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363251"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Tworzenie zadania eksportu dla usługi Import/Eksport Azure
Tworzenie zadania eksportu przy użyciu interfejsu API REST usługi Import/Eksport Microsoft Azure obejmuje następujące kroki:

-   Wybieranie obiektów blob do wyeksportowania.

-   Uzyskiwanie lokalizacji wysyłki.

-   Tworzenie zadania eksportu.

-   Wysyłanie pustych dysków do firmy Microsoft za pomocą usługi obsługiwane operatora.

-   Uaktualnianie zadania eksportu informacji.

-   Odbieranie dyski wstecz od firmy Microsoft.

 Zobacz [przy użyciu usługi Windows Azure importu/eksportu przesyłanie danych do magazynu obiektów Blob](storage-import-export-service.md) Omówienie usługi Import/Eksport i samouczek, który demonstruje sposób użycia [portalu Azure](https://portal.azure.com/) do tworzenia i zarządzania importowanie i eksportowanie zadań.

## <a name="selecting-blobs-to-export"></a>Wybieranie obiektów blob do wyeksportowania
 Aby utworzyć zadanie eksportu, należy udostępnić listę obiektów blob, które mają zostać wyeksportowane z konta magazynu. Istnieje kilka sposobów, aby wybrać obiekty BLOB do wyeksportowania:

-   Ścieżka względna obiektów blob służy do wybierz pojedynczego obiektu blob i wszystkie jego migawek.

-   Ścieżka względna obiektów blob służy do wybierania pojedynczego obiektu blob, z wyłączeniem jego migawek.

-   Ścieżka względna obiektów blob i czas migawka służy do wybierz jedna migawka.

-   Aby zaznaczyć wszystkie obiekty BLOB i migawki z danego prefiksu służy prefiks obiektu blob.

-   Możesz wyeksportować wszystkie obiekty BLOB i migawek na koncie magazynu.

 Aby uzyskać więcej informacji na temat określania obiektów blob, aby wyeksportować zobacz [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji.

## <a name="obtaining-your-shipping-location"></a>Uzyskiwanie lokalizacji wysyłki
Przed utworzeniem zadania eksportu, należy uzyskać wysyłanie nazwy lokalizacji i adres wywołując [pobrać lokalizacji](https://portal.azure.com) lub [lokalizacje listy](/rest/api/storageimportexport/listlocations) operacji. `List Locations` Zwraca listę lokalizacje i ich adres wysyłkowy. Możesz wybrać lokalizację z listy zwróconych i wysłać dyskach twardych na ten adres. Można również użyć `Get Location` operacji bezpośrednio uzyskać adres wysyłkowy dla określonej lokalizacji.

Wykonaj poniższe kroki, aby uzyskać lokalizacji wysyłki:

-   Określ nazwę lokalizacji konta magazynu. Tę wartość można znaleźć w **lokalizacji** na konto magazynu **pulpitu nawigacyjnego** na platformie Azure, w portalu lub, którego dotyczy kwerenda dla za pomocą operacji interfejsu API zarządzania usługami [Get właściwości konta magazynu](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Pobieranie lokalizacji, w której są dostępne do przetworzenia tego konta magazynu przez wywołanie metody `Get Location` operacji.

-   Jeśli `AlternateLocations` właściwość lokalizacji zawiera samą lokalizację, a następnie można użyć tej lokalizacji. W przeciwnym razie wywołać `Get Location` ponownie operację, używając jednej z lokalizacji alternatywnej. Oryginalnej lokalizacji może być tymczasowo zamknięte konserwacji.

## <a name="creating-the-export-job"></a>Tworzenie zadania eksportu
 Aby utworzyć zadanie eksportu, należy wywołać [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji. Należy podać następujące informacje:

-   Nazwa zadania.

-   Nazwa konta magazynu.

-   Wysyłanie nazwy lokalizacji, uzyskanych w poprzednim kroku.

-   Typ zadania (Eksportowanie).

-   Adres zwrotny, gdzie dyski mają być wysyłane po zakończeniu zadania eksportu.

-   Lista obiektów blob (lub prefiksy obiektów blob) do wyeksportowania.

## <a name="shipping-your-drives"></a>Wysyłanie dysków
 Następnie użyj narzędzia importu/eksportu Azure można określić liczbę dysków, które należy wysłać, na podstawie wybranych do wyeksportowania obiektów blob i rozmiar dysku. Zobacz [odwołania narzędzie importu/eksportu Azure](storage-import-export-tool-how-to-v1.md) szczegółowe informacje.

 Pakiet dysków w jednym pakiecie, a następnie wyślij je do uzyskanego w poprzednim kroku adresu. Zanotuj numer śledzenia pakietu do następnego kroku.

> [!NOTE]
>  Muszą dostarczać dysków za pomocą usługi obsługiwane operatora, który dostarczy numer identyfikacyjny pakietu.

## <a name="updating-the-export-job-with-your-package-information"></a>Informacje o pakiecie aktualizacji zadania eksportu
 Po utworzeniu numer śledzenia wywołań [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operację, aby zaktualizować Nazwa operatora i śledzenie liczby zadania. Opcjonalnie można określić liczbę dysków, adres zwrotny, a także data wysyłki.

## <a name="receiving-the-package"></a>Odbieranie pakietu
 Po przetworzeniu zadania eksportu dysków będą zwracane z zaszyfrowanych danych. Można pobrać klucza funkcji BitLocker dla wszystkich dysków przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_Get) operacji. Następnie można odblokować dysku przy użyciu klucza. Plik manifestu dysku na każdym dysku, zawiera listę plików na dysku, a także oryginalny adres obiektu blob dla każdego pliku.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
