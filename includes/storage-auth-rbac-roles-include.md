---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218094"
---
Platforma Azure udostępnia następujące wbudowane role kontroli RBAC do uzyskiwania dostępu do magazynu danych:

- [Właściciel danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Użyj, aby ustawić własność i zarządzania kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Współautor danych kolejki magazynu (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania do kolejek systemu Azure.
- [Czytnik danych kolejki magazynu (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do kolejek systemu Azure.

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Aby uzyskać informacji na temat tworzenia niestandardowych ról RBAC, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](../articles/role-based-access-control/custom-roles.md). 
