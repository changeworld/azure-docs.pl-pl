---
title: Plik dyrektywy include
description: zawierać plik dla poufnych stron docelowych scenariusza klienta (demon, aplikacja internetowa, internetowy interfejs API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773385"
---
## <a name="register-secrets-or-certificates"></a>Rejestrowanie wpisów tajnych lub certyfikatów

Jeśli chodzi o każdą poufną aplikację kliencką, musisz zarejestrować klucz tajny lub certyfikat. Wpisy tajne aplikacji można zarejestrować za pośrednictwem interaktywnego środowiska w [witrynie Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) lub przy użyciu narzędzi wiersza polecenia (takich jak program PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Rejestrowanie wpisów tajnych klienta przy użyciu portalu rejestracji aplikacji

Zarządzanie poświadczeniami klienta odbywa się na stronie **Certyfikaty & wpisy tajne** dla aplikacji:

![Certyfikaty & strony wpisów tajnych](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Klucz tajny aplikacji (również o nazwie klucz tajny klienta) jest generowany przez usługę Azure AD podczas rejestracji poufnej aplikacji klienckiej. To generowanie dzieje się po **wybraniu nowego klucza tajnego klienta**. W tym momencie należy skopiować tajny ciąg do schowka do użytku w aplikacji, zanim wybierzesz **Zapisz**. Ten ciąg nie będzie już prezentowany.
- Podczas rejestracji aplikacji można użyć przycisku **Przekaż certyfikat,** aby przekazać certyfikat. Usługa Azure AD obsługuje tylko certyfikaty, które są bezpośrednio zarejestrowane w aplikacji i nie są zgodne z łańcuchami certyfikatów.

Aby uzyskać szczegółowe informacje, zobacz [Szybki start: Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowych interfejsów API | Dodawanie poświadczeń do aplikacji](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Rejestrowanie wpisów tajnych klienta przy użyciu programu PowerShell

Alternatywnie można zarejestrować aplikację za pomocą usługi Azure AD przy użyciu narzędzi wiersza polecenia. Przykład [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) pokazuje, jak zarejestrować klucz tajny lub certyfikat aplikacji za pomocą aplikacji usługi Azure AD:

- Aby uzyskać szczegółowe informacje na temat rejestrowania klucza tajnego aplikacji, zobacz [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Aby uzyskać szczegółowe informacje na temat rejestrowania certyfikatu w aplikacji, zobacz [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
