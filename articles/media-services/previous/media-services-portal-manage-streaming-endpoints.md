---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 1775bbb2913f6b1a985ca7ec9e89bafed42fd0e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61129715"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure portal 

W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego przy użyciu witryny Azure portal. 

>[!NOTE]
>Upewnij się zapoznać się z [Przegląd](media-services-streaming-endpoints-overview.md) artykułu. 

Aby dowiedzieć się, jak skalować punkt końcowy przesyłania strumieniowego, zobacz [to](media-services-portal-scale-streaming-endpoints.md) artykułu.

## <a name="start-managing-streaming-endpoints"></a>Rozpoczynanie zarządzania punktami końcowymi przesyłania strumieniowego 

Aby rozpocząć zarządzanie punktami końcowymi przesyłania strumieniowego dla swojego konta, wykonaj następujące czynności.

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W **ustawienia** bloku wybierz **punkty końcowe przesyłania strumieniowego**.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="adddelete-a-streaming-endpoint"></a>Dodawanie/Usuwanie punktu końcowego przesyłania strumieniowego

>[!NOTE]
>Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Do dodawania/usuwania punktu końcowego przesyłania strumieniowego przy użyciu witryny Azure portal, wykonaj następujące czynności:

1. Aby dodać punkt końcowy przesyłania strumieniowego, kliknij **+ punkt końcowy** w górnej części strony. 

    Możesz chcieć wiele punktów końcowych przesyłania strumieniowego, jeśli planujesz mieć różne usługi CDN lub CDN i bezpośredni dostęp.

2. Aby usunąć punktu końcowego przesyłania strumieniowego, naciśnij **Usuń** przycisku.      
3. Kliknij przycisk **Start** przycisk, aby uruchomić punkt końcowy przesyłania strumieniowego.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Konfigurowanie punktu końcowego przesyłania strumieniowego
Punkt końcowy przesyłania strumieniowego, można skonfigurować następujące właściwości:

* Kontrola dostępu
* Kontrola pamięci podręcznej
* Obejmujące wiele lokacji zasad dostępu

Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Po włączeniu usługi CDN nie masz dostępu do dostępu do adresów IP. Dostęp do adresu IP ma zastosowanie tylko w przypadku, gdy nie masz usługi CDN.

Punkt końcowy przesyłania strumieniowego można skonfigurować, wykonując następujące czynności:

1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz skonfigurować.
2. Kliknij pozycję **Ustawienia**.

Krótki opis pól poniżej.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Zasady dotyczące maksymalnej pamięci podręcznej: używane do konfigurowania okres istnienia pamięci podręcznej dla elementów zawartości dostarczanych za pośrednictwem tego punktu końcowego przesyłania strumieniowego. Jeśli wartość nie jest ustawiona, używana jest wartość domyślna. Domyślne wartości można zdefiniować w taki sposób, bezpośrednio w usłudze Azure storage. Usługi Azure CDN została włączona dla punktu końcowego przesyłania strumieniowego, nie należy ustawić wartość zasad pamięci podręcznej na mniej niż 600 sekund.  
2. Dozwolone adresy IP: można określić adresy IP, które będzie mogła nawiązać połączenie z opublikowanych punkt końcowy przesyłania strumieniowego. Jeśli nie określono adresów IP, dowolny adres IP będzie nawiązać połączenie. Adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski notacji dziesiętnej podsieci (na przykład "10.0.0.1 () 255.255.255.0) ").
3. Konfiguracja uwierzytelnianie nagłówków podpisów Akamai: używany do określenia sposobu skonfigurowania żądanie uwierzytelnienia nagłówków podpisów z serwerów Akamai. Czas wygaśnięcia jest w formacie UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skalowanie usługi Premium, punkt końcowy przesyłania strumieniowego

Więcej informacji znajduje się w [tym](media-services-portal-scale-streaming-endpoints.md) artykule.

## <a id="enable_cdn"></a>Włączanie integracji z usługą Azure CDN

Podczas tworzenia nowego konta integracji przesyłania strumieniowego punktu końcowego usługi Azure CDN domyślny jest domyślnie włączona.

Jeśli zechcesz później wyłączanie/włączanie usługi CDN, musi należeć do punktu końcowego przesyłania strumieniowego **zatrzymana** stanu. Może potrwać do dwóch godzin dla integracji usługi Azure CDN włączany i zmian jako aktywny we wszystkich punktach obecności usługi CDN. Jednakże możesz uruchomić punkt końcowy przesyłania strumieniowego i strumienia bez przerw w pracy z punktu końcowego przesyłania strumieniowego, a po ukończeniu integracji strumień jest dostarczany z sieci CDN. W okresie udostępniania punktu końcowego przesyłania strumieniowego będą w **uruchamianie** stanu i użytkownik może obserwować pogorszenie wydajności.

Integracja usługi CDN jest włączona w centrach danych platformy Azure z wyjątkiem Chin i rządu federalnego regionów.

Po włączeniu, **kontroli dostępu**, ** niestandardowa nazwa hosta, a **uwierzytelniania podpisów serwerów Akamai** Konfiguracja pobiera wyłączona.
 
> [!IMPORTANT]
> Integracja usługi Azure Media Services z usługą Azure CDN jest wdrażana w **Azure CDN from Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego Premium można skonfigurować za pomocą wszystkich **usługi Azure CDN ceny warstwy i dostawców**. Aby uzyskać więcej informacji na temat funkcji usługi Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Jeśli CDN została włączona dla punktu końcowego przesyłania strumieniowego, klienci nie mogą zażądać zawartości bezpośrednio ze źródła. Jeśli potrzebna jest możliwość testowania treści z lub bez usługi CDN, możesz utworzyć inny punkt końcowy przesyłania strumieniowego nie włączono usługę CDN.
* Przesyłania strumieniowego nazwę hosta punktu końcowego nie zmienia się po włączeniu usługi CDN. Nie musisz wprowadzać żadnych zmian usługi przepływu pracy usługi media services po włączeniu usługi CDN. Na przykład w przypadku przesyłania strumieniowego nazwę hosta punktu końcowego strasbourg.streaming.mediaservices.windows.net, po włączeniu usługi CDN, dokładnie tej samej nazwy hosta jest używana.
* Nowe punkty końcowe przesyłania strumieniowego można włączyć sieć CDN po prostu tworząc nowy punkt końcowy; istniejące punkty końcowe przesyłania strumieniowego należy najpierw zatrzymać punktu końcowego i następnie Włączanie/wyłączanie usługi CDN.
* Standardowy punkt końcowy przesyłania strumieniowego można skonfigurować tylko przy użyciu **dostawcy CDN w warstwie standardowa firmy Verizon** przy użyciu klasycznej witryny Azure portal. Jednakże można włączyć innych dostawców rozwiązań w usłudze Azure CDN przy użyciu interfejsów API REST.

## <a name="configure-cdn-profile"></a>Konfigurowanie profilu CDN

Można skonfigurować profil usługi CDN, wybierając **zarządzania sieci CDN** przycisk od góry.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

