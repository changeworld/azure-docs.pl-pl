---
title: Ograniczenia adresów IP klienta - usługi Azure App Service | Dokumentacja firmy Microsoft
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853281"
---
# <a name="azure-app-service-static-access-restrictions"></a>Ograniczenia dostępu statycznych w usłudze Azure App Service #

Ograniczenia dostępu umożliwiają definiowanie priorytetu, uporządkowane zezwalania/niezezwalania na listę adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać adresów IPv4 i IPv6. W przypadku co najmniej jeden wpis istnieje, a następnie niejawna odmowa, wszystkie, znajdującą się na końcu listy.

Możliwość ograniczenia dostępu działa w przypadku wszystkich obciążeń pracą, które obejmują; hostowana usługa aplikacji aplikacje sieci Web, aplikacje interfejsu API, aplikacje systemu Linux, aplikacji kontenera systemu Linux i funkcje.

Po wysłaniu żądania do aplikacji, adres IP z zostaną ocenione względem listy ograniczeń dostępu. Jeśli adres nie jest dozwolony dostęp na podstawie reguł na liście, w odpowiedzi usługa [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) kod stanu.

Możliwość ograniczenia dostępu jest zaimplementowana w ról frontonu usługi App Service, które są nadrzędne hostów procesu roboczego, w której kod jest wykonywany. W związku z tym ograniczenia dostępu są faktycznymi list ACL w sieci.  

![Przepływ ograniczeń dostępu](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Czas możliwość ograniczenia dostępu w portalu była warstwy na podstawie możliwości ipSecurity w usługach IIS. Bieżąca możliwość ograniczenia dostępu jest inny. IpSecurity nadal można skonfigurować w ramach Twojego pliku web.config aplikacji, ale frontonu zasady ograniczeń dostępu na podstawie zostaną zastosowane, zanim cały ruch osiągnie usług IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Dodawanie i edytowanie reguły ograniczenie dostępu w portalu ##

Aby dodać reguły ograniczenie dostępu do aplikacji, użyj menu, aby otworzyć **sieci**>**ograniczenia dostępu** i kliknij pozycję **Konfigurowanie ograniczeń dostępu**

![Opcje sieciowe w usłudze App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

Z poziomu interfejsu użytkownika ograniczenia dostępu możesz przejrzeć listę reguł ograniczenia dostępu zdefiniowane dla twojej aplikacji.

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Jeśli Twoje reguły zostały skonfigurowane tak jak ten obraz, aplikacja będzie akceptować tylko ruch z 131.107.159.0/24 i odrzucone z dowolnego adresu IP.

Możesz kliknąć **[+] Dodaj** można dodać nowej reguły ograniczeń dostępu. Jeśli dodasz regułę, jego zostanie zaczynają obowiązywać natychmiast. Zasady są wymuszane w kolejności priorytetów, począwszy od najniższej liczbie i przedsiębiorstw. Istnieje niejawna odmowa wszystko, co jest aktywna, po dodaniu jednej reguły.

![Dodaj regułę ograniczeń dostępu](media/app-service-ip-restrictions/ip-restrictions-add.png)

Notacja adres IP musi być określona w notacji CIDR dla adresów IPv4 i IPv6. Aby określić dokładny adres, można użyć podobny 1.2.3.4/32 gdzie pierwsze cztery oktety reprezentują adresu IP, a /32 to maski. Notacja IPv4 CIDR dla wszystkich adresów jest 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, możesz przeczytać [Bezklasowego routingu międzydomenowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kliknięcie dowolnego wiersza, aby edytować istniejącą regułę ograniczeń dostępu. Zmiany zaczynają obowiązywać, natychmiast łącznie ze zmianami w kolejności priorytetu.

![Edytuj zasady ograniczeń dostępu](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Aby usunąć regułę, kliknij przycisk **...**  na swoje zasady, a następnie kliknij **Usuń**.

![Usuń regułę ograniczeń dostępu](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Można również ograniczyć dostęp do wdrożenia, w następnej karcie. Do Dodawanie/edytowanie/usuwanie każdej reguły, postępuj zgodnie z tego samego kroku opisanych powyżej.

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programowe operowanie zasady ograniczeń dostępu ##

Obecnie nie ma interfejsu wiersza polecenia lub programu PowerShell dla nowych możliwości ograniczenia dostępu, ale można ustawić wartości ręcznie za pomocą operacji PUT konfiguracji aplikacji w usłudze Resource Manager. Na przykład można użyć resources.azure.com i edycję bloku ipSecurityRestrictions, aby dodać wymagane za pomocą pliku JSON.

Lokalizacja tych informacji w usłudze Resource Manager to:

Management.Azure.com/subscriptions/**identyfikator subskrypcji**/resourceGroups/**grup zasobów**/providers/Microsoft.Web/sites/**Nazwa aplikacji sieci web**  /config/sieci web? Interfejs API-version = 2018-02-01

Jest ze składnią pliku JSON dla poprzedniego przykładu:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>Ograniczenia adresów IP aplikacji — funkcja

Ograniczenia adresów IP są dostępne dla obu aplikacji funkcji z taką samą funkcjonalność jak planów usługi App Service. Należy pamiętać, że włączenie IP ograniczenia spowoduje wyłączenie edytora kodu portalu żadnych niedozwolonych adresów IP.

[Dowiedz się więcej tutaj](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)