---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269366"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) za pośrednictwem [usługi domenowe Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu [usługi Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) poświadczeń. 

Możesz zarządzać udziału plików platformy Azure poziom dostępu do tożsamości, takich jak użytkownik, grupa w usłudze Azure AD za pomocą [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md). Można zdefiniować niestandardowe role RBAC, które obejmują typowe zestawy uprawnień, które umożliwiają dostęp do usługi Azure Files. Podczas przypisywania usługi niestandardową rolę RBAC do tożsamości usługi Azure AD, że tożsamość ma uprawnienia do udziału plików platformy Azure zgodnie z tych uprawnień.

Podczas korzystania z wersji zapoznawczej usługi Azure Files obsługuje również zachowania, dziedziczenie i wymuszanie [DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na wszystkich plików i katalogów w udziale plików. Jeśli kopiowanie danych z udziału plików do usługi Azure Files lub odwrotnie, można określić, że DACL systemu plików NTFS są obsługiwane. W ten sposób można zaimplementować scenariusze tworzenia kopii zapasowej przy użyciu usługi Azure Files, zachowując swoje DACL NTFS między udziału plików lokalnych i chmurze udziału plików. 

> [!NOTE]
> - Uwierzytelnianie usług domenowych AD systemu Azure na dostęp przez protokół SMB jest nieobsługiwany dla maszyn wirtualnych systemu Linux. Obsługiwane są tylko maszyny wirtualne systemu Windows.
> - Uwierzytelnianie usług domenowych AD systemu Azure na dostęp przez protokół SMB nie jest obsługiwana na komputerze dołączonym do domeny usługi Active Directory. W międzyczasie możesz rozważyć wykorzystanie [Azure Files Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) rozpocząć migrację danych do usługi Azure Files i wymuszanie kontroli dostępu przy użyciu podania AD poświadczeń z sieci lokalnej w dalszym ciągu przyłączone do domeny AD maszyn. 
> - Uwierzytelnianie usług domenowych AD systemu Azure na dostęp przez protokół SMB jest dostępna tylko w przypadku kont magazynu utworzonych po 24 września 2018 r.
> - Uwierzytelnianie usług domenowych AD systemu Azure dostęp przez protokół SMB i trwałych DACL systemu plików NTFS nie jest obsługiwana w udziałach plików platformy Azure zarządzane przez usługi Azure File Sync. 
