---
title: Magazyn w chmurze umożliwiający tworzenie wysoce bezpiecznych, trwałych i skalowalnych aplikacji w usłudze Azure Storage
description: Dowiedz się więcej o usługach, które przechowują dane aplikacji mobilnej z dużą strukturą i niestrukturalną strukturą w chmurze.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795655"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Magazyn w chmurze dla wysoce bezpiecznych, trwałych i skalowalnych aplikacji z usługą Azure Storage
Usługa [Azure Storage](https://azure.microsoft.com/services/storage/) to rozwiązanie magazynu w chmurze firmy Microsoft dla nowoczesnych aplikacji, które oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn komunikatów dla niezawodnej obsługi komunikatów i Magazyn NoSQL. Usługa Azure Storage to:
- **Trwałe i wysoce dostępne** — nadmiarowość gwarantuje, że dane są bezpieczne w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.
- **Zabezpieczenia** — wszystkie dane zapisywane w usłudze Azure Storage są szyfrowane przez usługę. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalne** usługi zostały zaprojektowane tak, aby były wysoce skalowalne, aby sprostać wymaganiom magazynu i wydajności danych współczesnych aplikacji.
- **Zarządzany** — platforma Azure obsługuje konserwację sprzętu, aktualizacje i problemy krytyczne.
- Dane są **dostępne** z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub https. Firma Microsoft udostępnia biblioteki klienckie w różnych językach, w tym .NET, Java, Node. js, Python, PHP, Ruby, go i innych, a także interfejs API REST. Obsługa skryptów jest obsługiwana w Azure PowerShell lub interfejs wiersza polecenia platformy Azure, a Azure Portal i Eksplorator usługi Azure Storage oferuje łatwe rozwiązania wizualne umożliwiające pracę z danymi.

Użyj następujących usług, aby włączyć magazyn w chmurze w aplikacjach mobilnych.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Usługa Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) oferuje rozwiązanie magazynu obiektów dla chmury i jest zoptymalizowane pod kątem przechowywania dużych ilości danych bez struktury, które nie są zgodne z konkretnym modelem danych lub definicją, na przykład tekst lub dane binarne. Obsługuje ona biblioteki klienckie różnych języków i jest przeznaczona dla:
- Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
- Przechowywanie plików do dostępu rozproszonego.
- Przesyłanie strumieniowe audio i wideo.
- Zapisywanie plików dziennika.
- Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
- Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

**Wołują**
- [Azure Portal](https://portal.azure.com)
- [Łączoną](/azure/storage/blobs/storage-blobs-introduction)
- [Przewodniki Szybki start](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Przykłady](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) to usługa, która przechowuje strukturalne dane NoSQL w chmurze, zapewniając Magazyn kluczy/atrybutów z projektem bez schematu. Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Najczęstsze zastosowania usługi Table Storage to:
- Przechowywanie danych strukturalnych z możliwością obsługi aplikacji w skali sieci Web.
- Przechowywanie zestawów danych, które nie wymagają złożonych sprzężeń, kluczy obcych lub procedur składowanych i może być nieznormalizowane, aby uzyskać szybki dostęp.
- Szybkie wykonywanie zapytań dotyczących danych przy użyciu indeksu klastrowanego.
- Uzyskiwanie dostępu do danych przy użyciu protokołu OData i zapytań LINQ z bibliotekami .NET usługi danych programu WCF.

Usługa Table Storage służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Tabele mogą być skalowane wraz ze wzrostem wymagań.

**Wołują**
- [Azure Portal](https://portal.azure.com)
- [Łączoną](/azure/storage/tables/table-storage-overview)
- [Przykłady](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Przewodniki Szybki start](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Usługa Pliki systemu Azure
Usługa [Azure Files](https://azure.microsoft.com/services/storage/files/) umożliwia konfigurowanie wysoce dostępnych udziałów plików sieciowych, do których można uzyskiwać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB, Server Message Block). Wiele maszyn wirtualnych może współużytkować te same pliki z dostępem do odczytu i zapisu. Pliki można także odczytywać przy użyciu interfejsu REST lub bibliotek klienckich magazynu. Możesz uzyskać dostęp do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego (SAS). Tokeny sygnatury dostępu współdzielonego można generować. Umożliwiają one uzyskanie określonego dostępu do prywatnego zasobu przez określony czas.

Udziały plików platformy Azure mogą być używane w następujących celach:
- Zastępowanie lub uzupełnienie lokalnych serwerów plików: popularne systemy operacyjne, takie jak Windows, macOS i Linux, mogą bezpośrednio instalować udziały plików platformy Azure wszędzie tam, gdzie znajdują się na świecie. Udziały plików platformy Azure można również replikować za pomocą usługi Azure File Sync w systemach Windows Server, lokalnie lub w chmurze, w celu zapewnienia wydajnego i rozproszonego buforowania danych w miejscu ich używania.
- Przenosząc i przenosząc **aplikacje** do chmury, które oczekują udziału plików na przechowywanie plików aplikacji lub danych użytkownika.
- **Uprość programowanie w chmurze**: Azure Files mogą być również używane na wiele sposobów w celu uproszczenia nowych projektów programistycznych w chmurze. Na przykład:
    - Ustawienia aplikacji udostępnionej: typowym wzorcem aplikacji rozproszonych jest posiadanie plików konfiguracji w scentralizowanej lokalizacji, w której można uzyskać do nich dostęp z wielu wystąpień aplikacji. Wystąpienia aplikacji mogą ładować swoje konfiguracje za pomocą interfejsu API REST plików i ludzie mogą uzyskiwać do nich dostęp w razie potrzeby przez zainstalowanie udziału SMB lokalnie.
    - Udział diagnostyczny: udział plików platformy Azure jest wygodnym miejscem do zapisywania dzienników, metryk i zrzutów awaryjnych aplikacji w chmurze. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pomocą interfejsu API REST pliku, a deweloperzy mogą uzyskiwać do nich dostęp przez zainstalowanie udziału plików na komputerze lokalnym. Zapewnia to dużą elastyczność, ponieważ deweloperzy mogą obsługiwać opracowywanie rozwiązań dla chmury bez konieczności porzucania istniejących narzędzi, które znają i lubią.

**Wołują**
- [Azure Portal](https://portal.azure.com)
- [Łączoną](/azure/storage/files/storage-files-introduction)
- [Przewodniki Szybki start](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure Queues
[Azure queue storage](https://azure.microsoft.com/services/storage/queues/) to usługa do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć długość do 64 KB, a Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

**Wołują**
- [Azure Portal](https://portal.azure.com)
- [Łączoną](/azure/storage/queues/)
- [Przewodniki Szybki start](/azure/storage/queues/storage-quickstart-queues-portal)
- [Przykłady](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
