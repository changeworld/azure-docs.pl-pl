---
title: Authentication
description: Wyjaśniono, jak działa uwierzytelnianie
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681638"
---
# <a name="configure-authentication"></a>Konfigurowanie uwierzytelniania

Renderowanie zdalne platformy Azure używa tego samego mechanizmu uwierzytelniania co [zakotwiczenia przestrzenne platformy Azure (ASA).](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) Klienci muszą ustawić *AccountKey*, *AuthenticationToken*lub *AccessToken,* aby pomyślnie wywołać interfejsy API REST. *AccountKey* można uzyskać w zakładce "Klucze" dla konta zdalnego renderowania w witrynie Azure portal. *AuthenticationToken* to token usługi Azure AD, który można uzyskać za pomocą [biblioteki ADAL.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) *AccessToken* to token MR, który można uzyskać z usługi Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Uwierzytelnianie dla wdrożonych aplikacji

 Używanie kluczy konta jest zalecane do szybkiego wejścia na pokład, ale tylko podczas opracowywania/tworzenia prototypów. Zdecydowanie zaleca się, aby nie wysyłać aplikacji do środowiska produkcyjnego przy użyciu klucza konta osadzonego w nim i zamiast tego używać metod uwierzytelniania usługi Azure AD opartych na użytkownikach lub usługach.

 Uwierzytelnianie usługi Azure AD jest opisane w sekcji [uwierzytelniania użytkowników usługi Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) usługi Azure Spatial [Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby ułatwić kontrolowanie poziomu dostępu przyznanego aplikacjom, usługom lub użytkownikom usługi Azure AD, utworzono następujące role, aby można je było przypisać zgodnie z potrzebami do kont zdalnego renderowania platformy Azure:

* **Administrator zdalnego renderowania:** zapewnia użytkownikowi możliwości konwersji, zarządzania sesją, renderowaniem i diagnostyką dla zdalnego renderowania platformy Azure.
* **Zdalny klient renderowania:** zapewnia użytkownikowi zarządzanie funkcjami sesji, renderowania i diagnostyki dla renderowania zdalnego platformy Azure.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta](create-an-account.md)
* [Używanie interfejsów API zaplecza azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
