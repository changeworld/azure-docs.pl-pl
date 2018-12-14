---
title: Ograniczenia adresów IP klienta - usługi Azure App Service | Dokumentacja firmy Microsoft
description: Sposób użycia ograniczenia adresów IP w usłudze Azure App Service
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
ms.openlocfilehash: a152efb3979b4ffe3402ed668c0f683f5e9cc651
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337159"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Ograniczenia statyczny adres IP w usłudze Azure App Service #

Ograniczenia adresów IP umożliwiają definiowanie priorytetu, uporządkowane zezwalania/niezezwalania na listę adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać adresów IPv4 i IPv6. W przypadku co najmniej jeden wpis istnieje, a następnie niejawna odmowa, wszystkie, znajdującą się na końcu listy. 

Funkcja ograniczenia adresów IP działa w przypadku wszystkich obciążeń pracą, które obejmują; hostowana usługa aplikacji aplikacje sieci Web, aplikacje interfejsu api, aplikacje systemu linux, aplikacji kontenera systemu linux i funkcje. 

Po wysłaniu żądania do aplikacji, adres IP z zostaną ocenione względem listy ograniczenia adresów IP. Jeśli adres nie jest dozwolony dostęp na podstawie reguł na liście, w odpowiedzi usługa [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) kod stanu.

Funkcja ograniczenia adresów IP jest zaimplementowana w ról frontonu usługi App Service, które są nadrzędne hostów procesu roboczego, w której kod jest wykonywany. Dlatego skutecznie list ACL sieci są ograniczenia adresów IP.  

![Ograniczenia adresów IP przepływu](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Czas możliwości ograniczenia adresów IP w portalu była warstwy na podstawie możliwości ipSecurity w usługach IIS. Bieżąca funkcja ograniczenia adresów IP jest inny. IpSecurity nadal można skonfigurować w ramach Twojego pliku web.config aplikacji, ale frontonu na podstawie reguł ograniczenia adresów IP zostaną zastosowane, zanim cały ruch osiągnie usług IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Dodawanie i edytowanie reguł ograniczenia adresów IP w portalu ##

Dodaj regułę ograniczeń adresów IP do swojej aplikacji, należy użyć, aby otworzyć menu **sieci**>**ograniczenia adresów IP** i kliknij pozycję **skonfigurować ograniczenia adresów IP**

![Opcje sieciowe w usłudze App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

Z poziomu interfejsu użytkownika ograniczenia adresów IP można przejrzeć listę reguł ograniczeń adresów IP zdefiniowane dla twojej aplikacji.

![ograniczenia adresów IP z listy](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Jeśli Twoje reguły zostały skonfigurowane tak jak ten obraz, aplikacja będzie akceptować tylko ruch z 131.107.159.0/24 i odrzucone z dowolnego adresu IP.

Możesz kliknąć **[+] Dodaj** można dodać nowej reguły ograniczeń adresów IP. Jeśli dodasz regułę, jego zostanie zaczynają obowiązywać natychmiast. Zasady są wymuszane w kolejności priorytetów, począwszy od najniższej liczbie i przedsiębiorstw. Istnieje niejawna odmowa wszystko, co jest aktywna, po dodaniu jednej reguły. 

![Dodaj regułę ograniczeń adresów IP](media/app-service-ip-restrictions/ip-restrictions-add.png)

Notacja adres IP musi być określona w notacji CIDR dla adresów IPv4 i IPv6. Aby określić dokładny adres, można użyć podobny 1.2.3.4/32 gdzie pierwsze cztery oktety reprezentują adresu IP, a /32 to maski. Notacja IPv4 CIDR dla wszystkich adresów jest 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, możesz przeczytać [Bezklasowego routingu międzydomenowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Kliknięcie dowolnego wiersza, aby edytować istniejącą regułę ograniczeń adresów IP. Zmiany zaczynają obowiązywać, natychmiast łącznie ze zmianami w kolejności priorytetu.

![Edytuj regułę ograniczeń adresów IP](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Aby usunąć regułę, kliknij przycisk **...**  na swoje zasady, a następnie kliknij **Usuń**. 

![Usuń regułę ograniczeń adresów IP](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Programowe operowanie zasady ograniczeń adresów IP ##

Obecnie nie ma interfejsu wiersza polecenia lub programu PowerShell dla nowych możliwości ograniczenia adresów IP, ale można ustawić wartości ręcznie za pomocą operacji PUT konfiguracji aplikacji w usłudze Resource Manager. Na przykład można użyć resources.azure.com i edycję bloku ipSecurityRestrictions, aby dodać wymagane za pomocą pliku JSON. 

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
