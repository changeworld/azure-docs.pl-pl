---
title: Przeodwodnione dane obiektów blob z warstwy Archiwum
description: Zapisz obiekty blob z magazynu archiwum, aby uzyskać dostęp do danych.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958215"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Przeodwodnione dane obiektów blob z warstwy Archiwum

Gdy obiekt BLOB znajduje się w warstwie dostępu archiwizowania, jest traktowany jako przełączony do trybu offline i nie można go odczytać ani modyfikować. Metadane obiektu BLOB pozostają w trybie online i są dostępne, umożliwiając wyświetlenie listy obiektów blob i jego właściwości. Odczytywanie i modyfikowanie danych obiektów BLOB jest możliwe tylko w przypadku warstw online, takich jak gorąca lub chłodna. Dostępne są dwie opcje pobierania i uzyskiwania dostępu do danych przechowywanych w warstwie dostępu archiwizowania.

1. Przeciąganie [zarchiwizowanego obiektu BLOB do warstwy online](#rehydrate-an-archived-blob-to-an-online-tier) — można ponownie podgrzać zarchiwizowany obiekt BLOB do gorącej lub chłodnej, zmieniając jego warstwę przy użyciu operacji [Set BLOB warstwy](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Skopiuj zarchiwizowany obiekt BLOB do warstwy online](#copy-an-archived-blob-to-an-online-tier) — Utwórz nową kopię zarchiwizowanego obiektu BLOB przy użyciu operacji [kopiowania obiektu](https://docs.microsoft.com/rest/api/storageservices/copy-blob) BLOB. Określ inną nazwę obiektu BLOB i warstwę docelową gorącą lub chłodną.

 Aby uzyskać więcej informacji o warstwach, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Przeciąganie zarchiwizowanego obiektu BLOB do warstwy online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiuj zarchiwizowany obiekt BLOB do warstwy online

Jeśli nie chcesz ponownie hydratać obiektu BLOB, możesz wybrać operację [kopiowania obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Oryginalny obiekt BLOB pozostanie niezmodyfikowany w archiwum, podczas gdy pracujesz nad nowym obiektem BLOB w warstwie gorąca lub chłodna. W przypadku korzystania z procesu kopiowania można ustawić opcjonalną Właściwość " *x-MS--* -------------(wersja zapoznawcza") o priorytecie

Obiekty blob archiwalne mogą być kopiowane tylko do warstw docelowych w trybie online. Kopiowanie obiektu BLOB archiwum do innego obiektu BLOB archiwum nie jest obsługiwane.

Kopiowanie obiektu BLOB z archiwum trwa. W tle operacja **kopiowania obiektu BLOB** tymczasowo dekoduje źródłowy obiekt BLOB archiwum, aby utworzyć nowy obiekt BLOB online w warstwie docelowej. Ten nowy obiekt BLOB nie jest dostępny do momentu zakończenia tymczasowego uzupełniania z archiwum, a dane są zapisywane w nowym obiekcie blob.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Ponownego wypełniania obiekty blob z archiwum do warstwy gorąca lub chłodna są obciążane jako operacje odczytu i pobierania danych. Użycie wysokiego priorytetu (wersja zapoznawcza) obejmuje wyższe koszty operacji i pobierania danych w porównaniu z priorytetem standardowym. Uzupełnianie o wysokim priorytecie jest wyświetlane jako osobny wiersz na rachunku. Jeśli żądanie o wysokim priorytecie zwracające obiekt BLOB archiwum z kilku gigabajtów trwa ponad 5 godzin, nie zostanie naliczona opłata za wysoki priorytet. Jednak nadal obowiązują standardowe stawki za pobieranie.

Kopiowanie obiektów blob z archiwum do warstwy gorąca lub chłodna podlega opłatom za operacje odczytu i pobieranie danych. Dla operacji zapisu jest naliczana opłata za utworzenie nowej kopii. Opłaty za wczesne usunięcie nie mają zastosowania podczas kopiowania do obiektu BLOB w trybie online, ponieważ źródłowy obiekt BLOB nie jest modyfikowany w warstwie archiwum. Obowiązują opłaty o wysokim priorytecie.

Obiekty blob w warstwie archiwum powinny być przechowywane przez co najmniej 180 dni. Usunięcie lub ponownego wypełniania zarchiwizowanych obiektów BLOB przed 180 dni spowoduje naliczenie opłat za wczesne usunięcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen blokowych obiektów blob i uzupełniania danych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Aby uzyskać więcej informacji na temat opłat za transfer danych wychodzących, zobacz [szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o warstwach Blob Storage](storage-blob-storage-tiers.md)
* [Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)
* [Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
* [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
