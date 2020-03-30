---
title: Bezpieczne usługi zaplecza przy użyciu uwierzytelniania certyfikatów klienta
titleSuffix: Azure API Management
description: Dowiedz się, jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatów klienta w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347109"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpieczać usługi zaplecza przy użyciu uwierzytelniania za pomocą certyfikatów klienta w usłudze Azure API Management

Zarządzanie interfejsem API umożliwia bezpieczny dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. W tym przewodniku pokazano, jak zarządzać certyfikatami w wystąpieniu usługi Azure API Management w witrynie Azure portal. Wyjaśniono również, jak skonfigurować interfejs API do używania certyfikatu w celu uzyskania dostępu do usługi zaplecza.

Aby uzyskać informacje dotyczące zarządzania certyfikatami przy użyciu interfejsu API REST zarządzania interfejsem API interfejsu API, zobacz <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Jednostka certyfikatu interfejsu API REST usługi Azure API Management</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku pokazano, jak skonfigurować wystąpienie usługi zarządzania interfejsem API do używania uwierzytelniania certyfikatu klienta w celu uzyskania dostępu do usługi zaplecza dla interfejsu API. Przed podjęciem kroków w tym artykule powinna być skonfigurowana usługa zaplecza do uwierzytelniania certyfikatów klienta[(aby skonfigurować uwierzytelnianie certyfikatów w usłudze Azure App Service, zapoznaj się z tym artykułem).][to configure certificate authentication in Azure WebSites refer to this article] Do przesłania go do usługi API Management potrzebny jest dostęp do certyfikatu i hasła.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Przekazywanie certyfikatu

> [!NOTE]
> Zamiast przekazanego certyfikatu można użyć certyfikatu przechowywanego w usłudze [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) jak pokazano w tym [przykładzie.](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)

![Dodawanie certyfikatów klientów](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Wykonaj poniższe czynności, aby przekazać nowy certyfikat klienta. Jeśli nie utworzono jeszcze wystąpienia usługi zarządzanie interfejsami API, zobacz samouczek [Tworzenie wystąpienia usługi zarządzania interfejsami API][Create an API Management service instance].

1. Przejdź do wystąpienia usługi Azure API Management w witrynie Azure portal.
2. Z menu **wybierz pozycję Certyfikaty.**
3. Kliknij przycisk **+ Dodaj**.
    ![Dodawanie certyfikatów klientów](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Wyszukaj certyfikat, podaj jego identyfikator i hasło.
5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Certyfikat musi być w formacie **.pfx.** Certyfikaty z podpisem własnym są dozwolone.

Po przesłaniu certyfikatu jest on wyświetlany w pliku **Certyfikaty**.  Jeśli masz wiele certyfikatów, zanotuj odcisk palca żądanego certyfikatu, aby [skonfigurować interfejs API do używania certyfikatu klienta do uwierzytelniania bramy.][Configure an API to use a client certificate for gateway authentication]

> [!NOTE]
> Aby wyłączyć sprawdzanie poprawności łańcucha certyfikatów podczas korzystania na przykład z certyfikatem z podpisem własnym, wykonaj kroki opisane w tym [elemencie](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)często zadawanych pytań .

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Usuwanie certyfikatu klienta

Aby usunąć certyfikat, kliknij menu kontekstowe **...** i wybierz polecenie **Usuń** obok certyfikatu.

![Usuwanie certyfikatów klientów](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Jeśli certyfikat jest używany przez interfejs API, zostanie wyświetlony ekran ostrzegawczy. Aby usunąć certyfikat, należy najpierw usunąć certyfikat z wszystkich interfejsów API, które są skonfigurowane do jego używania.

![Błąd usuwania certyfikatów klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurowanie interfejsu API do używania certyfikatu klienta do uwierzytelniania bramy

1. Kliknij **interfejsy API** z menu **Zarządzanie interfejsami API** po lewej stronie i przejdź do interfejsu API.
    ![Włączanie certyfikatów klientów](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na karcie **Projektowanie** kliknij ikonę ołówka w sekcji **Wewnętrznej bazy** danych.
3. Zmień **poświadczenia bramy** na **certyfikat klienta** i wybierz certyfikat z listy rozwijanej.
    ![Włączanie certyfikatów klientów](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kliknij przycisk **Zapisz**.

> [!WARNING]
> Ta zmiana wchodzi w życie natychmiast, a wywołania operacji tego interfejsu API będą używać certyfikatu do uwierzytelniania na serwerze zaplecza.


> [!TIP]
> Gdy certyfikat jest określony do uwierzytelniania bramy dla usługi zaplecza interfejsu API, staje się częścią zasad dla tego interfejsu API i można go wyświetlić w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Jeśli używasz certyfikatów z podpisem własnym, należy wyłączyć sprawdzanie poprawności łańcucha certyfikatów, aby usługa API Management komunikować się z systemem wewnętrznej bazy danych. W przeciwnym razie zwróci kod błędu 500. Aby to skonfigurować, można [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) użyć (dla nowych [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) zaplecza) lub (dla istniejących zaplecza) `-SkipCertificateChainValidation` poleceń `True`cmdlet programu PowerShell i ustawić parametr na .

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
