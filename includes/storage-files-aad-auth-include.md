---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854576"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Przyłączone do domeny maszyny wirtualne z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń [usługi Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Za pomocą [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md)można zarządzać dostępem na poziomie udziału Azure Files do tożsamości, takiej jak użytkownik lub grupa w usłudze Azure AD. Można zdefiniować niestandardowe role RBAC, które obejmują wspólne zestawy uprawnień, które są używane do uzyskiwania dostępu do Azure Files. Po przypisaniu niestandardowej roli RBAC do tożsamości usługi Azure AD tożsamość otrzymuje dostęp do udziału plików platformy Azure zgodnie z tymi uprawnieniami.

Azure Files obsługuje również zachowanie, dziedziczenie i wymuszanie [list DACL systemu plików NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) na wszystkich plikach i katalogach w udziale plików. W przypadku kopiowania danych z udziału plików do Azure Files lub na odwrót, można określić, że są zachowywane listy DACL systemu plików NTFS. W ten sposób można zaimplementować scenariusze tworzenia kopii zapasowych za pomocą Azure Files, zachowując poufne listy kontroli dostępu systemu plików NTFS między lokalnym udziałem plików a udziałem plików w chmurze. 

> [!NOTE]
> - Uwierzytelnianie AD DS platformy Azure dla dostępu bloku komunikatów serwera (SMB) nie jest obsługiwane w przypadku maszyn wirtualnych z systemem Linux. Obsługiwane są tylko maszyny wirtualne systemu Windows.
> - Uwierzytelnianie za pomocą usługi Azure AD DS dla dostępu SMB nie jest obsługiwane dla Active Directory maszyn przyłączonych do domeny. W tymczasowym systemie Rozważ użycie [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) , aby rozpocząć Migrowanie danych do Azure Files i kontynuować wymuszanie kontroli dostępu przy użyciu poświadczeń Active Directory z lokalnych Active Directory przyłączonych do domeny. 
> - Uwierzytelnianie za pomocą usługi Azure AD DS dla dostępu SMB jest dostępne tylko dla kont magazynu utworzonych po 24 września 2018.
> - Uwierzytelnianie za pomocą usługi Azure AD DS dla dostępu SMB i trwałego systemu plików NTFS nie jest obsługiwane w udziałach plików platformy Azure zarządzanych przez Azure File Sync.
> - Uwierzytelnianie za pomocą usługi Azure AD DS nie obsługuje uwierzytelniania dla kont komputerów utworzonych w usłudze Azure AD DS.
