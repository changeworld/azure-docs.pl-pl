---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449754"
---
Platforma Azure udostępnia następujące wbudowane role kontroli RBAC do uzyskiwania dostępu do magazynu danych:

- [Właściciel danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Użyj, aby ustawić własność i zarządzania kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Storage Queue Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania do kolejek systemu Azure.
- [Storage Queue Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do kolejek systemu Azure.
- [Dane w kolejce magazynu komunikatów procesora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Umożliwia udzielanie wglądu, pobieranie i uprawnienia do usuwania komunikatów w kolejkach usługi Azure Storage.
- [Dane w kolejce magazynu komunikatów nadawcy](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Użyj, aby przyznać uprawnienia dodać do wiadomości w kolejce usługi Azure Storage.

> [!IMPORTANT]
> Przypisania ról RBAC może potrwać do pięciu minut na propagację.

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Aby uzyskać informacji na temat tworzenia niestandardowych ról RBAC, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](../articles/role-based-access-control/custom-roles.md). 
