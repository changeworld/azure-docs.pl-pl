---
title: Zabezpieczanie usług zaplecza za pomocą klienta uwierzytelniania certyfikatów — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć usług zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141572"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Zabezpieczanie usług zaplecza za pomocą klienta uwierzytelniania certyfikatów w usłudze Azure API Management

Usługa API Management umożliwia bezpieczny dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. Ten przewodnik pokazuje, jak zarządzać certyfikatami w wystąpieniu usługi Azure API Management w witrynie Azure portal. Wyjaśniono również sposób konfigurowania interfejsu API, aby używać certyfikatu w celu uzyskania dostępu do usługi zaplecza.

Aby uzyskać informacje o zarządzaniu certyfikatami przy użyciu interfejsu API REST zarządzania interfejsu API, zobacz <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">jednostki certyfikat interfejsu API REST zarządzania interfejsu API platformy Azure</a>.

## <a name="prerequisites"> </a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten przewodnik pokazuje, jak skonfigurować Twojego wystąpienia usługi API Management, aby użyć uwierzytelniania certyfikatu klienta w celu uzyskania dostępu do usługi zaplecza dla interfejsu API. Przed wykonaniem kroków opisanych w tym artykule, należy skonfigurować do uwierzytelniania certyfikatu klienta usługi zaplecza ([Aby skonfigurować uwierzytelnianie certyfikatu w usłudze Azure WebSites odnoszą się do tego artykułu] [ to configure certificate authentication in Azure WebSites refer to this article]). Musisz mieć dostęp do certyfikatu i hasła do przekazywania go do usługi API Management.

## <a name="step1"> </a>Przekaż certyfikat klienta

![Dodaj certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Wykonaj poniższe kroki, aby przekazać nowy certyfikat klienta. Jeśli nie utworzono jeszcze wystąpienie usługi API Management, zapoznaj się z samouczkiem [Tworzenie wystąpienia usługi API Management][Create an API Management service instance].

1. Przejdź do wystąpienia usługi Azure API Management w witrynie Azure portal.
2. Wybierz **certyfikaty klienta** z menu.
3. Kliknij przycisk **+ Dodaj**.  
    ![Dodaj certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Przeglądaj w poszukiwaniu certyfikatu, podaj jego identyfikator i hasło.  
5. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Certyfikat musi znajdować się w **PFX** formatu. Certyfikaty z podpisem własnym są dozwolone.

Po przekazaniu certyfikatu pokazuje w **certyfikaty klienta**.  Jeśli masz wiele certyfikatów, zanotuj Odcisk palca żądanego certyfikatu w celu [Konfigurowanie interfejsu API do używania certyfikatu klienta dla bramy uwierzytelniania][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Aby wyłączyć sprawdzanie poprawności łańcucha certyfikatu podczas korzystania na przykład certyfikat z podpisem własnym, wykonaj czynności opisane w tym artykule [elementu](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij przycisk menu kontekstowego **...**  i wybierz **Usuń** obok certyfikatu.

![Usuń certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Jeśli certyfikat jest używany przez interfejs API, a następnie zostanie wyświetlony ekran ostrzegawczy. Można usunąć certyfikatu, należy najpierw usunąć certyfikat z dowolnych interfejsów API, które są skonfigurowane do używania go.

![Usuń błąd certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Konfigurowanie interfejsu API do bramy uwierzytelniania certyfikatu klienta

1. Kliknij przycisk **interfejsów API** z **usługi API Management** menu po lewej stronie i przejdź do interfejsu API.  
    ![Włącz certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. W **projektowania** kartę, kliknij ikonę ołówka **zaplecza** sekcji. 
3. Zmiana **poświadczeń bramy** do **certyfikat klienta** i wybierz certyfikat z listy rozwijanej.  
    ![Włącz certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kliknij pozycję **Zapisz**. 

> [!WARNING]
> Ta zmiana zacznie obowiązywać natychmiast, a wywołania operacji tego interfejsu API będzie używany certyfikat do uwierzytelniania na serwerze zaplecza.


> [!TIP]
> Jeśli certyfikat jest określony dla bramy uwierzytelniania dla usługi zaplecza interfejsu API, staje się częścią zasad dla tego interfejsu API i mogą być wyświetlane w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

W przypadku korzystania z certyfikatów z podpisem własnym należy wyłączyć sprawdzanie poprawności łańcucha certyfikatów w kolejności dla usługi API Management do komunikowania się z system wewnętrznej bazy danych. W przeciwnym razie zwróci kod błędu 500. Aby to skonfigurować, można użyć [ `New-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (dla nowego zaplecza) lub [ `Set-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (w przypadku istniejącego zaplecza) polecenia cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
