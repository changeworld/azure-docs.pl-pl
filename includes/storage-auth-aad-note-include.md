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
ms.openlocfilehash: ac7aa294dee25a6dea4ec2ee614f2b2d34d3a6bc
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49117681"
---
> [!IMPORTANT]
> - Uwierzytelniania usługi Azure AD dla kolejek i obiektów blob w wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie są obecnie dostępne. Jeśli uwierzytelnianie usługi Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach.
>
> - W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.
>
> - Do uwierzytelniania za pomocą usługi Azure AD musi używać protokołu HTTPS podczas wywoływania operacji obiektów blob i kolejek.
>
> - Azure portal obsługuje teraz, przy użyciu poświadczeń usługi Azure AD do odczytu i zapisu danych obiektów blob w ramach wersji zapoznawczej.
> 
> - Witryna Azure portal nie obsługuje obecnie przy użyciu poświadczeń usługi Azure AD do odczytu i zapisu danych kolejki. Dane w kolejce jest dostępna za pośrednictwem klucze konta magazynu.
>
> - [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) aktualnie używa klucza konta magazynu do dostępu do danych obiektów blob i kolejek.


