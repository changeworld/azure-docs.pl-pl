---
title: Znajdź identyfikatory obiektów tożsamości do uwierzytelniania, interfejsu API usługi Azure dla FHIR
description: W tym artykule wyjaśniono, jak znaleźć obiektu tożsamości identyfikatory wymagane do skonfigurowania uwierzytelniania dla interfejsu API platformy Azure dla FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824182"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Znajdź identyfikatory obiektów tożsamości dla konfiguracji uwierzytelniania

W tym artykule dowiesz się, jak znaleźć identyfikatory obiektów potrzebne do skonfigurowania listy identyfikatory obiektów tożsamości dozwolonych dla interfejsu API platformy Azure dla FHIR tożsamości.

W pełni zarządzany interfejs API platformy Azure dla FHIR&reg; usługa jest skonfigurowana do Zezwalaj na dostęp tylko do wstępnie zdefiniowanej listy tożsamości identyfikatory obiektów. Gdy aplikacja lub użytkownik próbuje uzyskać dostęp interfejsu API FHIR, przedstawia token elementu nośnego. Ten token elementu nośnego będzie miał pewne oświadczenia (pola). Aby udzielić dostępu do interfejsu API FHIR, token musi zawierać odpowiednie wystawcy (`iss`), odbiorców (`aud`), a Identyfikatora obiektu (`oid`) z listy identyfikatorów obiektów dozwolone. Identyfikator obiektu tożsamości jest identyfikator obiektu użytkownika lub jednostki w usłudze Azure Active Directory usługi.

## <a name="configure-list-of-allowed-object-ids"></a>Skonfiguruj listę identyfikatorów obiektów dozwolone

Podczas tworzenia nowego interfejsu API platformy Azure dla wystąpienia FHIR można skonfigurować listę dozwolonych obiekt identyfikatorów:

![Skonfiguruj identyfikatory obiektów dozwolone](media/quickstart-paas-portal/configure-allowed-oids.png)

Te identyfikatory obiektów, może to być identyfikatory dla określonych użytkowników lub jednostek usługi w usłudze Azure Active Directory.

## <a name="find-user-object-id-using-powershell"></a>Znajdź identyfikator obiektu użytkownika przy użyciu programu PowerShell

Jeśli masz użytkownikowi o nazwie `myuser@consoso.com`, możesz znaleźć, jeśli użytkownicy `ObjectId` przy użyciu następującego polecenia programu PowerShell:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

lub można użyć wiersza polecenia platformy Azure:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>Znajdź identyfikator obiektu jednostki usługi przy użyciu programu PowerShell

Załóżmy, że zarejestrowano [aplikacja kliencka usługi](register-service-azure-ad-client-app.md) i chcesz zezwolić na tym kliencie usługi, aby uzyskiwać dostęp do interfejsu API platformy Azure dla FHIR, można znaleźć Identyfikatora obiektu dla klienta jednostki usługi przy użyciu następującego polecenia programu PowerShell:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

gdzie `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` jest identyfikatorem usługi klienta aplikacji. Alternatywnie, można użyć `DisplayName` klienta usługi:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Jeśli używasz interfejsu wiersza polecenia platformy Azure, możesz użyć:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wyjaśniono sposób znajdowania obiektu tożsamości identyfikatory wymagane do skonfigurowania tożsamości, które mogą uzyskać dostęp do interfejsu API platformy Azure dla wystąpienia FHIR. Następnie można wdrożyć w pełni zarządzana interfejsu API usługi Azure dla FHIR:
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-paas-portal-quickstart.md)