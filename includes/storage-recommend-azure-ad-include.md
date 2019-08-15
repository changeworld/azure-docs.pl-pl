---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 825a9f5668cc80f1306d74623db2ea54614ba4a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985375"
---
> [!TIP]
> Usługa Azure Storage obsługuje autoryzowanie żądań do magazynu obiektów blob i Queue przy użyciu Azure Active Directory (Azure AD). Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z autoryzacją klucza współużytkowanego. W przypadku usługi Azure AD nie ma potrzeby przechowywania klucza dostępu do konta z kodem i ryzyka potencjalnej luki w zabezpieczeniach.
>
> Ponadto usługa Azure Storage obsługuje sygnaturę dostępu współdzielonego (SAS) delegowania użytkowników dla usługi BLOB Storage. Sygnatura dostępu współdzielonego użytkownika jest podpisywana przy użyciu poświadczeń usługi Azure AD. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi BLOB Storage, Użyj poświadczeń usługi Azure AD, aby utworzyć SYGNATURę czasową delegowania użytkowników dla zabezpieczeń nadrzędnych.
>
> Jeśli to możliwe, firma Microsoft zaleca korzystanie z usługi Azure AD z aplikacjami usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
