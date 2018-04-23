---
title: Bezpieczne usługi zaplecza przy użyciu klienta uwierzytelniania certyfikatów - Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć usług zaplecza w usłudze Azure API Management przy użyciu wstępnego uwierzytelniania certyfikatu klienta.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: afa5c6a07432456d703020c0b8c5c3606478b8fe
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpieczyć za pomocą klienta usług zaplecza certyfikatów uwierzytelniania w usłudze Azure API Management
Zarządzanie interfejsami API umożliwia bezpieczny dostęp do usługi zaplecza interfejsu API przy użyciu certyfikatów klienta. Ten przewodnik zawiera sposobu zarządzania certyfikatami w portalu wydawcy interfejsu API i Konfigurowanie interfejsu API do używania certyfikatu dostęp do jej usługi zaplecza.

Aby uzyskać informacje o zarządzaniu certyfikatami przy użyciu interfejsu API REST zarządzania interfejsu API, zobacz [jednostki certyfikat interfejsu API REST zarządzania interfejsu API platformy Azure][Azure API Management REST API Certificate entity].

## <a name="prerequisites"> </a>Wymagania wstępne
W tym przewodniku przedstawiono sposób skonfigurować wystąpienie usługi Zarządzanie interfejsami API do używania uwierzytelniania certyfikatów klienta do uzyskania dostępu do usługi zaplecza interfejsu API. Przed wykonaniem kroków w tym temacie, powinien mieć skonfigurowany na potrzeby uwierzytelniania certyfikatu klienta usługi zaplecza ([do konfigurowania certyfikatów uwierzytelniania w witrynach sieci Web Azure odwoływać się do tego artykułu] [ to configure certificate authentication in Azure WebSites refer to this article]), i mieć dostęp do certyfikat i hasło dla certyfikatu na potrzeby przekazywania w portalu zarządzania interfejsu API wydawcy.

## <a name="step1"> </a>Przekaż certyfikat klienta
Na początku kliknij opcję **Portal wydawcy** w klasycznej witrynie Azure Portal dla usługi API Management. Spowoduje to przejście do portalu wydawcy usługi API Management.

![Portal wydawcy interfejsu API][api-management-management-console]

> Jeśli jeszcze nie utworzono wystąpienie usługi API Management, zobacz [Utwórz wystąpienie usługi Zarządzanie interfejsami API][Create an API Management service instance].
> 
> 

Kliknij przycisk **zabezpieczeń** z **zarządzanie interfejsami API** menu po lewej stronie, a następnie kliknij przycisk **certyfikaty klienta**.

![Certyfikaty klienta][api-management-security-client-certificates]

Aby przekazać nowy certyfikat, kliknij przycisk **przekazywania certyfikatu**.

![Przekazywanie certyfikatu][api-management-upload-certificate]

Przejdź do swojego certyfikatu, a następnie wprowadź hasło dla certyfikatu.

> Certyfikat musi być w **PFX** format. Certyfikaty z podpisem własnym są dozwolone.
> 
> 

![Przekazywanie certyfikatu][api-management-upload-certificate-form]

Kliknij przycisk **przekazać** można przekazać certyfikatu.

> Hasło certyfikatu jest weryfikowana w tej chwili. Jeśli jest nieprawidłowe, zostanie wyświetlony komunikat o błędzie.
> 
> 

![Przekazano certyfikat][api-management-certificate-uploaded]

Po przesłaniu certyfikatu, pojawi się na **certyfikaty klienta** kartę. Jeśli istnieje wiele certyfikatów, należy Uwaga podmiot lub cztery ostatnie znaki odcisk palca, które są używane, aby wybrać certyfikat, podczas konfigurowania interfejsu API, aby korzystać z certyfikatów, co opisano w następujących [skonfigurować interfejs API do użycia certyfikat klienta na potrzeby uwierzytelniania bramy] [ Configure an API to use a client certificate for gateway authentication] sekcji.

> Aby wyłączyć weryfikacji łańcucha certyfikatu przy użyciu, na przykład certyfikatu z podpisem własnym, wykonaj czynności opisane w tym artykule [elementu](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).
> 
> 

## <a name="step1a"> </a>Usuń certyfikat klienta
Aby usunąć certyfikat, kliknij przycisk **usunąć** obok żądanego certyfikatu.

![Usuń certyfikat][api-management-certificate-delete]

Kliknij przycisk **tak, usuń go** o potwierdzenie.

![Potwierdzenie usunięcia][api-management-confirm-delete]

Jeśli certyfikat jest używany przez interfejs API, a następnie zostanie wyświetlony ekran ostrzeżenie. Aby usunąć certyfikat należy najpierw usunąć certyfikat z żadnych interfejsów API, które są skonfigurowane do używania go.

![Potwierdzenie usunięcia][api-management-confirm-delete-policy]

## <a name="step2"> </a>Skonfiguruj interfejs API do używania certyfikatu klienta uwierzytelniania bramy
Kliknij przycisk **interfejsów API** z **zarządzanie interfejsami API** menu po lewej stronie kliknij nazwę żądanego interfejsu API, a następnie kliknij przycisk **zabezpieczeń** kartę.

![Zabezpieczeń interfejsu API][api-management-api-security]

Wybierz **certyfikaty klienta** z **przy użyciu poświadczeń** listy rozwijanej.

![Certyfikaty klienta][api-management-mutual-certificates]

Wybierz żądany certyfikat z **certyfikatu klienta** listy rozwijanej. Jeśli dostępnych jest wiele certyfikatów można przyjrzeć się podmiot lub cztery ostatnie znaki odcisk palca, zgodnie z opisem w poprzedniej sekcji, aby określić odpowiedni certyfikat.

![Wybierz certyfikat][api-management-select-certificate]

Kliknij przycisk **zapisać** można zapisać zmiany konfiguracji do interfejsu API.

> Ta zmiana zaczyna się od razu i wywołania operacji tego interfejsu API będzie używany certyfikat do uwierzytelniania na serwerze zaplecza.
> 
> 

![Zapisz zmiany interfejsu API][api-management-save-api]

> Jeśli nie określono certyfikatu bramy uwierzytelniania usługi zaplecza interfejsu API, staje się częścią zasad dla tego interfejsu API i mogą być wyświetlane w edytorze zasad.
> 
> 

![Zasady certyfikatów][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Jeśli używane są certyfikaty z podpisem własnym, musisz wyłączyć weryfikacji łańcucha certyfikatu w kolejności dla interfejsu API zarządzania do komunikacji z systemem zaplecza, w przeciwnym razie zwróci kod komunikatu o błędzie 500. Aby to skonfigurować, można użyć [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (dla nowego zaplecza) lub [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (dla istniejących zaplecza) poleceń cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



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



