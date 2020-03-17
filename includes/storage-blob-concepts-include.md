---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136022"
---
Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne.

## <a name="about-blob-storage"></a>Informacje o usłudze Blob Storage

Przeznaczenie usługi Blob Storage:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie plików dziennika.
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów w magazynie obiektów blob za pośrednictwem protokołu HTTP/HTTPS z dowolnego miejsca na świecie. Obiekty w magazynie obiektów blob są dostępne za pośrednictwem [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienckie są dostępne dla różnych języków, w tym:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Informacje o usłudze Azure Data Lake Storage Gen2

Magazyn obiektów blob obsługuje usługę Azure Data Lake Storage Gen2 — rozwiązanie firmy Microsoft do analizy danych big data dla przedsiębiorstw w chmurze. Usługa Azure Data Lake Storage Gen2 zapewnia hierarchiczny system plików oraz zalety magazynu obiektów blob, w tym:

* Ekonomiczny magazyn warstwowy
* Wysoka dostępność
* Silna spójność
* Możliwości odzyskiwania po awarii

Aby uzyskać więcej informacji na temat usługi Data Lake Storage Gen2, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
