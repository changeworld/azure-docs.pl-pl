---
title: Zarządzanie punktów końcowych przesyłania strumieniowego przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób zarządzania punktów końcowych przesyłania strumieniowego przy użyciu portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: 542780766cfa90026d5ff492fcf7b579cb2d7029
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790349"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Zarządzanie punktów końcowych przesyłania strumieniowego przy użyciu portalu Azure

W tym artykule pokazano, jak używać portalu Azure do zarządzania punktów końcowych przesyłania strumieniowego. 

>[!NOTE]
>Upewnij się przejrzeć [omówienie](media-services-streaming-endpoints-overview.md) artykułu. 

Aby uzyskać informacje na temat skalowania punktu końcowego przesyłania strumieniowego, zobacz [to](media-services-portal-scale-streaming-endpoints.md) artykułu.

## <a name="start-managing-streaming-endpoints"></a>Rozpocznij zarządzanie punktów końcowych przesyłania strumieniowego 

Aby rozpocząć zarządzanie punktów końcowych przesyłania strumieniowego dla Twojego konta, wykonaj następujące czynności.

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W **ustawienia** bloku, wybierz opcję **punkty końcowe przesyłania strumieniowego**.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Rozliczenie jest przeprowadzane tylko w przypadku przesyłania strumieniowego punktu końcowego jest w stanie uruchomienia.

## <a name="adddelete-a-streaming-endpoint"></a>Dodawanie/Usuwanie punktu końcowego przesyłania strumieniowego

>[!NOTE]
>Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego.

Do dodawania/usuwania punktu końcowego przesyłania strumieniowego przy użyciu portalu Azure, wykonaj następujące czynności:

1. Aby dodać punkt końcowy przesyłania strumieniowego, kliknij przycisk **+ punktu końcowego** w górnej części strony. 

    Jeśli planujesz umieszczenie różnych CDN lub CDN oraz bezpośredni dostęp do można wiele punktów końcowych przesyłania strumieniowego.

2. Aby usunąć punkt końcowy przesyłania strumieniowego, naciśnij klawisz **usunąć** przycisku.      
3. Kliknij przycisk **Start** przycisk, aby uruchomić punktu końcowego przesyłania strumieniowego.
   
    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Konfigurowanie punktu końcowego przesyłania strumieniowego
Punktu końcowego przesyłania strumieniowego, można skonfigurować następujące właściwości:

* Kontrola dostępu
* Kontrola pamięci podręcznej
* Granic lokacji zasad dostępu

Aby uzyskać szczegółowe informacje na temat tych właściwości, zobacz [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Po włączeniu sieci CDN w warstwie dostępu do adresu IP nie może uzyskać dostępu. IP dostępu ma zastosowanie tylko w przypadku, gdy nie mają CDN.

Można skonfigurować punktu końcowego przesyłania strumieniowego, wykonując następujące czynności:

1. Wybierz punktu końcowego przesyłania strumieniowego, który chcesz skonfigurować.
2. Kliknij przycisk **ustawienia**.

Następuje krótki opis tych pól.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maksymalna pamięć podręczna zasad: używane do konfigurowania okres istnienia pamięci podręcznej obsługiwanych przez ten punkt końcowy przesyłania strumieniowego zasobów. Jeśli wartość nie jest ustawiona, używana jest wartość domyślna. Wartości domyślne można zdefiniować w taki sposób, bezpośrednio w magazynie Azure. Po włączeniu usługi Azure CDN dla punktu końcowego przesyłania strumieniowego powinno nie wartość pamięci podręcznej zasad mniejszy niż 600 sekund.  
2. Dozwolone adresy IP: umożliwia określenie adresów IP, które będą mogły nawiązywać połączenia do opublikowanych punktu końcowego przesyłania strumieniowego. Jeśli nie określono adresów IP, dowolnego adresu IP będą mogli nawiązywać połączeń. Adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakresu adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski podsieci dziesiętną kropkami (na przykład 10.0.0.1 () 255.255.255.0) ").
3. Konfiguracja Akamai podpis nagłówka uwierzytelniania: można określić sposób skonfigurowania podpis nagłówka uwierzytelniania żądania z serwerów Akamai. Wygaśnięcia jest w formacie UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skalowanie programu Premium punktu końcowego przesyłania strumieniowego

Więcej informacji znajduje się w [tym](media-services-portal-scale-streaming-endpoints.md) artykule.

## <a id="enable_cdn"></a>Włącz integrację usługi Azure CDN

Podczas tworzenia nowego konta integracji przesyłania strumieniowego punktu końcowego usługi Azure CDN domyślny jest domyślnie włączona.

Jeśli zechcesz później Włącz/Wyłącz CDN, musi należeć do punktu końcowego przesyłania strumieniowego **zatrzymana** stanu. Może zająć do dwóch godzin dla ustawiany integracji usługi Azure CDN i zmiany jako aktywnych we wszystkich CDN POP. Jednak można uruchomić punktu końcowego oraz strumienia bez przerw w działaniu przesyłania z punktu końcowego przesyłania strumieniowego i po ukończeniu integracji strumień jest dostarczany z sieci CDN. W okresie inicjowania obsługi administracyjnej punktu końcowego przesyłania strumieniowego będzie w **uruchamianie** stanu i użytkownik może obserwować degredad wydajności.

Integracja usługi CDN jest włączona w centrach danych platformy Azure z wyjątkiem Chin i federalnych regionów.

Gdy ta funkcja jest włączona, **kontroli dostępu**, ** niestandardowa nazwa hosta, i **uwierzytelnianie za pomocą sygnatury Akamai** konfiguracja zostanie wyłączona.
 
> [!IMPORTANT]
> Integracja usługi Azure Media Services z usługą Azure CDN jest wdrażana w **Azure CDN from Verizon** standardu punkty końcowe przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego Premium można skonfigurować przy użyciu wszystkich **Azure CDN ceny warstw i dostawców**. Aby uzyskać więcej informacji na temat funkcji usługi Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Dodatkowe zagadnienia

* Po włączeniu przesyłania strumieniowego punktu końcowego CDN klientów nie można żądać zawartości bezpośrednio ze źródła. Jeśli potrzebna jest możliwość testowania treści lub bez CDN, można utworzyć innej przesyłania strumieniowego punktu końcowego, który nie jest włączona w sieci CDN.
* Twoje przesyłania strumieniowego hosta punktu końcowego nie zmienia się po włączeniu usługi CDN. Nie należy wprowadzać żadnych zmian do przepływu pracy usług media po włączeniu usługi CDN. Na przykład jeśli Twoje przesyłania strumieniowego hosta punktu końcowego jest strasbourg.streaming.mediaservices.windows.net, po włączeniu usługi CDN, dokładnie tej samej nazwy hosta jest używana.
* Dla nowych punktów końcowych przesyłania strumieniowego można włączyć CDN za tworzenie nowego punktu końcowego; istniejące punkty końcowe przesyłania strumieniowego należy najpierw zatrzymać punktu końcowego, a następnie Włącz/Wyłącz CDN.
* Standardowego punktu końcowego przesyłania strumieniowego można skonfigurować tylko za pomocą **dostawcy sieci CDN w warstwie standardowa Verizon** przy użyciu klasycznego portalu Azure. Jednak można włączyć innych dostawców usługi Azure CDN przy użyciu interfejsów API REST.

## <a name="configure-cdn-profile"></a>Skonfiguruj profil CDN

Można skonfigurować profil CDN, wybierając **Zarządzanie CDN** przycisk od góry.

![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

