---
title: 'Samouczek: Włączanie domeny niestandardowej z protokołem SSL w statycznej witrynie internetowej przy użyciu usługi Azure CDN — Azure Storage'
description: Dowiedz się, w jaki sposób skonfigurować domenę niestandardową w celu hostowania statycznej witryny internetowej.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61427532"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Samouczek: Korzystanie z usługi Azure CDN w celu włączenia domeny niestandardowej dla statycznej witryny internetowej

Ten samouczek jest drugą częścią serii. Dowiesz się z niego, jak włączyć punkt końcowy domeny niestandardowej z protokołem SSL na potrzeby statycznej witryny internetowej. 

W tym samouczku pokazano korzystanie z usługi [Azure CDN](../../cdn/cdn-overview.md) w celu skonfigurowania punktu końcowego domeny niestandardowej dla statycznej witryny internetowej. Dzięki usłudze Azure CDN możesz jednocześnie aprowizować niestandardowe certyfikaty SSL, korzystać z domen niestandardowych i konfigurować niestandardowe reguły regenerowania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Usługa Azure CDN umożliwia również szyfrowanie za pomocą protokołu SSL z użyciem własnego certyfikatu. Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej
> * Włączanie domeny niestandardowej oraz protokołu SSL

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem ukończ część pierwszą, [Samouczek: Hostowanie statycznej witryny internetowej w usłudze Blob Storage ](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/) w przeglądarce internetowej. 
1. Znajdź konto magazynu i wyświetl omówienie konta.
1. Wybierz pozycję **Azure CDN** w menu **Blob Service**, aby skonfigurować usługę Azure CDN.
1. W sekcji **Nowy punkt końcowy** wypełnij pola, aby utworzyć nowy punkt końcowy usługi CDN.
1. Wprowadź nazwę punktu końcowego, na przykład *mystaticwebsiteCDN*.
1. Wprowadź domenę witryny internetowej jako nazwę hosta dla punktu końcowego usługi CDN.
1. Jako nazwę hosta źródła wprowadź punkt końcowy statycznej witryny internetowej. Aby odnaleźć punkt końcowy statycznej witryny internetowej, przejdź do sekcji **Statyczna witryna internetowa** na swoim koncie magazynu i skopiuj punkt końcowy. 
1. Przetestuj punkt końcowy usługi CDN, przechodząc do adresu *mywebsitecdn.azureedge.net* w przeglądarce.

## <a name="enable-custom-domain-and-ssl"></a>Włączanie domeny niestandardowej oraz protokołu SSL

1. Utwórz rekord CNAME z nazwą dostawcy domeny, aby przekierować domenę niestandardową do punktu końcowego usługi CDN. Rekord CNAME dla poddomeny *www* powinien wyglądać mniej więcej tak:

    ![Określanie rekordu CNAME dla poddomeny www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. W witrynie Azure Portal kliknij nowo utworzony punkt końcowy, aby skonfigurować domenę niestandardową oraz certyfikat SSL.
1. Wybierz polecenie **Dodaj domenę niestandardową** i wprowadź nazwę domeny, a następnie kliknij polecenie **Dodaj**.
1. Wybierz mapowanie nowo utworzonej domeny niestandardowej, aby aprowizować certyfikat SSL.
1. Ustaw pole **Protokół HTTPS dla domeny niestandardowej** w pozycji **WŁĄCZONE**. Wybierz pozycję **Zarządzany przez usługę CDN**, aby usługa Azure CDN zarządzała certyfikatem SSL. Kliknij pozycję **Zapisz**.
1. Przetestuj witrynę internetową, przechodząc do adresu URL witryny internetowej.

## <a name="next-steps"></a>Kolejne kroki

W drugiej części tego samouczka pokazano, jak skonfigurować domenę niestandardową z protokołem SSL w usłudze Azure CDN dla statycznej witryny internetowej.

Kliknij tej link, aby dowiedzieć się więcej na temat hostowania statycznej witryny internetowej w usłudze Azure Storage.

> [!div class="nextstepaction"]
> [Learn more about static websites (Więcej informacji na temat statycznych witryn internetowych)](storage-blob-static-website.md)
