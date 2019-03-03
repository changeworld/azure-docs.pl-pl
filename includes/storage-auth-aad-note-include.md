---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251898"
---
> [!NOTE]
> - Uwierzytelniania usługi Azure AD dla kolejek i obiektów blob w wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie są obecnie dostępne. Jeśli uwierzytelnianie usługi Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach.
>
> - W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.
>
> - Aby autoryzować obiektów blob i kolejek operacji przy użyciu tokenu OAuth, musi używać protokołu HTTPS.
>
> - Azure portal obsługuje teraz, przy użyciu poświadczeń usługi Azure AD na odczytywanie i zapisywanie obiektów blob i kolejki danych, jako część wersji zapoznawczej wersji. Aby dostęp do danych obiektów blob i kolejek w witrynie Azure portal, użytkownik musi mieć przypisaną rolę RBAC czytnika Menedżera zasobów platformy Azure oprócz roli odpowiedniej wersji zapoznawczej uzyskać dostęp do obiektów blob i kolejki. Aby uzyskać więcej informacji, zobacz [udzielać dostępu do kontenerów platformy Azure i kolejek o ROLACH w witrynie Azure portal (wersja zapoznawcza)](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktualnie używa klucza konta magazynu do dostępu do danych obiektów blob i kolejek. Dostęp OAuth jest obsługiwany w przypadku obiektów blob.
>
> - Usługa pliki systemu Azure obsługuje uwierzytelnianie przy użyciu usługi Azure AD za pośrednictwem protokołu SMB dla przyłączonych do domeny tylko maszyn wirtualnych (wersja zapoznawcza). Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, zobacz [uwierzytelniania Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](../articles/storage/files/storage-files-active-directory-overview.md).