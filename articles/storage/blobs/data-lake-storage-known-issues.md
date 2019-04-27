---
title: Znane problemy związane z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ograniczeniach i znanych problemach, za pomocą usługi Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: d56fb411eb032e5e6227d68cd8abe02c0e30850b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708468"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy związane z usługi Azure Data Lake Storage Gen2

Ten artykuł zawiera znane problemy i ograniczenia tymczasowe Data Lake Storage Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Obsługa zestawu SDK dla kont usługi Data Lake Storage Gen2

Nie ma dostępne są zestawy SDK, które będą współdziałać z konta Data Lake Storage Gen2.

## <a name="blob-storage-apis"></a>Magazyn obiektów blob interfejsów API

Magazyn obiektów blob interfejsy API nie są jeszcze dostępne dla konta Data Lake Storage Gen2.

Te interfejsy API są wyłączone, aby zapobiec nieumyślnemu danych problemów dotyczących dostępu, które mogą wystąpić, ponieważ interfejsy API usługi Blob Storage nie są jeszcze współpracujący z interfejsów API usługi Azure Data Lake Gen2.

Jeśli te interfejsy API są używane do ładowania danych, zanim zostały wyłączone, a nie produkcyjne do nich dostęp, następnie skontaktuj się z Microsoft Support z następującymi informacjami:

* Identyfikator subskrypcji (identyfikator GUID, a nie nazwę)

* Nazwy kont magazynu

* Czy aktywnie dotyczy Cię w środowisku produkcyjnym, a jeśli tak, dla których kont magazynu?

* Nawet wtedy, gdy użytkownik nie aktywnie dotyczą w środowisku produkcyjnym, powiedz nam, czy potrzebujesz tych danych do skopiowania do innego konta magazynu z jakiegoś powodu i dlaczego?

W tych okolicznościach firma Microsoft może przywrócenia dostępu do interfejsu API obiektu Blob na pewien czas tak, aby można było skopiować te dane do konta magazynu, który nie ma włączoną funkcję hierarchicznej przestrzeni nazw.

Niezarządzane dyski maszyny wirtualnej (VM) zależą od wyłączone interfejsów API Blob Storage, dlatego jeśli chcesz włączyć hierarchicznej przestrzeni nazw na koncie magazynu, należy rozważyć umieszczenie niezarządzanych dysków maszyn wirtualnych na konto magazynu, który nie ma funkcji hierarchicznej przestrzeni nazw włączone.

## <a name="api-interoperability"></a>Współdziałanie interfejsu API

Interfejsy API usługi blob Storage i interfejsów API usługi Azure Data Lake Gen2 — nie są współpracujący ze sobą.

Jeśli dysponujesz narzędziami, aplikacji, usług lub skryptów, które korzystają z interfejsów API usługi Blob, a chcesz ich używać do pracy z całej zawartości, który przekaże do swojego konta, następnie nie włączaj hierarchicznej przestrzeni nazw w ramach konta magazynu obiektów Blob, aż staną się interfejsów API usługi Blob współdziała z interfejsów API usługi Azure Data Lake Gen2. Przy użyciu konta magazynu bez hierarchicznej przestrzeni nazw oznacza, że nie masz dostępu do Data Lake Storage Gen2 określone funkcje, takie jak listy kontroli dostępu systemu plików i katalogów następnie.

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

Aby wyświetlić lub zarządzać konta Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage, użytkownik musi mieć co najmniej wersji `1.6.0` narzędzia, który jest dostępny jako [bezpłatne materiały do pobrania](https://azure.microsoft.com/features/storage-explorer/).

Należy zauważyć, że wersja programu Storage Explorer, które jest wbudowane w witrynie Azure portal jest aktualnie nie obsługuje wyświetlanie i zarządzanie kontami Data Lake Storage Gen2 z włączoną funkcją hierarchicznej przestrzeni nazw.

## <a name="blob-viewing-tool"></a>Narzędzie do przeglądania obiektu blob

Narzędzia do przeglądania w witrynie Azure portal obiektu blob tylko ma ograniczoną obsługę Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplikacje innych producentów

Aplikacje innych firm mogą nie obsługiwać Data Lake Storage Gen2.

Pomoc techniczna jest według uznania każdego dostawcy aplikacji innych firm. Obecnie usługa Blob storage interfejsów API i interfejsy API usługi Data Lake Storage Gen2 — nie można użyć do zarządzania tą samą zawartością. Gdy będziemy pracować w celu umożliwienia współdziałania tego jest to możliwe, że wiele narzędzi innych firm będzie automatycznie obsługiwać Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Obsługa narzędzia AzCopy

Narzędzie AzCopy w wersji 8 nie obsługuje Data Lake Storage Gen2.

Zamiast tego należy użyć najnowszej wersji wstępnej narzędzia AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), ponieważ obsługuje on Data Lake Storage Gen2 punktów końcowych.

## <a name="azure-event-grid"></a>Azure Event Grid

[Usługa Azure Event Grid](https://azure.microsoft.com/services/event-grid/) nie odbieranie zdarzeń z konta usługi Azure Data Lake Gen2, ponieważ te konta nie generują jeszcze je.  

## <a name="soft-delete-and-snapshots"></a>Usuwanie nietrwałe i migawki

Usuwanie nietrwałe i migawek nie są dostępne dla kont usługi Data Lake Storage Gen2.

Wszystkie funkcje wersji, w tym [migawek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) i [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) nie są jeszcze dostępne dla kont magazynu, które mają włączoną funkcję hierarchicznej przestrzeni nazw.

## <a name="object-level-storage-tiers"></a>Warstwy magazynowania poziomu obiektu

Obiekt magazynu poziomu warstwy (gorąca, zimnego i archiwalna) nie są jeszcze dostępne dla kont usługi Azure Data Lake Storage Gen 2, ale są one dostępne dla kont magazynu, które nie mają włączoną funkcję hierarchicznej przestrzeni nazw.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Zasady zarządzania cyklem życia w usłudze Azure Blob Storage

Zasady zarządzania cyklem życia w usłudze Azure Blob Storage nie są jeszcze dostępne dla kont usługi Data Lake Storage Gen2.

Te zasady są dostępne dla kont magazynu, które nie mają włączoną funkcję hierarchicznej przestrzeni nazw.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Dzienniki diagnostyczne nie są dostępne dla kont usługi Data Lake Storage Gen2.
