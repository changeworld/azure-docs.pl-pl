---
title: Bezpieczne usługi zaplecza przy użyciu klienta uwierzytelniania certyfikatów - Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć usług zaplecza w usłudze Azure API Management przy użyciu wstępnego uwierzytelniania certyfikatu klienta.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 844a7ea1c2dd8f7dbb4984fc148575529ac154db
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292862"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpieczyć za pomocą klienta usług zaplecza certyfikatów uwierzytelniania w usłudze Azure API Management

Zarządzanie interfejsami API umożliwia bezpieczny dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. W tym przewodniku przedstawiono sposób zarządzania certyfikatami w wystąpieniu usługi Azure API Management w portalu Azure. Ponadto wyjaśniono, jak skonfigurować interfejs API do używania certyfikatu do dostępu do usługi zaplecza.

Aby uzyskać informacje o zarządzaniu certyfikatami przy użyciu interfejsu API REST zarządzania interfejsu API, zobacz <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">jednostki certyfikat interfejsu API REST zarządzania interfejsu API platformy Azure</a>.

## <a name="prerequisites"> </a>Wymagania wstępne

W tym przewodniku przedstawiono sposób skonfigurować wystąpienie usługi Zarządzanie interfejsami API do używania uwierzytelniania certyfikatów klienta do uzyskania dostępu do usługi zaplecza interfejsu API. Przed wykonaniem kroków w tym artykule, powinien mieć skonfigurowany na potrzeby uwierzytelniania certyfikatu klienta usługi zaplecza ([do konfigurowania certyfikatów uwierzytelniania w witrynach sieci Web Azure odwoływać się do tego artykułu] [ to configure certificate authentication in Azure WebSites refer to this article]). Potrzebujesz dostępu do certyfikatu i hasła dla przekazać go do usługi API Management.

## <a name="step1"> </a>Przekaż certyfikat klienta

![Dodaj certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Wykonaj poniższe kroki, aby przekazać nowy certyfikat klienta. Jeśli nie utworzono jeszcze wystąpienia usługi API Management, zobacz samouczek [Utwórz wystąpienie usługi Zarządzanie interfejsami API][Create an API Management service instance].

1. Przejdź do wystąpienia usługi Azure API Management w portalu Azure.
2. Wybierz **certyfikaty klienta** z menu.
3. Kliknij przycisk **+ Dodaj** przycisku.  
    ![Dodaj certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Przeglądaj w poszukiwaniu certyfikatu, podaj swoją nazwę i hasło.  
5. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Certyfikat musi być w **PFX** format. Certyfikaty z podpisem własnym są dozwolone.

Po przekazaniu certyfikat będzie wyświetlana w **certyfikaty klienta**.  Jeśli masz wiele certyfikatów, zanotuj Odcisk palca żądanego certyfikatu w celu [skonfigurować interfejs API do używania certyfikatu klienta uwierzytelniania bramy][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Aby wyłączyć weryfikacji łańcucha certyfikatu przy użyciu, na przykład certyfikatu z podpisem własnym, wykonaj czynności opisane w tym artykule [elementu](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Usuń certyfikat klienta

Aby usunąć certyfikat, kliknij menu kontekstowe **...**  i wybierz **usunąć** obok certyfikatu.

![Usuń certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Jeśli certyfikat jest używany przez interfejs API, a następnie zostanie wyświetlony ekran ostrzeżenie. Aby usunąć certyfikat, należy najpierw usunąć certyfikat z żadnych interfejsów API, które są skonfigurowane do używania go.

![Usuń błąd certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Skonfiguruj interfejs API do używania certyfikatu klienta uwierzytelniania bramy

1. Kliknij przycisk **interfejsów API** z **zarządzanie interfejsami API** menu po lewej stronie i przejdź do interfejsu API.  
    ![Włącz certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. W **projekt** kliknij ikonę ołówka **zaplecza** sekcji. 
3. Zmień **poświadczeń bramy** do **certyfikatu klienta** i wybierz certyfikat z listy rozwijanej.  
    ![Włącz certyfikaty klienta](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Kliknij pozycję **Zapisz**. 

> [!WARNING]
> Ta zmiana zaczyna się od razu i wywołania operacji tego interfejsu API będzie używany certyfikat do uwierzytelniania na serwerze zaplecza.


> [!TIP]
> Jeśli nie określono certyfikatu bramy uwierzytelniania usługi zaplecza interfejsu API, staje się częścią zasad dla tego interfejsu API i mogą być wyświetlane w edytorze zasad.

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Jeśli używane są certyfikaty z podpisem własnym, należy wyłączyć weryfikacji łańcucha certyfikatu w kolejności dla interfejsu API zarządzania do komunikacji z systemem zaplecza. W przeciwnym razie zwróci kod komunikatu o błędzie 500. Aby to skonfigurować, można użyć [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (dla nowego zaplecza) lub [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (dla istniejących zaplecza) poleceń cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
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

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
