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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483605"
---
System Azure oferuje następujące wbudowane role kontroli RBAC do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i OAuth:

- [Właściciel danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Użyj, aby ustawić własność i zarządzania kontroli dostępu POSIX dla usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektu Blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Użyj, aby udzielić uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Storage Queue Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Użyj, aby udzielić uprawnień odczytu/zapisu/usuwania do kolejek systemu Azure.
- [Storage Queue Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Użyj, aby udzielić uprawnień tylko do odczytu do kolejek systemu Azure.
- [Dane w kolejce magazynu komunikatów procesora](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Umożliwia udzielanie wglądu, pobieranie i uprawnienia do usuwania komunikatów w kolejkach usługi Azure Storage.
- [Dane w kolejce magazynu komunikatów nadawcy](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Użyj, aby przyznać uprawnienia dodać do wiadomości w kolejce usługi Azure Storage.

> [!NOTE]
> Należy pamiętać, że przypisania ról RBAC może potrwać do pięciu minut na propagację.
