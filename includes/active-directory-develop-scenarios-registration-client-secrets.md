---
title: Plik dyrektywy include
description: Dołączanie pliku do poufnych informacji scenariusza klienta strony docelowe (demon, aplikacja sieci Web, interfejs API sieci Web)
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
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286206"
---
## <a name="registration-of-secrets-or-certificates"></a>Rejestracja wpisów tajnych lub certyfikatów

Podobnie jak w przypadku każdej poufnej aplikacji klienckiej, należy zarejestrować klucz tajny lub certyfikat. Wpisy tajne aplikacji można zarejestrować za pomocą środowiska interaktywnego w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)lub przy użyciu narzędzi wiersza polecenia (na przykład programu PowerShell).

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Rejestrowanie wpisów tajnych klienta przy użyciu portalu rejestracji aplikacji

Zarządzanie poświadczeniami klienta odbywa się na stronie **certyfikaty &** wpisy tajne aplikacji:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- wpis tajny aplikacji (nazywany także kluczem tajnym klienta) jest generowany przez usługę Azure AD podczas rejestracji poufnej aplikacji klienckiej. Ta generacja ma miejsce w przypadku wybrania **nowego klucza tajnego klienta**. W tym momencie należy skopiować ciąg tajny w schowku do użycia w aplikacji przed wybraniem opcji **Zapisz**. Ten ciąg nie zostanie już przedstawiony.
- certyfikat zostanie przekazany do rejestracji aplikacji przy użyciu przycisku **Przekaż certyfikat** . Usługa Azure AD obsługuje tylko certyfikaty, które są bezpośrednio zarejestrowane w aplikacji, i nie Śledź łańcuchów certyfikatów.

Aby uzyskać szczegółowe informacje [, zobacz Szybki Start: Konfigurowanie aplikacji klienckiej do uzyskiwania dostępu do interfejsów API sieci Web | Dodawanie poświadczeń do aplikacji](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Rejestrowanie wpisów tajnych klienta przy użyciu programu PowerShell

Alternatywnie możesz zarejestrować swoją aplikację w usłudze Azure AD przy użyciu narzędzi wiersza polecenia. Przykład " [Active-Directory-dotnetcore-demo-v2"](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) pokazuje, jak zarejestrować klucz tajny aplikacji lub certyfikat z aplikacją usługi Azure AD:

- Aby uzyskać szczegółowe informacje na temat rejestrowania wpisu tajnego aplikacji, zobacz [AppCreationScripts/Configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Aby uzyskać szczegółowe informacje na temat rejestrowania certyfikatu w aplikacji, zobacz [AppCreationScripts-withCert/Configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
