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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565093"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (wersja zapoznawcza) i [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (ga). Ten artykuł koncentruje się na tym, jak Azure Files mogą korzystać z usług domenowych lokalnie lub na platformie Azure w celu obsługi dostępu opartego na tożsamościach do Azure Files za pośrednictwem protokołu SMB. Pozwala to łatwo zastąpić istniejące serwery plików Azure Files i nadal korzystać z istniejącej usługi katalogowej, zapewniając bezproblemowe dostęp użytkowników do udziałów. 

Azure Files wymusza autoryzację dostępu użytkownika zarówno do udziału, jak i do poziomu katalogu/pliku. Przypisanie uprawnień na poziomie udziału można przypisać do użytkowników lub grup usługi Azure AD zarządzanych przy użyciu typowego modelu [kontroli dostępu opartego na rolach (RBAC)](../articles/role-based-access-control/overview.md) . W przypadku kontroli RBAC poświadczenia używane do uzyskiwania dostępu do plików powinny być dostępne lub synchronizowane z usługą Azure AD. Można przypisywać wbudowane role RBAC, takie jak dane plików magazynu SMB, do użytkowników lub grup w usłudze Azure AD w celu przyznania dostępu do odczytu do udziału plików platformy Azure.

Na poziomie katalogu/pliku Azure Files obsługuje zachowanie, dziedziczenie i wymuszanie [list DACL systemu Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) podobnie jak w przypadku wszystkich serwerów plików systemu Windows. Jeśli skopiujesz dane za pośrednictwem protokołu SMB z udziału plików do Azure Files lub na odwrót, możesz wybrać opcję zachowania list DACL systemu Windows. Niezależnie od tego, czy zamierzasz wymusić autoryzację, czy nie, możesz wykorzystać Azure Files do tworzenia kopii zapasowych list ACL wraz z danymi. 
