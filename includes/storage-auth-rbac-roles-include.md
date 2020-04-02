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
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519576"
---
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i OAuth:

- [Właściciel danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)służy do ustawiania własności i zarządzania kontrolą dostępu POSIX dla usługi Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor)służy do udzielania uprawnień do odczytu/zapisu/usuwania zasobów magazynu obiektów Blob.
- [Czytnik danych obiektów blob magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)Służy do udzielania uprawnień tylko do odczytu do zasobów magazynu obiektów Blob.
- [Współautor danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)służy do udzielania uprawnień do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytnik danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Służy do udzielania uprawnień tylko do odczytu do kolejek platformy Azure.
- [Procesor komunikatów o danych kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)służy do udzielania zaglądanie, pobieranie i usuwanie uprawnień do wiadomości w kolejkach usługi Azure Storage.
- [Nadawca wiadomości z danymi kolejki magazynu:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)służy do udzielania uprawnień do dodawania wiadomości w kolejkach usługi Azure Storage.

Aby uzyskać szczegółowe informacje na temat wbudowanych ról RBAC dla usługi Azure Storage dla usług azure i usługi zarządzania, zobacz sekcję **Magazyn** [w wbudowanych ról platformy Azure dla usługi Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage) Ponadto aby uzyskać informacje na temat różnych typów ról, które zapewniają uprawnienia na platformie Azure, zobacz [klasyczne role administratora subskrypcji, role usługi Azure RBAC i role usługi Azure AD.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!NOTE]
> Propagowanie przydziałów ról RBAC może potrwać do pięciu minut.
>
> Tylko role jawnie zdefiniowane dla dostępu do danych umożliwiają podmiotowi zabezpieczeń dostęp do danych obiektów blob lub kolejek. Role, takie jak **Właściciel,** **Współautor**i **Współautor konta magazynu,** umożliwiają podmiotowi zabezpieczeń zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu blob lub kolejki w ramach tego konta.
>
> Dostęp do danych obiektów blob lub kolejek w witrynie Azure portal może być autoryzowany przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [Dostęp do danych obiektów blob lub kolejek za pomocą witryny Azure Portal.](../articles/storage/common/storage-access-blobs-queues-portal.md)
