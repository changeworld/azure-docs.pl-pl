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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565093"
---
[Usługa Azure Files](../articles/storage/files/storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem bloku komunikatów serwera (SMB) za pośrednictwem [usługi Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (wersja zapoznawcza) i [usług domenowych Usługi domenowe Usługi Active Directory azure (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). W tym artykule skupiono się na tym, jak usługa Azure Files może korzystać z usług domeny lokalnie lub na platformie Azure w celu obsługi dostępu opartego na tożsamości do usługi Azure Files za pomocą SMB. Dzięki temu można łatwo zastąpić istniejące serwery plików za pomocą usługi Azure Files i nadal korzystać z istniejącej usługi katalogowej, zachowując bezproblemowy dostęp użytkowników do udziałów. 

Usługa Azure Files wymusza autoryzację dostępu użytkownika do udziału i poziomu katalogu/pliku. Przypisanie uprawnień na poziomie udziału można przypisać do użytkowników usługi Azure AD lub grup zarządzanych za pomocą typowego modelu [kontroli dostępu opartego na rolach (RBAC).](../articles/role-based-access-control/overview.md) W przypadku funkcji RBAC poświadczenia używane do dostępu do plików powinny być dostępne lub synchronizowane z usługą Azure AD. Wbudowane role RBAC, takie jak czytnik udostępniania SMB danych magazynu, można przypisać użytkownikom lub grupom w usłudze Azure AD, aby udzielić dostępu do odczytu do udziału plików platformy Azure.

Na poziomie katalogu/pliku usługa Azure Files obsługuje zachowywanie, dziedziczenie i wymuszanie [dacl dacl systemu Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) podobnie jak wszystkie serwery plików systemu Windows. Jeśli kopiujesz dane za pomocą protokołu SMB z udziału plików do usługi Azure Files lub odwrotnie, możesz zachować biblioteki DACL systemu Windows. Niezależnie od tego, czy planujesz wymusić autoryzację, czy nie, możesz korzystać z usługi Azure Files do tworzenia kopii zapasowych list ACL wraz z danymi. 
