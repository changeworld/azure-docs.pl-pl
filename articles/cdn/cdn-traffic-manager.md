---
title: Konfigurowanie trybu failover między wiele punktów końcowych usługi Azure CDN z usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu ustawiania usługi Azure Traffic Manager z punktów końcowych usługi Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: b52cad1f32cc3d16cf70bb81640dcb1d9f8614bf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133041"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurowanie trybu failover między wiele punktów końcowych usługi Azure CDN z usługi Azure Traffic Manager

Po skonfigurowaniu Azure sieci dostarczania zawartości (CDN), można wybrać optymalne dostawcy i warstwę cenową do własnych potrzeb. Usługi Azure CDN z jego infrastruktury globalnie rozproszone, domyślnie tworzy lokalne i geograficzne nadmiarowość i równoważenie do zwiększenia wydajności i dostępności usługi globalne obciążenia. Jeśli lokalizacja nie jest dostępna do obsługi zawartości, żądania są automatycznie kierowane do innej lokalizacji i optymalną punktu obecności (oparte na czynniki, takie jak żądanie lokalizacji i obciążenia serwera) jest używany do obsługi każdego żądania klienta. 
 
Jeśli masz wiele profilów CDN dalszego zwiększenia dostępności i wydajności z usługi Azure Traffic Manager. Za pomocą usługi Azure Traffic Manager i usługi Azure CDN załadować równowagi między wiele punktów końcowych usługi CDN dla trybu failover, obciążenia geo równoważenia i innych scenariuszy. W przypadku typowej trybu failover wszystkie żądania klienta najpierw są kierowane do głównej profilu CDN; Jeśli profil jest niedostępny, żądania są następnie przekazywane do dodatkowej profilu CDN, dopóki podstawowy profil CDN jest znowu w trybie online. Za pomocą usługi Azure Traffic Manager w ten sposób gwarantuje, że aplikacja sieci web jest zawsze dostępna. 

Ten artykuł zawiera wskazówki i przykład sposobu konfigurowania trybu failover z **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów.

## <a name="set-up-azure-cdn"></a>Konfigurowanie usługi Azure CDN 
Utwórz co najmniej dwa profile usługi Azure CDN, jak i punkty końcowe z różnych dostawców.

1. Utwórz **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilu, wykonując kroki opisane w [Utwórz nowy profil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN wiele profilów](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. W każdym nowych profilów utworzyć co najmniej jeden punkt końcowy, wykonując kroki opisane w [utworzyć nowy punkt końcowy CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Ustaw usługi Azure Traffic Manager
Utwórz profil Menedżera ruchu Azure i skonfigurować Równoważenie obciążenia w punktami końcowymi CDN. 

1. Tworzenie profilu Menedżera ruchu Azure, wykonując kroki opisane w [Tworzenie profilu Menedżera ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1). 

    Aby uzyskać **metody routingu**, wybierz pozycję **priorytet**.

2. Dodaj punktami końcowymi CDN w Twoim profilu usługi Traffic Manager, wykonując kroki opisane w [punkty końcowe dodać Menedżera ruchu](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Aby uzyskać **typu**, wybierz pozycję **zewnętrzne punkty końcowe**. Aby uzyskać **priorytet**, wprowadź liczbę z zakresu.

    Na przykład utworzyć *cdndemo101akamai.azureedge.net* z priorytet *1* i *cdndemo101verizon.azureedge.net* z priorytet *2*.

   ![Punkty końcowe Menedżera ruchu w sieci CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurowanie domeny niestandardowej Azure CDN i usługi Azure Traffic Manager
Po skonfigurowaniu profilów CDN i Menedżera ruchu, wykonaj następujące kroki, Dodaj mapowanie DNS i rejestrowanie niestandardowe domeny do punktów końcowych usługi CDN. Na przykład nazwa domeny niestandardowej jest *cdndemo101.dustydogpetcare.online*.

1. Przejdź do witryny sieci web dostawcy domeny domeny niestandardowe, takie jak GoDaddy i Utwórz dwa wpisy CNAME w systemie DNS. 

    a. Na pierwszej pozycji CNAME mapowania domenę niestandardową, z poddomeny cdnverify punktu końcowego CDN. Ten wpis jest wymagany krok zarejestrować domeny niestandardowej z punktem końcowym CDN, dodanego do usługi Traffic Manager w kroku 2.

      Na przykład: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Dla drugiego wpis CNAME mapy domeny niestandardowej, bez poddomeny cdnverify do punktu końcowego CDN. Ten wpis mapy domeny niestandardowej do usługi Traffic Manager. 

      Na przykład: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Jeśli domeny jest obecnie na żywo i nie można przerwać, ostatni wykonaj ten krok. Sprawdź, czy punktów końcowych usługi CDN i domeny Menedżera ruchu są na żywo przed zaktualizowaniem domenę niestandardową DNS do usługi Traffic Manager.
    >


2.  Z profilu Azure CDN wybierz pierwszy punkt końcowy CDN (Akamai). Wybierz **Dodaj domenę niestandardową** i wejściowego *cdndemo101akamai.azureedge.net*. Sprawdź, czy zielony znacznik wyboru, aby zweryfikować domenę niestandardową. 

    Używa usługi Azure CDN *cdnverify* poddomeny można sprawdzić poprawności mapowania DNS, aby ukończyć ten proces rejestracji. Aby uzyskać więcej informacji, zobacz [utworzyć rekord CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ten krok powoduje włączenie usługi Azure CDN rozpoznawanie domeny niestandardowej, dzięki czemu mogą odpowiadać na żądania jego.

3.  Wróć do witryny sieci web dostawcy domeny niestandardowe domeny i zaktualizuj pierwszy mapowanie DNS utworzony w tak, aby domeny niestandardowej jest mapowany na drugi punkt końcowy CDN.
                             
    Na przykład: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Z profilu Azure CDN wybierz drugiego punktu końcowego CDN (Verizon), a następnie powtórz krok 2. Wybierz **Dodaj domenę niestandardową**i wejściowego *cdndemo101akamai.azureedge.net*.
 
Po wykonaniu tych kroków usługi CDN wielu z możliwości trybu failover skonfigurowano z usługi Azure Traffic Manager. Będziesz mieć możliwość dostępu testu adresy URL z domeny niestandardowej. Aby przetestować funkcje, wyłącz podstawowy punkt końcowy CDN i sprawdź, czy żądanie jest poprawnie przenoszony do dodatkowej punktu końcowego CDN. 

## <a name="next-steps"></a>Kolejne kroki
Możesz także skonfigurować inne metody routingu, takich jak geograficzne, w celu zrównoważenia obciążenia między różnych punktów końcowych usługi CDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie metody routingu ruchu geograficzny przy użyciu Menedżera ruchu](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



