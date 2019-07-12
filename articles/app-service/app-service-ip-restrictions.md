---
title: Ograniczanie dostępu — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Sposób ograniczenia dostępu za pomocą usługi Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d3c547fbc09aeb034df5b7ed579639e1ff4bc0b4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705801"
---
# <a name="azure-app-service-access-restrictions"></a>Ograniczenia dostępu do usługi Azure App Service #

Ograniczenia dostępu umożliwiają definiowanie priorytetu uporządkowane zezwalania/niezezwalania listę kontroli dostępu do sieci do swojej aplikacji. Lista może zawierać adresy IP lub podsieci sieci wirtualnej platformy Azure. W przypadku co najmniej jeden wpis jest następnie niejawny "odmowa wszystkie", które występuje na końcu listy.

Możliwość ograniczenia dostępu do projektów w programach wszystkie usługi App Service hostowanej pracy ładuje, w tym; aplikacje sieci Web, aplikacje interfejsu API, aplikacje systemu Linux, aplikacji kontenera systemu Linux i funkcje.

Po wysłaniu żądania do aplikacji, adres nadawcy zostaną ocenione względem zasady adres IP na liście ograniczeń dostępu. Jeśli adres nadawcy znajduje się w podsieci, który jest skonfigurowany z punktów końcowych usługi dla Microsoft.Web, następnie podsieć źródłowa jest porównywana reguł sieci wirtualnej, na liście ograniczeń dostępu. Jeśli adres nie jest dozwolony dostęp na podstawie reguł na liście, w odpowiedzi usługa [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) kod stanu.

Możliwość ograniczenia dostępu jest zaimplementowana w ról frontonu usługi App Service, które są nadrzędne hostów procesu roboczego, w której kod jest wykonywany. W związku z tym ograniczenia dostępu są faktycznymi list ACL w sieci.

Możliwość ograniczenia dostępu do aplikacji sieci web z usługą Azure Virtual Network (VNet) jest nazywany [punkty końcowe usługi][serviceendpoints]. Punkty końcowe usługi pozwalają ograniczyć dostęp do usługi wielodostępne z wybranej podsieci. Musi być włączona na zarówno sieci po stronie, a także usługa, która jest włączane przy użyciu. Nie działa do ograniczania ruchu do aplikacji, które są hostowane w środowisku usługi App Service.  Jeśli jesteś w środowisku usługi App Service umożliwia kontrolę dostępu do aplikacji przy użyciu reguły dotyczące adresów IP.

![Przepływ ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Dodawanie i edytowanie reguły ograniczenie dostępu w portalu ##

Aby dodać reguły ograniczenie dostępu do aplikacji, użyj menu, aby otworzyć **sieci**>**ograniczenia dostępu** i kliknij pozycję **Konfigurowanie ograniczeń dostępu**

![Opcje sieciowe w usłudze App Service](media/app-service-ip-restrictions/access-restrictions.png)  

Z poziomu interfejsu użytkownika ograniczenia dostępu możesz przejrzeć listę reguł ograniczenia dostępu zdefiniowane dla twojej aplikacji.

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/access-restrictions-browse.png)

Na liście zostaną wyświetlone wszystkie bieżące ograniczenia, które znajdują się w aplikacji. Jeśli masz ograniczeń sieci wirtualnej w swojej aplikacji, tabeli zostaną wyświetlone, jeśli punkty końcowe usługi są włączone dla Microsoft.Web. Jeśli nie ma ograniczeń zdefiniowanych w aplikacji, aplikacja nie będzie dostępny z dowolnego miejsca.  

## <a name="adding-ip-address-rules"></a>Dodawanie reguły dotyczące adresów IP

Możesz kliknąć **[+] Dodaj** można dodać nowej reguły ograniczeń dostępu. Jeśli dodasz regułę, jego zostanie zaczynają obowiązywać natychmiast. Zasady są wymuszane w kolejności priorytetów, począwszy od najniższej liczbie i przedsiębiorstw. Istnieje niejawna odmowa wszystko, co jest aktywna, po dodaniu jednej reguły.

Podczas tworzenia reguły należy wybrać zezwalania/niezezwalania, a także typ reguły. Wymagane są także zapewnienie, że wartość priorytetu i co to jest ograniczenie dostępu do.  Możesz opcjonalnie dodać nazwę i opis reguły.  

