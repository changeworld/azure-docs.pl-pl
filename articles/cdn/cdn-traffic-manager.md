---
title: Przewijanie w stan failover w wielu punktach końcowych usługi Azure CDN za pomocą usługi Traffic Manager
description: Dowiedz się, jak skonfigurować usługę Azure Traffic Manager za pomocą punktów końcowych usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083005"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurowanie pracy awaryjnej w wielu punktach końcowych usługi Azure CDN za pomocą usługi Azure Traffic Manager

Podczas konfigurowania usługi Azure Content Delivery Network (CDN), można wybrać optymalną warstwę dostawcy i cen dla swoich potrzeb. Usługa Azure CDN, z infrastrukturą globalnie rozproszoną, domyślnie tworzy nadmiarowość lokalną i geograficzną oraz globalne równoważenie obciążenia w celu zwiększenia dostępności i wydajności usług. Jeśli lokalizacja nie jest dostępna do obsługi zawartości, żądania są automatycznie kierowane do innej lokalizacji, a optymalny pop (na podstawie takich czynników, jak lokalizacja żądania i obciążenie serwera) jest używany do obsługi każdego żądania klienta. 
 
Jeśli masz wiele profilów usługi CDN, możesz dodatkowo zwiększyć dostępność i wydajność za pomocą usługi Azure Traffic Manager. Usługi Azure Traffic Manager z usługą Azure CDN można używać do równoważenia obciążenia między wieloma punktami końcowymi usługi CDN w trybie failover, równoważenia obciążenia geograficznego i innych scenariuszy. W typowym scenariuszu pracy awaryjnej wszystkie żądania klientów są najpierw kierowane do podstawowego profilu sieci CDN; Jeśli profil nie jest dostępny, żądania są przekazywane do pomocniczego profilu sieci CDN, dopóki podstawowy profil sieci CDN nie zostanie z powrotem w trybie online. Korzystanie z usługi Azure Traffic Manager w ten sposób zapewnia, że aplikacja sieci web jest zawsze dostępna. 

Ten artykuł zawiera wskazówki i przykład konfigurowania pracy awaryjnej za pomocą **usługi Azure CDN Standard firmy Verizon** i **standardu Azure CDN standard z profilów Akamai.**

## <a name="set-up-azure-cdn"></a>Konfigurowanie usługi Azure CDN 
Utwórz dwa lub więcej profili i punktów końcowych usługi Azure CDN z różnymi dostawcami.

1. Utwórz **standard usługi Azure CDN z poziomu usługi Verizon** i Azure CDN standard z profilu **Akamai,** wykonując kroki opisane w [obszarze Tworzenie nowego profilu sieci CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Wiele profili cdn](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. W każdym z nowych profili utwórz co najmniej jeden punkt końcowy, wykonując kroki opisane w [programie Utwórz nowy punkt końcowy sieci CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurowanie usługi Azure Traffic Manager
Utwórz profil usługi Azure Traffic Manager i skonfiguruj równoważenie obciążenia w punktach końcowych usługi CDN. 

1. Utwórz profil usługi Azure Traffic Manager, wykonując kroki opisane w [obszarze Tworzenie profilu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    W przypadku **metody Routing**wybierz **pozycję Priorytet**.

2. Dodawanie punktów końcowych sieci CDN do profilu usługi Traffic Manager, wykonując kroki opisane w [programie Dodawanie punktów końcowych usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    W obszarze **Typ**wybierz **pozycję Zewnętrzne punkty końcowe**. W **ciemięgosi**wprowadź liczbę.

    Na przykład utwórz *cdndemo101akamai.azureedge.net* o priorytecie *1* i *cdndemo101verizon.azureedge.net* z priorytetem *2*.

   ![Punkty końcowe menedżera ruchu CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurowanie domeny niestandardowej w usłudze Azure CDN i usłudze Azure Traffic Manager
Po skonfigurowaniu profilów sieci CDN i usługi Traffic Manager wykonaj następujące kroki, aby dodać mapowanie DNS i zarejestrować domenę niestandardową w punktach końcowych usługi CDN. W tym przykładzie niestandardowa nazwa domeny to *cdndemo101.dustydogpetcare.online*.

1. Przejdź do witryny sieci Web dostawcy domeny niestandardowej, takiej jak GoDaddy, i utwórz dwa wpisy CNAME DNS. 

    a. W przypadku pierwszego wpisu CNAME zamapuj domenę niestandardową z poddomeną cdnverify na punkt końcowy sieci CDN. Ten wpis jest krokiem wymaganym do zarejestrowania domeny niestandardowej w punkcie końcowym usługi CDN dodanym do usługi Traffic Manager w kroku 2.

      Przykład: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. W przypadku drugiego wpisu CNAME mapuj domenę niestandardową bez poddomeny cdnverify do punktu końcowego sieci CDN. Ten wpis mapuje domenę niestandardową na Menedżera ruchu. 

      Przykład: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Jeśli domena jest aktualnie przeżywana i nie można jej przerwać, wykonaj ten krok jako ostatni. Przed zaktualizowaniem usługi DNS domeny niestandardowej do usługi Traffic Manager sprawdź, czy punkty końcowe usługi CDN i domeny menedżera ruchu są aktywne.
    >


2.  Z profilu usługi Azure CDN wybierz pierwszy punkt końcowy usługi CDN (Akamai). Wybierz **pozycję Dodaj domenę niestandardową** i wejście *cdndemo101.dustydogpetcare.online*. Sprawdź, czy znacznik wyboru sprawdzania poprawności domeny niestandardowej jest zielony. 

    Usługa Azure CDN używa poddomeny *cdnverify* do sprawdzania poprawności mapowania DNS w celu ukończenia tego procesu rejestracji. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ten krok umożliwia usługi Azure CDN rozpoznać domeny niestandardowej, dzięki czemu może odpowiadać na jego żądania.
    
    > [!NOTE]
    > Aby włączyć SSL w **usłudze Azure CDN z profilów Akamai,** należy bezpośrednio cname domeny niestandardowej do punktu końcowego. cdnverify dla włączania SSL nie jest jeszcze obsługiwany. 
    >

3.  Wróć do witryny sieci Web dostawcy domeny domeny niestandardowej i zaktualizuj pierwsze mapowanie DNS utworzone w taki sposób, aby domena niestandardowa była mapowana na drugi punkt końcowy sieci CDN.
                             
    Przykład: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Z profilu usługi Azure CDN wybierz drugi punkt końcowy usługi CDN (Verizon) i powtórz krok 2. Wybierz **pozycję Dodaj domenę niestandardową**i wejście *cdndemo101.dustydogpetcare.online*.
 
Po wykonaniu tych kroków usługa multi-CDN z możliwościami pracy awaryjnej jest skonfigurowana za pomocą usługi Azure Traffic Manager. Będziesz mieć dostęp do testowych adresów URL z domeny niestandardowej. Aby przetestować funkcjonalność, należy wyłączyć podstawowy punkt końcowy sieci CDN i sprawdzić, czy żądanie jest poprawnie przeniesione do pomocniczego punktu końcowego sieci CDN. 

## <a name="next-steps"></a>Następne kroki
Można również skonfigurować inne metody routingu, takie jak geograficzne, aby zrównoważyć obciążenie między różnymi punktami końcowymi sieci CDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie metody routingu ruchu geograficznego przy użyciu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



