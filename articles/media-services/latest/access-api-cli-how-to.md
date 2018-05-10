---
title: Dostęp do interfejsu API usługi Azure Media - CLI 2.0 | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego Porada można uzyskać dostępu do interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a4a7c59e93b860245d67695de90fbae2becac3e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Dostęp do usługi Azure Media Services interfejsu API z interfejsu wiersza polecenia 2.0
 
Należy użyć uwierzytelniania podmiotu zabezpieczeń usługi Azure AD do nawiązania połączenia interfejsu API Azure Media Services. Aplikacja musi żądać tokenu usługi Azure AD, który ma następujące parametry:

* Punktu końcowego dzierżawcy usługi Azure AD
* Identyfikator URI zasobu usługi Media Services
* Identyfikator URI dla usługi REST Media Services
* Wartości aplikacji w usłudze Azure AD: identyfikator klienta i klucz tajny klienta

W tym artykule przedstawiono sposób użycia interfejsu wiersza polecenia 2.0 do tworzenia aplikacji usługi Azure AD i usługi głównej i wartości, które są niezbędne do dostępu do zasobów usługi Azure Media Services.

## <a name="prerequisites"></a>Wymagania wstępne 

Utwórz nowe konto usługi Azure Media Services, zgodnie z opisem w [tego przewodnika Szybki Start](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do [portalu Azure](http://portal.azure.com) , a następnie uruchom **CloudShell** można wykonać polecenia interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumień pliku](stream-files-dotnet-quickstart.md)
