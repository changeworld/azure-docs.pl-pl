---
title: Porównanie usługi Azure Data Lake Storage Gen1 z obiektem Blob usługi Azure Storage | Dokumenty firmy Microsoft
description: Porównanie usługi Azure Data Lake Storage Gen1 z obiektem Blob usługi Azure Storage
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438719"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porównanie usługi Azure Data Lake Storage Gen1 i usługi Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

W tabeli w tym artykule podsumowano różnice między usługą Azure Data Lake Storage Gen1 i Usługi Azure Blob Storage w niektórych kluczowych aspektach przetwarzania dużych zbiorów danych. Usługa Azure Blob Storage to magazyn obiektów ogólnego przeznaczenia, skalowalny, który jest przeznaczony dla wielu różnych scenariuszy magazynu. Usługa Azure Data Lake Storage Gen1 to repozytorium hiperskali zoptymalizowane pod kątem obciążeń analizy dużych zbiorów danych.

|  | Usługa Azure Data Lake Storage 1. generacji | Azure Blob Storage |
| --- | --- | --- |
| Przeznaczenie |Zoptymalizowana pamięć masowa dla obciążeń analizy dużych zbiorów danych |Magazyn obiektów ogólnego przeznaczenia dla szerokiej gamy scenariuszy pamięci masowej, w tym analizy dużych zbiorów danych |
| Przypadki użycia |Dane wsadowe, interaktywne, analizy strumieniowej i uczenia maszynowego, takie jak pliki dziennika, dane IoT, strumienie kliknięć, duże zestawy danych |Każdy typ tekstu lub danych binarnych, takich jak zaplecze aplikacji, dane kopii zapasowej, przechowywanie multimediów do przesyłania strumieniowego i dane ogólnego przeznaczenia. Ponadto pełne wsparcie dla obciążeń analitycznych; dane wsadowe, interaktywne, analizy strumieniowej i uczenia maszynowego, takie jak pliki dziennika, dane IoT, strumienie kliknięć, duże zestawy danych |
| Kluczowe pojęcia |Konto Data Lake Storage Gen1 zawiera foldery, które z kolei zawierają dane przechowywane jako pliki |Konto magazynowe ma kontenery, które z kolei mają dane w postaci obiektów blob |
| Struktura |Hierarchiczny system plików |Magazyn obiektów z płaską przestrzenią nazw |
| interfejs API |INTERFEJS API REST za pośrednictwem protokołu HTTPS |INTERFEJS API REST za pośrednictwem protokołu HTTP/HTTPS |
| Interfejs API po stronie serwera |[Interfejs API REST zgodny z usługą WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systemu plików Hadoop |Tak |Tak |
| Operacje na danych — uwierzytelnianie |Na podstawie [tożsamości usługi Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Na podstawie udostępnionych wpisów tajnych — [klucze dostępu do konta](../storage/common/storage-account-keys-manage.md) i [klucze podpisu dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operacje na danych — protokół uwierzytelniania |2.0. Wywołania muszą zawierać prawidłowy token JWT (JSON Web Token) wystawiony przez usługę Azure Active Directory |Kod uwierzytelniania wiadomości oparty na skrótach (HMAC) . Wywołania muszą zawierać skrót SHA-256 zakodowany w bazie Base4 przez część żądania HTTP. |
| Operacje danych — autoryzacja |Listy kontroli dostępu POSIX (ACL).  Listy ACL oparte na tożsamościach usługi Azure Active Directory można ustawić na poziomie pliku i folderu. |Autoryzacja na poziomie konta — użyj [kluczy dostępu do konta](../storage/common/storage-account-keys-manage.md)<br>W przypadku autoryzacji konta, kontenera lub obiektu blob — używanie [kluczy podpisu dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operacje na danych — inspekcja |Dostępne. Zobacz [tutaj,](data-lake-store-diagnostic-logs.md) aby uzyskać informacje. |Dostępne |
| Dane szyfrowania w stanie spoczynku |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi usługami</li><li>Klucze zarządzane przez klienta w usłudze Azure KeyVault</li></ul></ul> |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi usługami</li><li>Klucze zarządzane przez klienta w usłudze Azure KeyVault (wersja zapoznawcza)</li></ul><li>Szyfrowania po stronie klienta</li></ul> |
| Operacje zarządzania (np. tworzenie konta) |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) dostarczana przez platformę Azure w celu zarządzania kontami |[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) dostarczana przez platformę Azure w celu zarządzania kontami |
| Zestaw SDK dla deweloperów |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Wydajność obciążenia analitycznego |Zoptymalizowana wydajność dla obciążeń analizy równoległej. Wysoka przepływność i we/wy. |Zoptymalizowana wydajność dla obciążeń analizy równoległej. |
| Limity rozmiaru |Brak limitów rozmiarów kont, rozmiarów plików lub liczby plików |Aby uzyskać określone limity, zobacz [Cele skalowalności dla standardowych kont magazynu](../storage/common/scalability-targets-standard-account.md) oraz cele [skalowalności i wydajności magazynu obiektów Blob](../storage/blobs/scalability-targets.md). Większe limity kont dostępne po skontaktowaniu się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/) |
| Nadmiarowość geograficzna |Lokalnie nadmiarowe (wiele kopii danych w jednym regionie platformy Azure) |Lokalnie nadmiarowe (LRS), strefa nadmiarowa (ZRS), globalnie nadmiarowe (GRS), dostęp do odczytu globalnie nadmiarowy (RA-GRS). Zobacz [tutaj,](../storage/common/storage-redundancy.md) aby uzyskać więcej informacji |
| Stan usługi |Ogólnie dostępne |Ogólnie dostępne |
| Dostępność regionalna |Zobacz [tutaj](https://azure.microsoft.com/regions/#services) |Dostępne we wszystkich regionach platformy Azure |
| Price |Zobacz [cennik](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobacz [cennik](https://azure.microsoft.com/pricing/details/storage/) |


