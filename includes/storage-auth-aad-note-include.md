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
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372669"
---
> [!NOTE]
> - Przypisania ról RBAC może potrwać do dwóch minut na propagację.
>
> Aby autoryzować obiektów blob i kolejek operacji przy użyciu tokenu OAuth, musi używać protokołu HTTPS.
>
> - Azure portal obsługuje teraz, przy użyciu poświadczeń usługi Azure AD na odczytywanie i zapisywanie obiektów blob i kolejki danych, jako część wersji zapoznawczej wersji. Aby uzyskać dostęp do danych obiektów blob i kolejek w witrynie Azure portal, użytkownik musi mieć przypisaną usługi Azure Resource Manager **czytnika** roli, oprócz odpowiednią rolę dla dostępu do obiektów blob i kolejki. Aby uzyskać więcej informacji, zobacz [udzielać dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktualnie używa klucza konta magazynu do dostępu do danych obiektów blob i kolejek. Jeśli klucz nie jest dostępna, autoryzacji usługi Azure AD jest używany do uzyskiwania dostępu do obiektów blob. Autoryzacja w usłudze Azure AD nie jest obecnie obsługiwane dla kolejek. 
>
> - Usługa pliki systemu Azure obsługuje uwierzytelnianie przy użyciu usługi Azure AD za pośrednictwem protokołu SMB dla przyłączonych do domeny tylko maszyn wirtualnych (wersja zapoznawcza). Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, zobacz [uwierzytelniania Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](../articles/storage/files/storage-files-active-directory-overview.md).