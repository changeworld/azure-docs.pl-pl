---
title: 'Samouczek: Włączanie niestandardowej domeny & SSL dla statycznej witryny sieci Web na platformie Azure'
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
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Samouczek: Włączanie niestandardowej domeny & SSL dla statycznej witryny sieci Web na platformie Azure

Ten samouczek jest drugą częścią serii. Dowiesz się z niego, jak włączyć punkt końcowy domeny niestandardowej z protokołem SSL na potrzeby statycznej witryny internetowej. 

W tym samouczku pokazano korzystanie z usługi [Azure CDN](../../cdn/cdn-overview.md) w celu skonfigurowania punktu końcowego domeny niestandardowej dla statycznej witryny internetowej. Dzięki usłudze Azure CDN możesz jednocześnie aprowizować niestandardowe certyfikaty SSL, korzystać z domen niestandardowych i konfigurować niestandardowe reguły regenerowania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Usługa Azure CDN umożliwia również szyfrowanie za pomocą protokołu SSL z użyciem własnego certyfikatu. Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej
> * Włączanie domeny niestandardowej oraz protokołu SSL

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem Ukończ część pierwszej, [Samouczek: hostuje statyczną witrynę sieci Web na BLOB Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Tworzenie punktu końcowego usługi CDN w puncie końcowym statycznej witryny internetowej

1. Znajdź konto magazynu w Azure Portal i Wyświetl przegląd konta.
1. Wybierz pozycję **Azure CDN** w menu **Blob Service**, aby skonfigurować usługę Azure CDN.
1. W sekcji **profil usługi CDN** określ nowy lub istniejący profil usługi CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md).
1. Określ warstwę cenową punktu końcowego usługi CDN. Ten samouczek używa standardowej warstwy cenowej **Akamai** , ponieważ szybko propaguje, zazwyczaj w ciągu kilku minut. Inne warstwy cenowe mogą trwać dłużej, ale mogą również oferować inne zalety. Aby uzyskać więcej informacji, zobacz [porównanie Azure CDN funkcji produktu](../../cdn/cdn-features.md).
1. W polu **Nazwa punktu końcowego usługi CDN** Podaj nazwę punktu końcowego sieci CDN. Punkt końcowy usługi CDN musi być unikatowy na platformie Azure.
1. Określ, że jesteś statycznym punktem końcowym witryny sieci Web w polu **Nazwa hosta źródła** . Aby znaleźć punkt końcowy statycznej witryny internetowej, przejdź do ustawień **statycznej witryny internetowej** dla konta magazynu. Skopiuj podstawowy punkt końcowy i wklej go do konfiguracji usługi CDN, usuwając Identyfikator protokołu (*np.* https).

    Na poniższej ilustracji przedstawiono przykładową konfigurację punktu końcowego:

    ![Zrzut ekranu przedstawiający przykładową konfigurację punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Utwórz punkt końcowy usługi CDN i poczekaj na jego propagowanie.
1. Aby sprawdzić, czy punkt końcowy usługi CDN został prawidłowo skonfigurowany, kliknij punkt końcowy, aby przejść do jego ustawień. W obszarze przegląd usługi CDN dla konta magazynu Znajdź nazwę hosta punktu końcowego i przejdź do punktu końcowego, jak pokazano na poniższej ilustracji. Format punktu końcowego usługi CDN będzie podobny do `https://staticwebsitesamples.azureedge.net`.

    ![Zrzut ekranu przedstawiający przegląd punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Po zakończeniu propagacji punktu końcowego usługi CDN przechodzenie do punktu końcowego usługi CDN spowoduje wyświetlenie zawartości pliku index. html, który został wcześniej przekazany do statycznej witryny sieci Web.

1. Aby przejrzeć ustawienia źródła dla punktu końcowego usługi CDN, przejdź do lokalizacji **źródłowej** w sekcji **Ustawienia** dla punktu końcowego usługi CDN. Zobaczysz, że w polu **Typ źródła** jest ustawiona wartość *Źródło niestandardowe* i że w polu **Nazwa hosta źródła** jest wyświetlany statyczny punkt końcowy witryny sieci Web.

    ![Zrzut ekranu przedstawiający ustawienia źródła dla punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Włączanie domeny niestandardowej oraz protokołu SSL

1. Utwórz rekord CNAME z nazwą dostawcy domeny, aby przekierować domenę niestandardową do punktu końcowego usługi CDN. Rekord CNAME dla poddomeny *www* powinien wyglądać mniej więcej tak:

    ![Określanie rekordu CNAME dla poddomeny www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. W Azure Portal Wyświetl ustawienia punktu końcowego usługi CDN. Przejdź do pozycji **domeny niestandardowe** w obszarze **Ustawienia** , aby skonfigurować domenę niestandardową i certyfikat SSL.
1. Wybierz polecenie **Dodaj domenę niestandardową** i wprowadź nazwę domeny, a następnie kliknij polecenie **Dodaj**.
1. Wybierz nowe niestandardowe mapowanie domeny, aby zainicjować certyfikat SSL.
1. Dla **domeny niestandardowej** ustaw wartość **włączone**, a następnie kliknij przycisk **Zapisz**. Skonfigurowanie domeny niestandardowej może potrwać kilka godzin. W portalu jest wyświetlany postęp, jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu przedstawiający postęp niestandardowej konfiguracji domeny](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Przetestuj mapowanie statycznej witryny internetowej na domenę niestandardową, uzyskując dostęp do adresu URL domeny niestandardowej.

Aby uzyskać więcej informacji na temat włączania protokołu HTTPS dla domen niestandardowych, zobacz [Samouczek: Konfigurowanie protokołu HTTPS w domenie niestandardowej Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Następne kroki

W drugiej części tego samouczka pokazano, jak skonfigurować domenę niestandardową z protokołem SSL w usłudze Azure CDN dla statycznej witryny internetowej.

Aby uzyskać więcej informacji o konfigurowaniu i używaniu Azure CDN, zobacz [co to jest Azure CDN?](../../cdn/cdn-overview.md).