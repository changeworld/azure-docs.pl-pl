---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4f9a828e5233c88db2106bc648c07578927e0d29
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514869"
---
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Służy do ustawiania własności i zarządzania kontrolą dostępu POSIX dla Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania dla zasobów magazynu obiektów BLOB.
- [Czytnik danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Służy do przyznawania uprawnień tylko do odczytu zasobom magazynu obiektów BLOB.
- [Współautor danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania dla kolejek platformy Azure.
- [Czytnik danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Użyj, aby przyznać uprawnienia tylko do odczytu kolejkom platformy Azure.
- [Procesor komunikatów danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Służy do przyznawania uprawnień wglądu, pobierania i usuwania do komunikatów w kolejkach usługi Azure Storage.
- [Nadawca komunikatu o danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Służy do przyznawania uprawnień do dodawania do komunikatów w kolejkach usługi Azure Storage.

> [!NOTE]
> Należy pamiętać, że propagacja ról RBAC może potrwać do 5 minut.
