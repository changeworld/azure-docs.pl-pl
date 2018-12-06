---
title: Znane problemy związane z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ograniczeniach i znanych problemach, za pomocą usługi Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: 83e9dfbe18dd79e8547e6b48daef39a5aed2cced
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975284"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy związane z usługi Azure Data Lake Storage Gen2

Ten artykuł zawiera znane problemy i ograniczenia tymczasowych za pomocą usługi Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Współdziałanie interfejsu API

Interfejsy API usługi blob Storage i interfejsów API usługi Azure Data Lake Gen2 — nie są współpracujący ze sobą.

Jeśli potrzebujesz używać tego samego narzędzia do pracy z całej zawartości, który przekaże do swojego konta, nie włączysz hierarchicznej przestrzeni nazw na konta usługi Blob storage do momentu te interfejsy API zostają współpracujący ze sobą. Przy użyciu konta magazynu bez hierarchicznej przestrzeni nazw oznacza, że nie masz dostępu do określonych funkcji Data Lake Storage Gen2, takich jak katalog i system plików następnie listy kontroli dostępu.

## <a name="blob-storage-apis"></a>Magazyn obiektów blob interfejsów API

Magazyn obiektów blob interfejsy API nie są jeszcze dostępne dla konta usługi Azure Data Lake Storage Gen 2.

Te interfejsy API są wyłączone, aby zapobiec nieumyślnemu danych problemów dotyczących dostępu, które mogą wystąpić, ponieważ interfejsy API usługi Blob Storage nie są jeszcze współpracujący z interfejsów API usługi Azure Data Lake Gen2.

Niezarządzane dyski maszyny wirtualnej (VM) zależą od tych interfejsów API, dlatego jeśli chcesz włączyć hierarchiczne przestrzenie nazw na koncie magazynu, należy rozważyć umieszczenie niezarządzanych dysków maszyn wirtualnych na konto magazynu, który nie ma włączonych hierarchicznej przestrzeni nazw.

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

Niektóre funkcje, w Eksploratorze usługi Storage jeszcze nie działają z systemem plików usługi Azure Data Lake Storage Gen2. Te ograniczenia mają zastosowanie do obu [autonomiczna wersja](https://azure.microsoft.com/features/storage-explorer/) Eksploratora usługi Azure Storage, a także wersji, która pojawia się w witrynie Azure portal.

## <a name="blob-viewing-tool"></a>Narzędzie do przeglądania obiektu blob

Obiekt blob, narzędzia do przeglądania w witrynie Azure portal ma ograniczoną obsługę usługi Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplikacje innych producentów

Aplikacje innych firm mogą nie obsługiwać usługi Azure Data Lake Storage Gen2.

Pomoc techniczna jest według uznania każdego dostawcy aplikacji innych firm. Obecnie usługa Blob storage interfejsów API i interfejsów API usługi Azure Data Lake Storage Gen2 — nie można użyć do zarządzania tą samą zawartością. Gdy będziemy pracować w celu umożliwienia współdziałania tego jest to możliwe, że wiele narzędzi innych firm będzie automatycznie obsługiwać usługi Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Obsługa narzędzia AzCopy

Narzędzie AzCopy w wersji 8 nie obsługuje usługi Azure Data Lake Storage Gen2.

Zamiast tego należy użyć najnowszej wersji wstępnej narzędzia AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) jak punktów końcowych usługi Azure Data Lake Storage Gen2.

## <a name="oauth-authentication"></a>Uwierzytelnianie OAuth

Usługi, takie jak usługi Azure Databricks, HDInsight i Azure Data Factory jeszcze nie integrować z tokenu uwierzytelniania elementu nośnego OAuth usługi Azure Active Directory (Azure AD).

## <a name="access-control-lists-acl"></a>Listy kontroli dostępu (ACL)

Katalog i listy kontroli dostępu na poziomie pliku (ACL) są trudne do zarządzania. Istnieje nie oparty na interfejsie użytkownika narzędzia, który służy do pobierania i ustawiania tych list kontroli dostępu.

## <a name="azure-event-grid"></a>Azure Event Grid

[Usługa Azure Event Grid](https://azure.microsoft.com/services/event-grid/) nie odbieranie zdarzeń z konta usługi Azure Data Lake Gen2, ponieważ te konta nie generują jeszcze je.  

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na roli nie można zastosować do obiektów systemu plików na koncie usługi Azure Data Lake Storage Gen2.

## <a name="sql-data-warehouse-polybase"></a>Program PolyBase magazynu danych SQL

Jeżeli zapór usługi Storage są włączone na koncie usługi Azure Storage, SQL Data Warehouse [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) nie może uzyskać dostępu tych kont.

## <a name="soft-delete-and-snapshots"></a>Usuwanie nietrwałe i migawki

Usuwanie nietrwałe i migawek nie są dostępne dla kont usługi Azure Data Lake Storage Gen2.

Wszystkie funkcje wersji, w tym [migawek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) i [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) nie są jeszcze dostępne dla konta magazynu, które mają włączonych hierarchicznej przestrzeni nazw.

## <a name="object-level-storage-tiers"></a>Warstwy magazynowania poziomu obiektu

Obiekt magazynu poziomu warstwy (gorąca, zimnego i archiwalna) nie są jeszcze dostępne dla kont usługi Azure Data Lake Storage Gen 2, ale są one dostępne dla kont magazynu, które nie mają hierarchiczne miejsca do magazynowania, które są włączone.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Zasady zarządzania (wersja zapoznawcza) cyklu życia w usłudze Azure Blob Storage

Zasady zarządzania (wersja zapoznawcza) cyklu życia w usłudze Azure Blob Storage nie są jeszcze dostępne dla kont usługi Azure Data Lake Storage Gen2.

Te zasady są dostępne dla kont magazynu, które nie mają hierarchiczne miejsca do magazynowania, które są włączone.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Dzienniki diagnostyczne nie są dostępne dla kont usługi Azure Data Lake Storage Gen2.

Aby zażądać dziennika diagnostycznego, skontaktuj się z działem pomocy technicznej systemu Azure. Udostępniaj im swojej nazwy konta i okres, dla którego wymagane dzienniki.
