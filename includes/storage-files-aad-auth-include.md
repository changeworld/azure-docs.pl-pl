---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 903074c78180ab2cd755abcf4207232f2851804e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47019697"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) za pośrednictwem [usługi domenowe Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/active-directory-ds-overview.md). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu [usługi Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) poświadczeń. 

Usługa Azure AD uwierzytelnia tożsamości, takie jak użytkownika, grupy lub nazwy głównej usługi z [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md). Można zdefiniować niestandardowe role RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do usługi Azure Files. Podczas przypisywania usługi niestandardową rolę RBAC do tożsamości usługi Azure AD, że tożsamość ma uprawnienia do udziału plików platformy Azure zgodnie z tych uprawnień.

Podczas korzystania z wersji zapoznawczej usługi Azure Files obsługuje również zachowania, dziedziczenie i wymuszanie [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na wszystkich plików i katalogów w udziale plików. Jeśli kopiowanie danych z udziału plików do usługi Azure Files lub odwrotnie, można określić, że DACL systemu plików NTFS są obsługiwane. W ten sposób można zaimplementować scenariusze tworzenia kopii zapasowej przy użyciu usługi Azure Files, zachowując swoje DACL NTFS między udziału plików lokalnych i chmurze udziału plików. 

> [!NOTE]
> Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB nie jest obsługiwane dla maszyn wirtualnych systemu Linux w wersji zapoznawczej. Obsługiwane są tylko systemu Windows Server dla maszyn wirtualnych.
>
> Uwierzytelnianie usługi Azure AD jest dostępna tylko w przypadku kont magazynu utworzonych po 24 września 2018 r.
