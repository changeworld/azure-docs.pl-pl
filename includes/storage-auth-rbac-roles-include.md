---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279513"
---
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i OAuth:

- [Właściciel danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)służy do ustawiania własności i zarządzania kontrolą dostępu POSIX dla usługi Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)służy do udzielania uprawnień do odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)Służy do udzielania uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Współautor danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)służy do udzielania uprawnień do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytnik danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Służy do udzielania uprawnień tylko do odczytu do kolejek platformy Azure.
- [Procesor komunikatów o danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)służy do udzielania zaglądanie, pobieranie i usuwanie uprawnień do wiadomości w kolejkach usługi Azure Storage.
- [Nadawca wiadomości z danymi kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)służy do udzielania uprawnień do dodawania wiadomości w kolejkach usługi Azure Storage.

> [!NOTE]
> Propagowanie przydziałów ról RBAC może potrwać do pięciu minut.
>
> Tylko role jawnie zdefiniowane dla dostępu do danych umożliwiają podmiotowi zabezpieczeń dostęp do danych obiektów blob lub kolejek. Role, takie jak **Właściciel,** **Współautor**i **Współautor konta magazynu,** umożliwiają podmiotowi zabezpieczeń zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu blob lub kolejki w ramach tego konta.
>
> Dostęp do danych obiektów blob lub kolejek w witrynie Azure portal może być autoryzowany przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [Dostęp do danych obiektów blob lub kolejek za pomocą witryny Azure Portal.](../articles/storage/common/storage-access-blobs-queues-portal.md)
