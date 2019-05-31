---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237742"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) za pośrednictwem [usługi domenowe Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu [usługi Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) poświadczeń. 

Usługa Azure AD uwierzytelnia tożsamości, takie jak użytkownika, grupy lub nazwy głównej usługi z [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md). Można zdefiniować niestandardowe role RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do usługi Azure Files. Podczas przypisywania usługi niestandardową rolę RBAC do tożsamości usługi Azure AD, że tożsamość ma uprawnienia do udziału plików platformy Azure zgodnie z tych uprawnień.

Podczas korzystania z wersji zapoznawczej usługi Azure Files obsługuje również zachowania, dziedziczenie i wymuszanie [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na wszystkich plików i katalogów w udziale plików. Jeśli kopiowanie danych z udziału plików do usługi Azure Files lub odwrotnie, można określić, że DACL systemu plików NTFS są obsługiwane. W ten sposób można zaimplementować scenariusze tworzenia kopii zapasowej przy użyciu usługi Azure Files, zachowując swoje DACL NTFS między udziału plików lokalnych i chmurze udziału plików. 

> [!NOTE]
> - Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB nie jest obsługiwane dla maszyn wirtualnych systemu Linux w wersji zapoznawczej. Obsługiwane są tylko systemu Windows Server dla maszyn wirtualnych.
> - Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB nie jest obsługiwane dla maszyn lokalnych, uzyskiwanie dostępu do usługi Azure Files.
> - Uwierzytelnianie usługi Azure AD jest dostępna tylko w przypadku kont magazynu utworzonych po 24 września 2018 r.
> - Uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB i trwałych ACL systemu plików NTFS nie jest obsługiwana w udziałach plików platformy Azure zarządzane przez usługi Azure File Sync. 
