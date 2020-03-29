---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą portalu Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą witryny Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900879"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure Portal 

W tym artykule pokazano, jak używać witryny Azure portal do zarządzania punktami końcowymi przesyłania strumieniowego. 

>[!NOTE]
>Zapoznaj się z [omówieniem](media-services-streaming-endpoints-overview.md) artykułu. 

Aby uzyskać informacje dotyczące skalowania punktu końcowego przesyłania strumieniowego, zobacz [ten](media-services-portal-scale-streaming-endpoints.md) artykuł.

## <a name="start-managing-streaming-endpoints"></a>Rozpoczynanie zarządzania punktami końcowymi przesyłania strumieniowego 

Aby rozpocząć zarządzanie punktami końcowymi przesyłania strumieniowego dla swojego konta, wykonaj następujące czynności.

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W bloku **Ustawienia** wybierz pozycję **Punkty końcowe przesyłania strumieniowego**.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Naliczane są tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomionym.

## <a name="adddelete-a-streaming-endpoint"></a>Dodawanie/usuwanie punktu końcowego przesyłania strumieniowego

>[!NOTE]
>Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Aby dodać/usunąć punkt końcowy przesyłania strumieniowego przy użyciu portalu Azure portal, wykonaj następujące czynności:

1. Aby dodać punkt końcowy przesyłania strumieniowego, kliknij **+ punkt końcowy** u góry strony. 

    Jeśli planujesz mieć różne sieci CDN lub sieć CDN i bezpośredni dostęp, można mieć wiele punktów końcowych przesyłania strumieniowego.

2. Aby usunąć punkt końcowy przesyłania strumieniowego, naciśnij przycisk **Usuń.**      
3. Kliknij przycisk **Start,** aby uruchomić punkt końcowy przesyłania strumieniowego.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurowanie punktu końcowego przesyłania strumieniowego
Punkt końcowy przesyłania strumieniowego umożliwia skonfigurowanie następujących właściwości:

* Kontrola dostępu
* Kontrola pamięci podręcznej
* Zasady dostępu do różnych witryn

Aby uzyskać szczegółowe informacje na temat tych właściwości, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Gdy sieć CDN jest włączona, nie można uzyskać dostępu do adresu IP. Dostęp do ip ma zastosowanie tylko wtedy, gdy nie masz sieci CDN.

Punkt końcowy przesyłania strumieniowego można skonfigurować, wykonując następujące czynności:

1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz skonfigurować.
2. Kliknij przycisk **Ustawienia**.

Poniżej znajduje się krótki opis pól.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maksymalna zasada pamięci podręcznej: służy do konfigurowania okresu istnienia pamięci podręcznej dla zasobów obsługiwanych za pośrednictwem tego punktu końcowego przesyłania strumieniowego. Jeśli żadna wartość nie jest ustawiona, używana jest wartość domyślna. Wartości domyślne można również zdefiniować bezpośrednio w magazynie platformy Azure. Jeśli usługa Azure CDN jest włączona dla punktu końcowego przesyłania strumieniowego, nie należy ustawiać wartości zasad pamięci podręcznej na mniej niż 600 sekund.  
2. Dozwolone adresy IP: używane do określania adresów IP, które mogłyby łączyć się z opublikowanym punktem końcowym przesyłania strumieniowego. Jeśli nie określono żadnych adresów IP, każdy adres IP będzie mógł się połączyć. Adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres ADRESÓW IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres IP przy użyciu adresu IP i kropkowana podsieć dziesiętnej maski (na przykład '10.0.0.1(255.255.255.0)").
3. Konfiguracja uwierzytelniania nagłówka podpisu Akamai: służy do określania sposobu konfigurowania żądania uwierzytelniania nagłówka podpisu z serwerów Akamai. Wygaśnięcie jest w utc.

## <a name="scale-your-premium-streaming-endpoint"></a>Skalowanie punktu końcowego przesyłania strumieniowego w wersji Premium

Więcej informacji znajduje się w [tym](media-services-portal-scale-streaming-endpoints.md) artykule.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Włączanie integracji usługi Azure CDN

Podczas tworzenia nowego konta domyślna integracja usługi Azure CDN w programie Endpoint Endpoint jest domyślnie włączona.

Jeśli później chcesz wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi być w stanie **zatrzymania.** Włączenie integracji usługi Azure w sieci CDN może potrwać do dwóch godzin, a zmiany będą aktywne we wszystkich usługach YKP usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i przesyłać strumieniowo bez przerw z punktu końcowego przesyłania strumieniowego, a po zakończeniu integracji strumień jest dostarczany z sieci CDN. W okresie inicjowania obsługi administracyjnej punkt końcowy przesyłania strumieniowego będzie w stanie **początkowym** i można zaobserwować obniżoną wydajność.

Integracja usługi CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem regionów Chin i rządu federalnego.

Po włączeniu kontrola **dostępu**, ** Niestandardowa nazwa hosta i **konfiguracja uwierzytelniania podpisu Akamai** zostanie wyłączona.
 
> [!IMPORTANT]
> Integracja usługi Azure Media Services z usługą Azure CDN jest implementowana w **usłudze Azure CDN firmy Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **warstw i dostawców usług Azure CDN.** Aby uzyskać więcej informacji na temat funkcji usługi Azure CDN, zobacz [omówienie usługi CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Gdy sieć CDN jest włączona dla punktu końcowego przesyłania strumieniowego, klienci nie mogą żądać zawartości bezpośrednio ze źródła. Jeśli potrzebujesz możliwości testowania zawartości z siecią CDN lub bez niej, możesz utworzyć inny punkt końcowy przesyłania strumieniowego, który nie jest włączony.
* Nazwa hosta punktu końcowego przesyłania strumieniowego pozostaje taka sama po włączeniu sieci CDN. Po włączeniu usługi CDN nie trzeba wprowadzać żadnych zmian w przepływie pracy usług multimedialnych. Na przykład jeśli nazwa hosta punktu końcowego przesyłania strumieniowego jest strasbourg.streaming.mediaservices.windows.net, po włączeniu sieci CDN używana jest dokładnie ta sama nazwa hosta.
* Dla nowych punktów końcowych przesyłania strumieniowego można włączyć cdn po prostu tworząc nowy punkt końcowy; dla istniejących punktów końcowych przesyłania strumieniowego, należy najpierw zatrzymać punkt końcowy, a następnie włączyć/wyłączyć sieć CDN.
* Standardowy punkt końcowy przesyłania strumieniowego można skonfigurować tylko przy użyciu **dostawcy usługi Verizon Standard CDN** przy użyciu klasycznego portalu platformy Azure. Można jednak włączyć innych dostawców usługi Azure CDN przy użyciu interfejsów API REST.

## <a name="configure-cdn-profile"></a>Konfigurowanie profilu sieci CDN

Profil sieci CDN można skonfigurować, wybierając przycisk **Zarządzaj siecią CDN** u góry.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

