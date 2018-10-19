---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ec687580eb86db9df77a657dedc4feec1dbb2b2f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430392"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) za pośrednictwem [usługi domenowe Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/active-directory-ds-overview.md). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu [usługi Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) poświadczeń. 

Usługa Azure AD uwierzytelnia tożsamości, takie jak użytkownika, grupy lub nazwy głównej usługi z [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md). Można zdefiniować niestandardowe role RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do usługi Azure Files. Podczas przypisywania usługi niestandardową rolę RBAC do tożsamości usługi Azure AD, że tożsamość ma uprawnienia do udziału plików platformy Azure zgodnie z tych uprawnień.

Podczas korzystania z wersji zapoznawczej usługi Azure Files obsługuje również zachowania, dziedziczenie i wymuszanie [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na wszystkich plików i katalogów w udziale plików. Jeśli kopiowanie danych z udziału plików do usługi Azure Files lub odwrotnie, można określić, że DACL systemu plików NTFS są obsługiwane. W ten sposób można zaimplementować scenariusze tworzenia kopii zapasowej przy użyciu usługi Azure Files, zachowując swoje DACL NTFS między udziału plików lokalnych i chmurze udziału plików. 

> [!NOTE]
> - Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB nie jest obsługiwane dla maszyn wirtualnych systemu Linux w wersji zapoznawczej. Obsługiwane są tylko systemu Windows Server dla maszyn wirtualnych.
> - Uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB nie jest obsługiwana dla maszyn lokalnych, uzyskiwanie dostępu do usługi Azure Files przy użyciu albo usługi AD lub poświadczeń usługi AAD.
> - Uwierzytelnianie usługi Azure AD jest dostępna tylko w przypadku kont magazynu utworzonych po 24 września 2018 r.
