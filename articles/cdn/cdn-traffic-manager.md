---
title: Konfigurowanie trybu failover między wieloma punktami końcowymi usługi CDN Azure przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o konfigurowaniu usługi Azure Traffic Manager z punktami końcowymi usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: f7de7fe1c677d54c0fa3e6d3ca4730ef1083bc81
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273276"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurowanie trybu failover między wieloma punktami końcowymi usługi CDN Azure przy użyciu usługi Azure Traffic Manager

Podczas konfigurowania usługi Azure Content Delivery Network (CDN), można wybrać optymalne dostawcy i warstwę cenową dla Twoich potrzeb. Usługa Azure CDN, za pomocą jego globalnie rozproszonej infrastruktury domyślnie tworzy lokalne i geograficznej nadmiarowości i globalne równoważenia do poprawy wydajności i dostępności usługi. Jeśli lokalizacja nie jest dostępna do obsługi zawartości, żądania są automatycznie kierowane do innej lokalizacji i optymalne punktu obecności (oparte na takie czynniki jak żądanie lokalizacji i obciążenia serwera) jest używany do obsługi każdego żądania klienta. 
 
Jeśli masz wiele profilów CDN, dodatkowo zwiększyć dostępność i wydajność za pomocą usługi Azure Traffic Manager. Za pomocą usługi Azure Traffic Manager i usługi Azure CDN można załadować saldo między wieloma punktami końcowymi usługi CDN dla trybu failover, geograficznie obciążenia równoważenia i innych scenariuszy. W przypadku typowego pracy awaryjnej wszystkie żądania klientów są najpierw kierowane do podstawowego profil CDN; profil, który nie jest dostępny, żądania są następnie przekazywane do dodatkowej profilu usługi CDN do momentu powrotu do trybu online głównej profilu CDN. W ten sposób za pomocą usługi Azure Traffic Manager gwarantuje, że aplikacja sieci web jest zawsze dostępna. 

Ten artykuł zawiera wskazówki i przykład sposobu konfigurowania trybu failover z **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów.

## <a name="set-up-azure-cdn"></a>Konfigurowanie usługi Azure CDN 
Utwórz co najmniej dwóch profilów Azure CDN, jak i punktów końcowych z różnych dostawców.

1. Utwórz **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilu, wykonując kroki opisane w [Utwórz nowy profil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Sieci CDN wiele profilów](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. We wszystkich nowych profilów, Utwórz co najmniej jeden punkt końcowy, wykonując kroki opisane w [utworzyć nowy punkt końcowy CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Ustawianie usługi Azure Traffic Manager
Tworzenie profilu usługi Azure Traffic Manager i skonfigurować Równoważenie obciążenia w punktach końcowych sieci CDN. 

1. Tworzenie profilu usługi Azure Traffic Manager, wykonując kroki opisane w [Tworzenie profilu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Aby uzyskać **Metoda routingu**, wybierz opcję **priorytet**.

2. Dodawanie punktów końcowych sieci CDN w profilu usługi Traffic Manager, wykonując kroki opisane w [punkty końcowe usługi Traffic Manager Dodaj](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Aby uzyskać **typu**, wybierz opcję **zewnętrzne punkty końcowe**. Aby uzyskać **priorytet**, wprowadź liczbę.

    Na przykład można utworzyć *cdndemo101akamai.azureedge.net* z priorytetem *1* i *cdndemo101verizon.azureedge.net* z priorytetem *2*.

   ![Punktów końcowych usługi CDN traffic manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurowanie domeny niestandardowej na sieć CDN systemu Azure i usługi Azure Traffic Manager
Po skonfigurowaniu profilów usługi Traffic Manager i CDN, wykonaj następujące kroki, aby dodać mapowanie DNS i zarejestruj domeny niestandardowej do punktów końcowych usługi CDN. W tym przykładzie nazwa domeny niestandardowej jest *cdndemo101.dustydogpetcare.online*.

1. Przejdź do witryny internetowej dostawcy domeny w domenie niestandardowej, np. GoDaddy i Utwórz dwa wpisy CNAME w systemie DNS. 

    a. Pierwszy wpis CNAME mapowanie domeny niestandardowej, z poziomu poddomeny cdnverify do punktu końcowego usługi CDN. Ten wpis jest to krok wymagany do zarejestrowania domeny niestandardowej do punktu końcowego usługi CDN, dodanego do usługi Traffic Manager w kroku 2.

      Na przykład: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Drugiego wpisu CNAME mapować domenę niestandardową, bez domeny podrzędnej cdnverify, do punktu końcowego usługi CDN. Ten wpis mapuje domenę niestandardową do usługi Traffic Manager. 

      Na przykład: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Jeśli domeny jest obecnie aktywna i nie można przerwać, ostatnia wykonaj ten krok. Sprawdź, czy punkty końcowe sieci CDN i domen Menedżera ruchu na żywo przed zaktualizowaniem niestandardowej domeny DNS do usługi Traffic Manager.
    >


2.  Z profilem w usłudze Azure CDN wybierz pierwszy punkt końcowy usługi CDN (firmy Akamai). Wybierz **Dodaj domenę niestandardową** i wejściowego *cdndemo101.dustydogpetcare.online*. Sprawdź, czy zielony znacznik wyboru, aby zweryfikować domenę niestandardową. 

    Usługa Azure CDN używa *cdnverify* poddomeny, aby sprawdzić poprawność mapowania DNS, aby ukończyć ten proces rejestracji. Aby uzyskać więcej informacji, zobacz [tworzenie rekordu CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Ten krok powoduje włączenie usługi Azure CDN, dzięki czemu mogą odpowiadać na żądania jego, rozpoznawał domeny niestandardowej.
    
    > [!NOTE]
    > Aby włączyć protokół SSL na **Azure CDN from Akamai** profile, należy bezpośrednio rekordu cname domeny niestandardowej do punktu końcowego usługi. cdnverify do włączenia protokołu SSL nie jest jeszcze obsługiwana. 
    >

3.  Wróć do witryny sieci web dostawcy domeny niestandardowej domeny i zaktualizuj mapowanie DNS pierwszego, utworzony w tak, aby domena niestandardowa została zamapowana do drugiego punktu końcowego usługi CDN.
                             
    Na przykład: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Z profilem w usłudze Azure CDN Wybierz drugi punkt końcowy usługi CDN (Verizon), a następnie powtórz krok 2. Wybierz **Dodaj domenę niestandardową**i wejściowego *cdndemo101.dustydogpetcare.online*.
 
Po wykonaniu tych kroków, usługi CDN wielu, możliwości trybu failover jest skonfigurowany przy użyciu usługi Azure Traffic Manager. Będziesz mieć możliwość dostępu testu adresy URL z domeny niestandardowej. Aby przetestować funkcję, wyłącz podstawowego punktu końcowego usługi CDN i sprawdź, czy żądanie jest poprawnie przeniesieni do pomocniczego punktu końcowego usługi CDN. 

## <a name="next-steps"></a>Kolejne kroki
Możesz także skonfigurować inne metody routingu, takich jak geograficzne, aby równoważyć obciążenie między różnych punktów końcowych usługi CDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie metody routingu geograficznego ruchu przy użyciu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



