---
title: Porównanie usługi Azure Data Lake Store za pomocą usługi Azure Blob Storage | Dokumentacja firmy Microsoft
description: Porównanie usługi Azure Data Lake Store za pomocą usługi Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 0b374e92a1e1d9828bc8c095e29e1dfdfd13275b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492915"
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Porównanie usługi Azure Data Lake Store i Azure Blob Storage
W tabeli w tym artykule przedstawiono różnice w usłudze Azure Data Lake Store i Azure Blob Storage wzdłuż niektórych kluczowych aspektów przetwarzania danych big Data. Usługa Azure Blob Storage jest ogólnego przeznaczenia, skalowalny magazyn obiektów przeznaczony do szerokiej gamy scenariuszy magazynowania. Azure Data Lake Store to ogromne repozytorium, które jest zoptymalizowane pod kątem obciążeń analizy danych big data.

|  | Azure Data Lake Store | Azure Blob Storage |
| --- | --- | --- |
| Przeznaczenie |Magazyn zoptymalizowany pod kątem na potrzeby obciążeń analizy danych big data |Magazyn obiektów ogólnego przeznaczenia dla szerokiej gamy scenariuszy magazynu, w tym analizy danych big data |
| Przypadki użycia |Wsadowe, interakcyjne, przesyłanie strumieniowe analytics i machine learning danych takich jak pliki dzienników, danych IoT kliknij pozycję strumieni dużych zestawów danych |Dowolnego typu dane tekstowe lub binarne, takie jak aplikacja kopii zakończenia, tworzenia kopii zapasowej danych, magazynu multimediów do przesyłania strumieniowego i ogólnego przeznaczenia — dane. Ponadto pełną pomoc techniczną na potrzeby obciążeń analizy; wsadowe, interakcyjne, przesyłanie strumieniowe analytics i machine learning danych takich jak pliki dzienników, danych IoT kliknij pozycję strumieni dużych zestawów danych |
| Kluczowe pojęcia |Konta Data Lake Store zawiera foldery, które z kolei zawiera dane przechowywane jako pliki |Konto magazynu ma kontenerów, co z kolei zawiera dane w postaci obiektów blob |
| Struktura |Hierarchiczny system plików |Magazyn obiektów przy użyciu prosty obszar nazw |
| Interfejs API |Interfejs API REST przy użyciu protokołu HTTPS |Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS |
| Interfejs API po stronie serwera |[Interfejs API REST zgodnych z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Interfejs API REST magazynu obiektów Blob platformy Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systemu plików usługi Hadoop |Yes |Yes |
| Operacje na danych — uwierzytelnianie |Na podstawie [tożsamości usługi Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Oparte na wspólne klucze tajne — [kluczy dostępu do konta](../storage/common/storage-create-storage-account.md#manage-your-storage-account) i [klucze dostępu współużytkowanego w podpisie](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operacje na danych — protokół uwierzytelniania |OAuth 2.0. Wywołania musi zawierać prawidłowy token JWT (JSON Web Token) wystawione przez usługę Azure Active Directory |Kod uwierzytelniania wiadomości bazujących na skrótach (HMAC). Wywołania musi zawierać wartości skrótu SHA-256 algorytmem Base64 nad częścią żądania HTTP. |
| Operacje na danych — autoryzacji |Listy kontroli dostępu POSIX (kontroli dostępu ACL).  Listy ACL w oparciu o usługi Azure Active Directory tożsamości można ustawić na poziomie plików i folderów. |W przypadku zezwolenia na poziomie konta — użyj [kluczy dostępu do konta](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>W przypadku konta, kontenera lub obiektu blob autoryzacji — użyj [udostępnione klucze sygnatur dostępu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operacje na danych — Inspekcja |Jest dostępna. Zobacz [tutaj](data-lake-store-diagnostic-logs.md) informacji. |Dostępne |
| Szyfrowanie danych magazynowanych |<ul><li>Przezroczyste, po stronie serwera</li> <ul><li>Za pomocą kluczy zarządzanych przez usługę</li><li>Za pomocą kluczy zarządzanych przez klienta w usłudze Azure KeyVault</li></ul></ul> |<ul><li>Przezroczyste, po stronie serwera</li> <ul><li>Za pomocą kluczy zarządzanych przez usługę</li><li>Za pomocą kluczy zarządzanych przez klienta w usłudze Azure KeyVault (wersja zapoznawcza)</li></ul><li>Szyfrowania po stronie klienta</li></ul> |
| Operacje zarządzania (np. Tworzenie konta) |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami |
| Zestawy SDK dla deweloperów |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Analiza obciążenia wydajności |Optymalizacji wydajności dla obciążeń analizy równoległej. Wysoka przepływność i operacje We/Wy. |Optymalizacji wydajności dla obciążeń analizy równoległej. |
| Limity rozmiaru |Brak limitów rozmiarów kont, rozmiarów plików lub liczby plików |Określone limity udokumentowane [tutaj](../storage/common/storage-scalability-targets.md). Konto większe limity dostępne, kontaktując się z [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/faq/) |
| Nadmiarowość geograficzna |Lokalnie nadmiarowy (wielu kopii danych w jednym regionie platformy Azure) |Lokalnie nadmiarowy (LRS), podzielić na strefy nadmiarowy (ZRS), globalnie nadmiarowy (GRS), dostęp do odczytu globalnie nadmiarowego (RA-GRS). Zobacz [tutaj](../storage/common/storage-redundancy.md) Aby uzyskać więcej informacji |
| Stan usługi |Ogólnie dostępna |Ogólnie dostępna |
| Dostępność regionalna |Zobacz [tutaj](https://azure.microsoft.com/regions/#services) |Dostępne we wszystkich regionach platformy Azure |
| Cena |Zobacz [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobacz [ceny](https://azure.microsoft.com/pricing/details/storage/) |


