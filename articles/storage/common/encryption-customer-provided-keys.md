---
title: Podaj klucz szyfrowania na żądanie do magazynu obiektów Blob
titleSuffix: Azure Storage
description: Klienci składający żądania względem usługi Azure Blob storage mają możliwość zapewnienia klucza szyfrowania na podstawie żądania (w wersji zapoznawczej). Włączenie klucza szyfrowania w żądaniu zapewnia szczegółową kontrolę nad ustawieniami szyfrowania dla operacji magazynu obiektów Blob.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410066"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Podaj klucz szyfrowania na żądanie do magazynu obiektów Blob (wersja zapoznawcza)

Klienci składający żądania względem usługi Azure Blob storage mają możliwość zapewnienia klucza szyfrowania na podstawie żądania (w wersji zapoznawczej). Włączenie klucza szyfrowania w żądaniu zapewnia szczegółową kontrolę nad ustawieniami szyfrowania dla operacji magazynu obiektów Blob. Klucze dostarczone przez klienta mogą być przechowywane w usłudze Azure Key Vault lub w innym magazynie kluczy.

## <a name="encrypting-read-and-write-operations"></a>Szyfrowanie operacji odczytu i zapisu

Gdy aplikacja kliencka udostępnia klucz szyfrowania na żądanie, usługa Azure Storage wykonuje szyfrowanie i odszyfrowywanie w sposób przejrzysty podczas odczytywania i zapisywania danych obiektów blob. Usługa Azure Storage zapisuje skrót SHA-256 klucza szyfrowania wraz z zawartością obiektu blob. Skrót jest używany do sprawdzenia, czy wszystkie kolejne operacje względem obiektu blob używają tego samego klucza szyfrowania.

Usługa Azure Storage nie przechowuje klucza szyfrowania wysyłany z żądaniem ani nie zarządza nim. Klucz jest bezpiecznie odrzucany po zakończeniu procesu szyfrowania lub odszyfrowywania.

Gdy klient tworzy lub aktualizuje obiekt blob przy użyciu klucza dostarczonego przez klienta na żądanie, następnie kolejne żądania odczytu i zapisu dla tego obiektu blob musi również zapewnić klucz. Jeśli klucz nie jest podany w żądaniu obiektu blob, który został już zaszyfrowany za pomocą klucza dostarczonego przez klienta, żądanie kończy się niepowodzeniem z kodem błędu 409 (Konflikt).

Jeśli aplikacja kliencka wysyła klucz szyfrowania na żądanie, a konto magazynu jest również szyfrowany przy użyciu klucza zarządzanego przez firmę Microsoft lub klucz zarządzany przez klienta, a następnie usługa Azure Storage używa klucza dostarczonego na żądanie szyfrowania i odszyfrowywania.

Aby wysłać klucz szyfrowania w ramach żądania, klient musi ustanowić bezpieczne połączenie z usługą Azure Storage przy użyciu protokołu HTTPS.

Każda migawka obiektu blob może mieć swój własny klucz szyfrowania.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Nagłówki żądań do określania kluczy dostarczonych przez klienta

W przypadku wywołań REST klienci mogą używać następujących nagłówków do bezpiecznego przekazywania informacji o kluczu szyfrowania w żądaniu do magazynu obiektów Blob:

|Nagłówek żądania | Opis |
|---------------|-------------|
|`x-ms-encryption-key` |Wymagane zarówno dla żądań zapisu, jak i odczytu. Wartość klucza szyfrowania AES-256 zakodowana w bazie podstawowej 64. |
|`x-ms-encryption-key-sha256`| Wymagane zarówno dla żądań zapisu, jak i odczytu. Kodowanie kodu SHA256 zakodowanego w bazie Base4 klucza szyfrowania. |
|`x-ms-encryption-algorithm` | Wymagane dla żądań zapisu, opcjonalne dla żądań odczytu. Określa algorytm używany podczas szyfrowania danych przy użyciu danego klucza. Musi być AES256. |

Określanie kluczy szyfrowania w żądaniu jest opcjonalne. Jeśli jednak określisz jeden z nagłówków wymienionych powyżej dla operacji zapisu, należy określić wszystkie z nich.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operacje magazynu obiektów Blob obsługujące klucze dostarczone przez klienta

Następujące operacje magazynu obiektów Blob obsługują wysyłanie kluczy szyfrowania dostarczonych przez klienta na żądanie:

- [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list)
- [Umieść blok](/rest/api/storageservices/put-block)
- [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url)
- [Umieść stronę](/rest/api/storageservices/put-page)
- [Umieść stronę z adresu URL](/rest/api/storageservices/put-page-from-url)
- [Dołącz blok](/rest/api/storageservices/append-block)
- [Ustawianie właściwości obiektu blob](/rest/api/storageservices/set-blob-properties)
- [Ustawianie metadanych obiektów blob](/rest/api/storageservices/set-blob-metadata)
- [Pobierz obiekt blob](/rest/api/storageservices/get-blob)
- [Pobieranie właściwości obiektu blob](/rest/api/storageservices/get-blob-properties)
- [Pobierz metadane obiektów blob](/rest/api/storageservices/get-blob-metadata)
- [Wykonywanie migawki obiektu blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Obracanie kluczy dostarczonych przez klienta

Aby obrócić klucz szyfrowania, który został użyty do zaszyfrowania obiektu blob, pobierz obiekt blob, a następnie ponownie przekaż go za pomocą nowego klucza szyfrowania.

> [!IMPORTANT]
> Witryny Azure portal nie może służyć do odczytu lub zapisu do kontenera lub obiektu blob, który jest zaszyfrowany przy użyciu klucza dostarczonego na żądanie.
>
> Pamiętaj, aby chronić klucz szyfrowania, który można podać na żądanie do magazynu obiektów Blob w bezpiecznym magazynie kluczy, takich jak Usługa Azure Key Vault. Jeśli spróbujesz operacji zapisu na kontenerze lub obiekcie blob bez klucza szyfrowania, operacja zakończy się niepowodzeniem i utracisz dostęp do obiektu.

## <a name="next-steps"></a>Następne kroki

- [Określ klucz dostarczony przez klienta w żądaniu magazynu obiektów Blob za pomocą platformy .NET](../blobs/storage-blob-customer-provided-key.md)
- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md)