![Dodaj regułę ograniczeń dostępu do adresów IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Aby ustawić adres IP na podstawie reguły, wybierz typ protokołu IPv4 lub IPv6. Notacja adres IP musi być określona w notacji CIDR dla adresów IPv4 i IPv6. Aby określić dokładny adres, można użyć podobny 1.2.3.4/32 gdzie pierwsze cztery oktety reprezentują adresu IP, a /32 to maski. Notacja IPv4 CIDR dla wszystkich adresów jest 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, możesz przeczytać [Bezklasowego routingu międzydomenowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi można ograniczyć dostęp do podsieci wybranej sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do określonej podsieci, należy utworzyć regułę ograniczenie z typem sieci wirtualnej. Możesz wybrać subskrypcję, sieć wirtualną i podsieć, którą chcesz udzielić lub odmówić dostępu za pomocą. Jeśli punkty końcowe usługi nie są już włączone za pomocą Microsoft.Web dla podsieci, które wybrano, jego zostaną automatycznie włączone dla Ciebie, jeśli nie zaznaczysz pole zapytaniem w tym celu. Sytuacja, w którym chcesz ją włączyć w aplikacji, ale nie podsieci jest powiązana z dużym stopniu, jeśli masz uprawnienia, aby włączyć punkty końcowe usługi w podsieci, czy nie. Jeśli potrzebujesz uzyskać osoby, aby włączyć punkty końcowe usługi w podsieci można zaznacz pole wyboru i aplikacji skonfigurowany dla punktów końcowych usługi w oczekiwaniu go później włączane w podsieci. 

![Dodaj regułę ograniczeń dostępu do sieci wirtualnej](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Punkty końcowe usługi nie można ograniczyć dostęp do aplikacji działających w środowisku usługi App Service. Jeśli aplikacja znajduje się w środowisku usługi App Service, umożliwia kontrolę dostępu do aplikacji przy użyciu reguły dostępu do adresów IP. 

Punkty końcowe usługi można skonfigurować swoją aplikację przy użyciu bramy aplikacji lub innych urządzeń zapory aplikacji sieci Web. Można również skonfigurować aplikacje wielowarstwowe z bezpiecznego zaplecza. Aby uzyskać szczegółowe informacje na temat niektórych możliwości, przeczytaj [funkcje sieci i usługi App Service](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Zarządzanie regułami ograniczeń dostępu

Kliknięcie dowolnego wiersza, aby edytować istniejącą regułę ograniczeń dostępu. Zmiany zaczynają obowiązywać, natychmiast łącznie ze zmianami w kolejności priorytetu.

![Edytuj zasady ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Podczas edytowania reguły nie można zmienić typu między reguła adresów IP i reguły sieci wirtualnej. 

![Edytuj zasady ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Aby usunąć regułę, kliknij przycisk **...**  na swoje zasady, a następnie kliknij **Usuń**.

![Usuń regułę ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blokowanie pojedynczego adresu IP ##

Podczas dodawania pierwszego reguły ograniczenia adresów IP, usługa zostanie dodany jawnego **Odmawiaj wszystkim** reguła z priorytetem 2147483647. W praktyce jawne **Odmawiaj wszystkim** reguła będzie ostatnia reguła wykonywane i spowoduje zablokowanie dostępu do dowolnego adresu IP, który nie jest jawnie dozwolone przy użyciu **Zezwalaj** reguły.

W scenariuszu, gdzie użytkownicy chcą jawnie blokować pojedynczego adresu IP lub blok adresów IP, ale zezwalaj na wszystko, czego jeszcze dostępu, należy go dodać jawne **Zezwalaj na wszystko** reguły.

![adres ip jednego bloku](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Witryny SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji, można również ograniczyć dostęp do witryny scm, używanych przez aplikację. Witryny scm jest, narzędzie web deploy punktu końcowego, a także do konsoli Kudu. Oddzielnie można przypisać ograniczenia dostępu do witryny scm z aplikacji lub używać tego samego zestawu dla witryny scm i aplikacji. Jeśli zaznaczysz to pole, aby te same ograniczenia co aplikacja, wszystko jest wygaszony. Jeśli usuniesz zaznaczenie pola wyboru pola są stosowane niezależnie od ustawienia była wcześniej w witryny scm. 

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programowe operowanie zasady ograniczeń dostępu ##

Obecnie nie ma interfejsu wiersza polecenia lub programu PowerShell dla nowych możliwości ograniczenia dostępu, ale można ustawić wartości ręcznie za pomocą [interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure/) operacji PUT konfiguracji aplikacji w usłudze Resource Manager. Na przykład można użyć resources.azure.com i edycję bloku ipSecurityRestrictions, aby dodać wymagane za pomocą pliku JSON.

Lokalizacja tych informacji w usłudze Resource Manager to:

Management.Azure.com/subscriptions/**identyfikator subskrypcji**/resourceGroups/**grup zasobów**/providers/Microsoft.Web/sites/**Nazwa aplikacji sieci web**  /config/sieci web? Interfejs API-version = 2018-02-01

Jest ze składnią pliku JSON dla poprzedniego przykładu:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Ograniczenia adresów IP aplikacji — funkcja

Ograniczenia adresów IP są dostępne dla obu aplikacji funkcji z taką samą funkcjonalność jak planów usługi App Service. Włączenie ograniczeń adresów IP spowoduje wyłączenie edytora kodu portalu żadnych niedozwolonych adresów IP.

[Dowiedz się więcej tutaj](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
