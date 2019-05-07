---
title: Plik dyrektywy include
description: Dołącz plik dla scenariusza poufne klienta stronach (demona, aplikacji sieci Web, interfejs API sieci Web)
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074653"
---
## <a name="registration-of-secrets-or-certificates"></a>Rejestracja klucze tajne i certyfikaty

Np. dla każdej aplikacji poufne klienta należy zarejestrować hasło lub certyfikat. Wpisy tajne aplikacji można zarejestrować za pomocą środowiska interaktywnego w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), lub za pomocą narzędzia wiersza polecenia (na przykład programu PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Rejestrowanie klienta wpisami tajnymi za pomocą portalu rejestracji aplikacji

Zarządzanie poświadczeniami klienta odbywa się w **certyfikaty i klucze tajne** stronę dla aplikacji:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- klucz tajny aplikacji (również klucz tajny klienta o nazwie) jest generowany przez usługę Azure AD podczas rejestracji aplikacji zawierających poufne dane klienta. Ta generacja się dzieje po wybraniu **nowy wpis tajny klienta**. W tym momencie należy skopiować klucza tajnego ciągu Schowka w celu użycia w aplikacji, przed wybraniem **Zapisz**. Ten ciąg nie będzie już wyświetlone.
- certyfikat jest przekazywany w rejestracji aplikacji przy użyciu **Przekaż certyfikat** przycisku

Aby uzyskać więcej informacji, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej dostęp do interfejsów API sieci web | Dodawanie poświadczeń do aplikacji](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Rejestrowanie klienta wpisami tajnymi za pomocą programu PowerShell

Alternatywnie można zarejestrować aplikację w usłudze Azure AD przy użyciu narzędzia wiersza polecenia. [Active-directory-dotnetcore — demon — w wersji 2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) przykład pokazuje, jak zarejestrować wpis tajny aplikacji lub certyfikatu z aplikacją usługi Azure AD:

- Aby uzyskać więcej informacji o sposobie rejestrowania wpis tajny aplikacji, zobacz [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Aby uzyskać więcej informacji na temat sposobu rejestrowania certyfikatu z aplikacją, zobacz [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)