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
ms.openlocfilehash: d97a2350765ac321cf77f8a9f84825c88d0c9185
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824257"
---
Platforma Azure udostępnia następujące wbudowane role kontroli RBAC do uzyskiwania dostępu do magazynu danych:

- [Właściciel danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Użyj, aby ustawić własność i zarządzania kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektu Blob Storage (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Współautor danych kolejki magazynu (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania do kolejek systemu Azure.
- [Czytnik danych kolejki magazynu (wersja zapoznawcza)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Użyj, aby udzielić uprawnień tylko do odczytu do kolejek systemu Azure.

Aby uzyskać więcej informacji o tym, jak wbudowane role są definiowane dla usługi Azure Storage, zobacz [zrozumienie definicji ról](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview).

Usługa Azure Storage obsługuje również niestandardowe role RBAC. Aby uzyskać więcej informacji, zobacz [tworzenie ról niestandardowych dla kontroli dostępu](../articles/role-based-access-control/custom-roles.md). 
