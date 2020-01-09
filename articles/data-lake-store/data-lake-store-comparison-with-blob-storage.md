---
title: Porównanie Azure Data Lake Storage Gen1 z Azure Storage Blob | Microsoft Docs
description: Porównanie Azure Data Lake Storage Gen1 z Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438719"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porównanie Azure Data Lake Storage Gen1 i platformy Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

W tabeli w tym artykule zestawiono różnice między Azure Data Lake Storage Gen1 i Blob Storage platformy Azure, a także kluczowe aspekty przetwarzania danych Big Data. Azure Blob Storage to ogólny cel, skalowalny magazyn obiektów przeznaczony dla szerokiej gamy scenariuszy magazynowych. Azure Data Lake Storage Gen1 to repozytorium skalowania funkcji Hyper-Skala, które jest zoptymalizowane pod kątem obciążeń związanych z analizą danych Big Data.

|  | Azure Data Lake Storage 1. generacji | Azure Blob Storage |
| --- | --- | --- |
| Przeznaczenie |Zoptymalizowany magazyn dla obciążeń analizy danych Big Data |Magazyn obiektów ogólnego przeznaczenia dla wielu różnych scenariuszy magazynowania, w tym analizy danych Big Data |
| Przypadki użycia |Batch, Interactive, Stream Analytics i uczenie maszynowe, takie jak pliki dziennika, dane IoT, kliknij strumienie, duże zestawy danych |Dowolny typ danych tekstowych lub binarnych, takich jak zaplecze aplikacji, dane kopii zapasowej, magazyn multimediów do przesyłania strumieniowego i dane ogólne ogólnego przeznaczenia. Dodatkowo pełna obsługa obciążeń analitycznych; Batch, Interactive, Stream Analytics i uczenie maszynowe, takie jak pliki dziennika, dane IoT, kliknij strumienie, duże zestawy danych |
| Kluczowe pojęcia |Konto Data Lake Storage Gen1 zawiera foldery, które z kolei zawierają dane przechowywane jako pliki |Konto magazynu ma kontenery, które z kolei mają dane w postaci obiektów BLOB |
| Struktura |Hierarchiczny system plików |Magazyn obiektów z płaską przestrzenią nazw |
| API |Interfejs API REST przy użyciu protokołu HTTPS |Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS |
| Interfejs API po stronie serwera |[Interfejs API REST zgodnych z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Interfejs API REST usługi Azure Blob Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systemu plików usługi Hadoop |Tak |Tak |
| Operacje na danych — uwierzytelnianie |Na podstawie [tożsamości Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Na podstawie [kluczy dostępu](../storage/common/storage-account-keys-manage.md) współdzielonego i [kluczy sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operacje na danych — protokół uwierzytelniania |OAuth 2,0. Wywołania muszą zawierać prawidłowy JWT (token sieci Web JSON) wystawiony przez Azure Active Directory |Kod uwierzytelniania wiadomości oparte na skrótach (HMAC). Wywołania muszą zawierać skrót SHA-256 zakodowany algorytmem Base64 w ramach żądania HTTP. |
| Operacje na danych — autoryzacja |Listy Access Control POSIX (ACL).  Listy ACL oparte na tożsamościach Azure Active Directory można ustawić na poziomie plików i folderów. |W przypadku autoryzacji na poziomie konta — Użyj [kluczy dostępu do konta](../storage/common/storage-account-keys-manage.md)<br>Dla konta, kontenera lub autoryzacji obiektów BLOB — Użyj [kluczy sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operacje na danych — Inspekcja |Udostępnione. Aby [uzyskać więcej informacji](data-lake-store-diagnostic-logs.md) , zobacz. |Dostępna |
| Szyfrowanie danych magazynowanych |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi przez usługę</li><li>Z kluczami zarządzanymi przez klienta w usłudze Azure Keys</li></ul></ul> |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi przez usługę</li><li>Z kluczami zarządzanymi przez klienta w magazynie kluczy platformy Azure (wersja zapoznawcza)</li></ul><li>Szyfrowania po stronie klienta</li></ul> |
| Operacje zarządzania (np. Tworzenie konta) |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami |
| Zestawy SDK dla deweloperów |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Wydajność obciążeń analitycznych |Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. |Optymalizacji wydajności dla obciążeń analizy równoległej. |
| Limity rozmiaru |Brak limitów rozmiarów kont, rozmiarów plików lub liczby plików |Aby uzyskać określone limity, zobacz [elementy docelowe skalowalności dla standardowych kont magazynu](../storage/common/scalability-targets-standard-account.md) i [skalowalności oraz docelowych wydajności dla usługi BLOB Storage](../storage/blobs/scalability-targets.md). Większe limity konta dostępne przez skontaktowanie się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/) |
| Nadmiarowość geograficzna |Lokalnie nadmiarowy (wiele kopii danych w jednym regionie świadczenia usługi Azure) |Lokalnie nadmiarowy (LRS), strefy nadmiarowe (ZRS), globalnie nadmiarowy (GRS), dostęp do odczytu globalnie nadmiarowy (RA-GRS). Aby uzyskać więcej informacji, zobacz [tutaj](../storage/common/storage-redundancy.md) . |
| Stan usługi |Ogólnie dostępna |Ogólnie dostępna |
| Dostępność regionalna |Zobacz [tutaj](https://azure.microsoft.com/regions/#services) |Dostępne we wszystkich regionach świadczenia usługi Azure |
| Cena |Zobacz [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobacz [ceny](https://azure.microsoft.com/pricing/details/storage/) |


