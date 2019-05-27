---
title: 'Samouczek: Włączanie domeny niestandardowej z protokołem SSL w statycznej witrynie internetowej przy użyciu usługi Azure CDN — Azure Storage'
description: Dowiedz się, w jaki sposób skonfigurować domenę niestandardową w celu hostowania statycznej witryny internetowej.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226093"
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

1. Odszukaj konto magazynu w witrynie Azure portal i wyświetlić przegląd konta.
1. Wybierz pozycję **Azure CDN** w menu **Blob Service**, aby skonfigurować usługę Azure CDN.
1. W **profil CDN** sekcji, określ nowy lub istniejący profil CDN. Aby uzyskać więcej informacji, zobacz temat [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md).
1. Określ warstwę cenową dla punktu końcowego usługi CDN. W tym samouczku **warstwy Standard Akamai** warstwy cenowej, ponieważ jego rozprzestrzenia się szybko, zazwyczaj w ciągu kilku minut. Innej warstwy cenowej może potrwać dłużej Propagacja, ale mogą także oferować inne korzyści. Aby uzyskać więcej informacji, zobacz [funkcji produktu porównanie usługi Azure CDN](../../cdn/cdn-features.md).
1. W **Nazwa punktu końcowego usługi CDN** pola, określ nazwę dla punktu końcowego usługi CDN. Punkt końcowy usługi CDN musi być unikatowa w obrębie platformy Azure.
1. Określ jest punkt końcowy statycznej witryny internetowej w **nazwa hosta źródła** pola. Aby znaleźć punkt końcowy statycznej witryny internetowej, przejdź do **statycznej witryny internetowej** ustawienia konta magazynu. Kopiowanie podstawowego punktu końcowego i wkleić go do konfiguracji usługi CDN, usunięcie identyfikatora protokołu (*np.* , HTTPS).

    Na poniższej ilustracji przedstawiono przykładową konfigurację punktu końcowego:

    ![Zrzut ekranu przedstawiający przykładowy CDN punktu końcowego konfiguracji](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Tworzenie punktu końcowego usługi CDN i poczekaj na jego propagację.
1. Aby sprawdzić, czy punkt końcowy usługi CDN jest poprawnie skonfigurowana, kliknij w punkcie końcowym, aby przejść do jego ustawień. Od omówienia usługi CDN dla konta magazynu Znajdź nazwę hosta punktu końcowego, a następnie przejdź do punktu końcowego, jak pokazano na poniższej ilustracji. Format punktu końcowego usługi CDN będą wyglądać mniej więcej `https://staticwebsitesamples.azureedge.net`.

    ![Zrzut ekranu przedstawiający Przegląd punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Po zakończeniu Propagacja punktu końcowego usługi CDN, przechodząc do punktu końcowego usługi CDN Wyświetla zawartość pliku index.html, który został wcześniej przekazany do statycznej witryny internetowej.

1. Aby przejrzeć ustawienia źródła dla punktu końcowego usługi CDN, przejdź do **pochodzenia** w obszarze **ustawienia** sekcji dla punktu końcowego usługi CDN. Zostanie wyświetlony **typ źródła** pole jest ustawione na *Źródło niestandardowe* i **nazwa hosta źródła** pole zawiera punkt końcowy statycznej witryny internetowej.

    ![Zrzut ekranu przedstawiający ustawienia punktu początkowego do punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Włączanie domeny niestandardowej oraz protokołu SSL

1. Utwórz rekord CNAME z nazwą dostawcy domeny, aby przekierować domenę niestandardową do punktu końcowego usługi CDN. Rekord CNAME dla poddomeny *www* powinien wyglądać mniej więcej tak:

    ![Określanie rekordu CNAME dla poddomeny www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. W witrynie Azure portal Wyświetl ustawienia punktu końcowego usługi CDN. Przejdź do **domen niestandardowych** w obszarze **ustawienia** skonfigurować certyfikat SSL i domeną niestandardową.
1. Wybierz polecenie **Dodaj domenę niestandardową** i wprowadź nazwę domeny, a następnie kliknij polecenie **Dodaj**.
1. Wybierz nowe mapowanie domeny niestandardowej do obsługi administracyjnej certyfikatu SSL.
1. Ustaw **niestandardowy protokół HTTPS dla domeny** do **ON**, następnie kliknij przycisk **Zapisz**. Może upłynąć kilka godzin, aby skonfigurować domenę niestandardową. Portal Wyświetla postęp, jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu pokazujący postęp konfiguracji domeny niestandardowej](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Przetestuj mapowanie statycznej witryny internetowej na domenę niestandardową, uzyskując dostęp do adresu URL dla domeny niestandardowej.

Aby uzyskać więcej informacji na temat włączania protokołu HTTPS dla domen niestandardowych, zobacz [samouczka: Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Kolejne kroki

W drugiej części tego samouczka pokazano, jak skonfigurować domenę niestandardową z protokołem SSL w usłudze Azure CDN dla statycznej witryny internetowej.

Aby uzyskać więcej informacji na temat konfigurowania i używania usługi Azure CDN, zobacz [co to jest usługa Azure CDN?](../../cdn/cdn-overview.md).