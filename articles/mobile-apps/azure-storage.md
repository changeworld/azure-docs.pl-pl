---
title: Magazyn w chmurze do tworzenia wysoce bezpiecznych, trwałych i skalowalnych aplikacji za pomocą usługi Azure Storage
description: Dowiedz się więcej o usługach do przechowywania dużych danych z ustrukturyzowanych i niestrukturalnych aplikacji mobilnych w chmurze.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240974"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Pamięć masowa w chmurze dla wysoce bezpiecznych, trwałych i skalowalnych aplikacji z usługą Azure Storage
[Usługa Azure Storage](https://azure.microsoft.com/services/storage/) to rozwiązanie magazynu w chmurze firmy Microsoft dla nowoczesnych aplikacji, które oferuje masowo skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn wiadomości dla niezawodnych wiadomości i magazyn NoSQL. Usługa Azure Storage to:
- **Trwałe i wysoce dostępne:** Nadmiarowość zapewnia, że dane są bezpieczne w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.
- **Bezpieczne:** Wszystkie dane zapisane w usłudze Azure Storage są szyfrowane przez usługę. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalne:** Usługi są zaprojektowane tak, aby były skalowalne, aby sprostać potrzebom w zakresie przechowywania danych i wydajności dzisiejszych aplikacji.
- **Zarządzane:** Platforma Azure obsługuje konserwację sprzętu, aktualizacje i krytyczne problemy.
- **Dostępne:** Dane są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia biblioteki klienckie w różnych językach, takich jak .NET, Java, Node.js, Python, PHP, Ruby i Go oraz dojrzały interfejs API REST. Skrypty są obsługiwane w programie Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Portal Azure i Eksplorator usługi Azure Storage oferują łatwe rozwiązania wizualne do pracy z danymi.

Skorzystaj z następujących usług, aby włączyć pamięć masową w chmurze w aplikacjach mobilnych.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Usługa Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) oferuje rozwiązanie magazynu obiektów dla chmury. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania ogromnych ilości nieustrukturyzowanych danych, które nie są zgodne z określonym modelem danych lub definicją, taką jak tekst lub plik binarny. Obsługuje wiele języków, które używają biblioteki klienckie. Magazyn obiektów blob jest przeznaczony do:
- Obsługa obrazów lub dokumentów bezpośrednio w przeglądarce.
- Przechowuj pliki dla dostępu rozproszonego.
- Przesyłaj strumieniowo wideo i audio.
- Zapis do plików dziennika.
- Przechowuj dane do tworzenia kopii zapasowych i przywracania, odzyskiwania po awarii i archiwizacji.
- Przechowywanie danych do analizy przez lokalną lub hostowane na platformie Azure.

**Odwołania**
- [Portal Azure](https://portal.azure.com)
- [Dokumentacja usługi Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Szybki start](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Próbki](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Usługa Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) to usługa, która przechowuje ustrukturyzowane dane NoSQL w chmurze i zapewnia magazyn kluczy lub atrybutów z projektem bez schematu. Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele platformy Azure są idealne do przechowywania danych strukturalnych, nierelacyjnych. Magazyn tabel jest zwykle używany do:
- Przechowywanie terabajtów danych strukturalnych umożliwiających obsługę aplikacji w skali sieci Web.
- Przechowuj zestawy danych, które nie wymagają złożonych sprzężeń, kluczy obcych lub procedur przechowywanych i mogą być zdenormalizowane w celu szybkiego dostępu.
- Szybko wysyłaj zapytania o dane przy użyciu indeksu klastrowanego.
- Dostęp do danych przy użyciu protokołu OData i zapytań LINQ z bibliotekami .NET usług Windows Communication Foundation (WCF).

Magazyn tabel służy do przechowywania i wykonywania zapytań o ogromne zestawy danych strukturalnych, nierelacyjnych. Tabele są skalowane wraz ze wzrostem popytu.

**Odwołania**
- [Portal Azure](https://portal.azure.com)
- [Dokumentacja usługi Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Próbki](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Szybki start](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Za [pomocą usługi Azure Files](https://azure.microsoft.com/services/storage/files/)można skonfigurować udziały plików sieciowych o wysokiej dostępności, do których można uzyskać dostęp przy użyciu standardowego protokołu Bloku Komunikatów serwera (SMB). Wiele maszyn wirtualnych może udostępniać te same pliki z dostępem do odczytu i zapisu. Można również odczytać pliki przy użyciu interfejsu REST lub bibliotek klienta magazynu. Dostęp do plików z dowolnego miejsca na świecie można uzyskać przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego (SAS). Można wygenerować tokeny sygnatury dostępu Współdzielonego. Umożliwiają one określony dostęp do prywatnego zasobu przez określony czas.

Udziały plików platformy Azure mogą być używane w następujących celach:
- **Zastąp lub uzupełnij lokalne serwery plików:** Popularne systemy operacyjne, takie jak Windows, macOS i Linux, mogą bezpośrednio montować udziały plików platformy Azure w dowolnym miejscu na świecie. Udziały plików platformy Azure można również replikować za pomocą usługi Azure File Sync w systemach Windows Server, lokalnie lub w chmurze, w celu zapewnienia wydajnego i rozproszonego buforowania danych w miejscu ich używania.
- **Zastosowania w podnoszeniu i przesunięciu:** Migruj aplikacje do chmury, które oczekują udziału plików do przechowywania aplikacji plików lub danych użytkownika.
- **Uprość tworzenie chmury:** Usługa Azure Files może być również używana na wiele sposobów, aby uprościć nowe projekty programistyczne w chmurze. Przykład:
    - **Udostępnione ustawienia aplikacji:** Typowym wzorcem dla aplikacji rozproszonych jest mieć pliki konfiguracyjne w scentralizowanej lokalizacji, gdzie można uzyskać do nich dostęp z wielu wystąpień aplikacji. Wystąpienia aplikacji można załadować ich konfiguracji za pośrednictwem interfejsu API FILE REST. Użytkownicy mogą uzyskać do nich dostęp w razie potrzeby, montując udział SMB lokalnie.
    - **Udział diagnostyczny:** Udział plików platformy Azure jest wygodnym miejscem dla aplikacji w chmurze do zapisywania swoich dzienników, metryk i zrzutów awaryjnych. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pośrednictwem interfejsu API FILE REST. Deweloperzy mogą uzyskać do nich dostęp, montując udział plików na komputerze lokalnym. Ta funkcja zapewnia dużą elastyczność. Deweloperzy mogą korzystać z tworzenia chmury bez konieczności porzucania istniejących narzędzi, które znają.

**Odwołania**
- [Portal Azure](https://portal.azure.com)
- [Dokumentacja usługi Azure Files](/azure/storage/files/storage-files-introduction)
- [Szybki start](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Usługa Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) to usługa do przechowywania dużej liczby wiadomości. Dostęp do wiadomości z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie.

**Odwołania**
- [Portal Azure](https://portal.azure.com)
- [Dokumentacja usługi Azure Queue Storage](/azure/storage/queues/)
- [Szybki start](/azure/storage/queues/storage-quickstart-queues-portal)
- [Próbki](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
