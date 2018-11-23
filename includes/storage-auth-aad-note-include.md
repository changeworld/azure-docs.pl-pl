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
ms.openlocfilehash: fbc5ca4d433be65d43ae535703cc7f765dda0a1e
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292700"
---
> [!NOTE]
> - Uwierzytelniania usługi Azure AD dla kolejek i obiektów blob w wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie są obecnie dostępne. Jeśli uwierzytelnianie usługi Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach.
>
> - W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.
>
> - Aby autoryzować obiektów blob i kolejek operacji przy użyciu tokenu OAuth, musi używać protokołu HTTPS.
>
> - Azure portal obsługuje teraz, przy użyciu poświadczeń usługi Azure AD na odczytywanie i zapisywanie obiektów blob i kolejki danych, jako część wersji zapoznawczej wersji.
> 
> - [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktualnie używa klucza konta magazynu do dostępu do danych obiektów blob i kolejek.
>
> - Usługa pliki systemu Azure obsługuje uwierzytelnianie przy użyciu usługi Azure AD za pośrednictwem protokołu SMB dla przyłączonych do domeny tylko maszyn wirtualnych (wersja zapoznawcza). Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, zobacz [uwierzytelniania Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](../articles/storage/files/storage-files-active-directory-overview.md).



