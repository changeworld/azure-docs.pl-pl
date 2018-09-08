---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095595"
---
> [!IMPORTANT]
> - Uwierzytelniania usługi Azure AD dla kolejek i obiektów blob w wersji zapoznawczej jest przeznaczony tylko do użytku nieprodukcyjnych. Produkcyjne usługi poziomu usług (SLA) nie są obecnie dostępne. Jeśli uwierzytelnianie usługi Azure AD nie jest jeszcze obsługiwana dla danego scenariusza, należy nadal używać klucza wspólnego autoryzacji lub tokenów SAS w swoich aplikacjach.
>
> - W trakcie okresu zapoznawczego przypisania ról RBAC może potrwać do pięciu minut na propagację.
>
> - Do uwierzytelniania za pomocą usługi Azure AD musi używać protokołu HTTPS podczas wywoływania operacji obiektów blob i kolejek.
>
> - W wersji zapoznawczej witryny Azure portal nie używa poświadczeń usługi Azure AD do odczytu i zapisu danych obiektów blob i kolejek. Portal nadal zależą od klucz dostępu konta. Aby wyświetlić lub zaktualizować dane obiektów blob lub kolejki w portalu, użytkownik musi mieć przypisaną rolę RBAC, który obejmuje [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), która udziela uprawnień do wywoływania [konta magazynu — Lista kluczy](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). Role współautora i czytelnika dla obiektów blob i kolejek nie obejmują **listkeys** akcji w ramach wersji zapoznawczej wydania i dlatego nie są oferowane dostęp do danych za pośrednictwem witryny Azure portal. Aby poznać oparta na tożsamości dostępu do danych obiektów blob i kolejek w wersji zapoznawczej, należy użyć programu PowerShell lub wiersza polecenia platformy Azure.
