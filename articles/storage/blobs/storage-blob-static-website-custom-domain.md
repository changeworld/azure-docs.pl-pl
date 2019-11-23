---
title: 'Tutorial: Enable custom domain & SSL for a static website in Azure'
description: Dowiedz się, w jaki sposób skonfigurować domenę niestandardową w celu hostowania statycznej witryny internetowej.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327495"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: Enable custom domain & SSL for a static website in Azure

Ten samouczek jest drugą częścią serii. Dowiesz się z niego, jak włączyć punkt końcowy domeny niestandardowej z protokołem SSL na potrzeby statycznej witryny internetowej. 

W tym samouczku pokazano korzystanie z usługi [Azure CDN](../../cdn/cdn-overview.md) w celu skonfigurowania punktu końcowego domeny niestandardowej dla statycznej witryny internetowej. Dzięki usłudze Azure CDN możesz jednocześnie aprowizować niestandardowe certyfikaty SSL, korzystać z domen niestandardowych i konfigurować niestandardowe reguły regenerowania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Usługa Azure CDN umożliwia również szyfrowanie za pomocą protokołu SSL z użyciem własnego certyfikatu. Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej
> * Włączanie domeny niestandardowej oraz protokołu SSL

## <a name="prerequisites"></a>Wymagania wstępne

Before you begin this tutorial, complete part one, [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej

1. Locate your storage account in the Azure portal and display the account overview.
1. Wybierz pozycję **Azure CDN** w menu **Blob Service**, aby skonfigurować usługę Azure CDN.
1. In the **CDN profile** section, specify a new or existing CDN profile. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md).
1. Specify a pricing tier for the CDN endpoint. This tutorial uses the **Standard Akamai** pricing tier, because it propagates quickly, typically within a few minutes. Other pricing tiers may take longer to propagate, but may also offer other advantages. For more information, see [Compare Azure CDN product features](../../cdn/cdn-features.md).
1. In the **CDN endpoint name** field, specify a name for your CDN endpoint. The CDN endpoint must be unique across Azure.
1. Specify your the static website endpoint in the **Origin hostname** field. To find your static website endpoint, navigate to the **Static website** settings for your storage account. Copy the primary endpoint and paste it into the CDN configuration, removing the protocol identifier (*e.g.* , HTTPS).

    The following image shows an example endpoint configuration:

    ![Screenshot showing sample CDN endpoint configuration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Create the CDN endpoint and wait for it to propagate.
1. To verify that the CDN endpoint is configured correctly, click on the endpoint to navigate to its settings. From the CDN overview for your storage account, locate the endpoint hostname, and navigate to the endpoint, as shown in the following image. The format of your CDN endpoint will be similar to `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot showing overview of CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Once the CDN endpoint propagation is complete, navigating to the CDN endpoint displays the contents of the index.html file that you previously uploaded to your static website.

1. To review the origin settings for your CDN endpoint, navigate to **Origin** under the **Settings** section for your CDN endpoint. You will see that the **Origin type** field is set to *Custom Origin* and that the **Origin hostname** field displays your static website endpoint.

    ![Screenshot showing Origin settings for CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Włączanie domeny niestandardowej oraz protokołu SSL

1. Utwórz rekord CNAME z nazwą dostawcy domeny, aby przekierować domenę niestandardową do punktu końcowego usługi CDN. Rekord CNAME dla poddomeny *www* powinien wyglądać mniej więcej tak:

    ![Określanie rekordu CNAME dla poddomeny www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. In the Azure portal, display the settings for your CDN endpoint. Navigate to **Custom domains** under **Settings** to configure the custom domain and the SSL certificate.
1. Wybierz polecenie **Dodaj domenę niestandardową** i wprowadź nazwę domeny, a następnie kliknij polecenie **Dodaj**.
1. Select the new custom domain mapping to provision an SSL certificate.
1. Set **Custom Domain HTTPS** to **ON**, then click **Save**. It may take several hours to configure your custom domain. The portal displays progress as shown in the following image.

    ![Screenshot showing progress of custom domain configuration](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Test the mapping of your static website to your custom domain by accessing the URL for your custom domain.

For more information about enabling HTTPS for custom domains, see [Tutorial: Configure HTTPS on an Azure CDN custom domain](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Następne kroki

W drugiej części tego samouczka pokazano, jak skonfigurować domenę niestandardową z protokołem SSL w usłudze Azure CDN dla statycznej witryny internetowej.

For more information about configuring and using Azure CDN, see [What is Azure CDN?](../../cdn/cdn-overview.md).