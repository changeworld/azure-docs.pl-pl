---
title: Porównanie usługi Azure Data Lake Store z obiektu Blob magazynu Azure | Dokumentacja firmy Microsoft
description: Porównanie usługi Azure Data Lake Store z obiektu Blob magazynu Azure
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: acdd829785c44f6683c356c135587be183cce392
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Porównanie usługi Azure Data Lake Store i magazynu obiektów Blob platformy Azure
W tabeli w tym artykule przedstawiono różnice między Azure Data Lake Store i magazynu obiektów Blob Azure wzdłuż niektóre kluczowe aspekty przetwarzania danych big. Magazyn obiektów Blob Azure jest ogólnego przeznaczenia, magazynu skalowalne obiektów, które jest przeznaczone do szerokiej gamy scenariuszy magazynu. Azure Data Lake Store to repozytorium hiperskali, zoptymalizowana pod kątem obciążeń analizy danych big data.

|  | Azure Data Lake Store | Azure Blob Storage |
| --- | --- | --- |
| Przeznaczenie |Zoptymalizowane magazynu na potrzeby obciążeń analizy danych big data |Ogólnego przeznaczenia magazynu obiektów dla różnych scenariuszy magazynu, w tym analizy danych big data |
| Przypadki użycia |Partia, interaktywne i przesyłania strumieniowego analytics machine learning danych takich jak pliki dzienników, IoT danych, kliknij strumieni, dużych zestawów danych |Dowolnego typu danych tekstowych lub binarnych, takich jak aplikacji kopii zakończenia, dane kopii zapasowej, nośnika magazynu danych celu przesyłania strumieniowego i ogólne. Ponadto pełnej obsługi obciążeń analizy; Partia, interaktywne i przesyłania strumieniowego analytics machine learning danych takich jak pliki dzienników, IoT danych, kliknij strumieni, dużych zestawów danych |
| Kluczowe pojęcia |Konto usługi Data Lake Store zawiera foldery, który z kolei zawiera dane przechowywane w postaci plików |Konto magazynu ma kontenerów, który z kolei ma dane w postaci obiektów blob |
| Struktura |Hierarchiczny system plików |Obiekt magazynu z prosty obszar nazw |
| Interfejs API |Interfejs API REST za pośrednictwem protokołu HTTPS |Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS |
| Interfejs API po stronie serwera |[Interfejs API REST WebHDFS zgodny](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Interfejs API REST magazynu obiektów Blob platformy Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systemu plików usługi Hadoop |Yes |Yes |
| Operacje na danych - uwierzytelniania |Na podstawie [tożsamości usługi Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) |Oparte na wspólne klucze tajne - [klucze dostępu do konta](../storage/common/storage-create-storage-account.md#manage-your-storage-account) i [kluczy współużytkowanych podpisu dostępu](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operacje na danych — protokół uwierzytelniania |OAuth 2.0. Wywołania musi zawierać prawidłowy token JWT (JSON Web Token) wydanego przez usługę Azure Active Directory |Kod uwierzytelniania wiadomości na podstawie skrótu (HMAC). Wywołania musi zawierać skrótu SHA-256 algorytmem Base64 częścią żądania HTTP. |
| Operacje na danych - autoryzacji |Listy kontroli dostępu POSIX (ACL).  Listy ACL oparte na Azure Active Directory tożsamości można ustawić na poziomie plików i folderów. |Na poziomie konta autoryzacji — użyj [klucze dostępu do konta](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>Dla konta, kontenera lub obiektu blob autoryzacji - użyj [udostępnionych kluczy podpisów dostępu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operacje na danych - inspekcji |Dostępne. Zobacz [tutaj](data-lake-store-diagnostic-logs.md) informacji. |Dostępna |
| Szyfrowanie danych magazynowanych |<ul><li>Przezroczyste, po stronie serwera</li> <ul><li>Z usługami zarządzać kluczami</li><li>Z zarządzanego przez klienta kluczy w Azure KeyVault</li></ul></ul> |<ul><li>Przezroczyste, po stronie serwera</li> <ul><li>Z usługami zarządzać kluczami</li><li>Z zarządzanego przez klienta kluczy w KeyVault Azure (wersja zapoznawcza)</li></ul><li>Szyfrowania po stronie klienta</li></ul> |
| Operacje zarządzania (np. Utwórz konto) |[Kontrola dostępu oparta na rolach](../active-directory/role-based-access-control-what-is.md) (RBAC) jest dostarczany przez platformę Azure do zarządzania kontami |[Kontrola dostępu oparta na rolach](../active-directory/role-based-access-control-what-is.md) (RBAC) jest dostarczany przez platformę Azure do zarządzania kontami |
| Zestawy SDK dewelopera |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Wydajność przetwarzania obciążenia analityka |Zoptymalizowana wydajność w przypadku obciążeń równoległych analytics. Wysokiej przepływności i IOPS. |Zoptymalizowana wydajność w przypadku obciążeń równoległych analytics. |
| Limity rozmiaru |Brak ograniczeń na rozmiarów kont, rozmiarów plików lub liczby plików |Określone limity udokumentowane [tutaj](../storage/common/storage-scalability-targets.md). Konto większych ogranicza dostępne kontaktując się [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/) |
| Nadmiarowość geograficzna |Lokalnie nadmiarowego (wielu kopii danych w jednym regionie Azure) |Magazyn lokalnie nadmiarowy (LRS), strefy nadmiarowy (ZRS), globalnie nadmiarowy (GRS), dostęp do odczytu globalnie nadmiarowego (RA-GRS). Zobacz [tutaj](../storage/common/storage-redundancy.md) Aby uzyskać więcej informacji |
| Stan usługi |Ogólnie dostępna |Ogólnie dostępna |
| Dostępność regionalna |Zobacz [tutaj](https://azure.microsoft.com/regions/#services) |Dostępna we wszystkich regionach platformy Azure |
| Cena |Zobacz [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobacz [ceny](https://azure.microsoft.com/pricing/details/storage/) |


