---
title: Lokalna brama danych dla usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Brama lokalna jest niezbędna, jeśli serwer usług Analysis Services na platformie Azure połączy się z lokalnymi źródłami danych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310156"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Łączenie się z lokalnymi źródłami danych za pomocą lokalnej bramy danych

Lokalna brama danych zapewnia bezpieczny transfer danych między lokalnymi źródłami danych a serwerami usług Azure Analysis Services w chmurze. Oprócz pracy z wieloma serwerami usług Azure Analysis Services w tym samym regionie najnowsza wersja bramy współpracuje również z aplikacjami Azure Logic Apps, Power BI, Power Apps i Power Automate. Podczas gdy instalowane bramy jest taka sama we wszystkich tych usługach, usługi Azure Analysis Services i aplikacje logiki mają kilka dodatkowych kroków.

Informacje podane w tym miejscu są specyficzne dla sposobu, w jaki usługi Azure Analysis Services współpracują z lokalną bramą danych. Aby dowiedzieć się więcej o bramie w ogóle i jak działa z innymi usługami, zobacz Co to [jest lokalna brama danych?](/data-integration/gateway/service-gateway-onprem).

W przypadku usług Azure Analysis Services pierwsze konfigurowanie bramy jest procesem czteroczęściowym:

- **Pobierz i uruchom konfigurację** — ten krok instaluje usługę bramy na komputerze w organizacji. Zaloguj się również na platformie Azure przy użyciu konta w usłudze Azure AD [dzierżawy.](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Konta usługi Azure B2B (gość) nie są obsługiwane.

- **Zarejestruj bramę** — w tym kroku należy określić nazwę i klucz odzyskiwania bramy i wybrać region, rejestrując bramę za pomocą usługi gateway Cloud Service. Zasób bramy można zarejestrować w dowolnym regionie, ale zaleca się, aby znajdować się w tym samym regionie co serwery usług Analysis Services. 

- **Utwórz zasób bramy na platformie Azure** — w tym kroku utworzysz zasób bramy na platformie Azure.

- **Podłącz serwery do zasobu bramy** — po uzyskaniu zasobu bramy można rozpocząć łączenie serwerów z nim. Można połączyć wiele serwerów i innych zasobów, pod warunkiem, że znajdują się one w tym samym regionie.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Jak to działa
Brama instalana na komputerze w organizacji działa jako usługa systemu **Windows, lokalna brama danych**. Ta usługa lokalna jest rejestrowana w usłudze w chmurze dla bramy za pośrednictwem usługi Azure Service Bus. Następnie należy utworzyć zasób lokalnej bramy danych dla subskrypcji platformy Azure. Serwery usług Azure Analysis Services są następnie połączone z zasobem bramy platformy Azure. Gdy modele na serwerze muszą łączyć się z lokalnymi źródłami danych dla zapytań lub przetwarzania, kwerenda i przepływ danych przechodzi przez zasób bramy, usługę Azure Service Bus, lokalną usługę bramy danych lokalnych i źródła danych. 

![Jak to działa](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Zapytania i przepływ danych:

1. Zapytanie jest tworzone przez usługę w chmurze przy użyciu zaszyfrowanych poświadczeń dla lokalnego źródła danych. Następnie jest wysyłane do kolejki dla bramy do przetworzenia.
2. Usługa w chmurze bramy analizuje kwerendę i wypycha żądanie do [usługi Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Brama danych lokalnych sonduje usługę Azure Service Bus pod kątem żądań oczekujących.
4. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłami danych, używając tych poświadczeń.
5. Brama wysyła zapytanie do źródła danych w celu wykonania.
6. Wyniki są odsyłane ze źródła danych do bramy, z powrotem do bramy, a następnie do usługi w chmurze i na serwer.

## <a name="installing"></a>Instalowanie programu

Podczas instalowania w środowisku usług Azure Analysis Services należy wykonać kroki opisane w [temacie Instalowanie i konfigurowanie lokalnej bramy danych dla usług Azure Analysis Services.](analysis-services-gateway-install.md) Ten artykuł jest specyficzny dla usługi Azure Analysis Services. Zawiera dodatkowe kroki wymagane do skonfigurowania lokalnego zasobu bramy danych na platformie Azure i połączenia serwera usług Azure Analysis Services z zasobem.

## <a name="ports-and-communication-settings"></a>Porty i ustawienia komunikacji

Brama tworzy połączenie wychodzące do usługi Azure Service Bus. Komunikuje się ona na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 do 9354.  Brama nie wymaga portów przychodzących.

Może być konieczne uwzględnienie adresów IP regionu danych w zaporze. Możesz pobrać [listę adresów IP centrów danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). Ta lista jest aktualizowana co tydzień. Na liście adresów IP centrów danych platformy Azure adresy są wymienione w notacji CIDR. Aby dowiedzieć się więcej, zobacz [Bezklasowe routing między domenami](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Poniżej przedstawiono w pełni kwalifikowane nazwy domen używane przez bramę.

| Nazwy domen | Porty wychodzące | Opis |
| --- | --- | --- |
| *.powerbi.com |80 |Protokół HTTP używany do pobierania instalatora. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Zaawansowane usługi kolejkowania Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Odbiorniki usługi Service Bus Relay korzystające z protokołu TCP (wymaga portu 443 w celu uzyskania tokenu kontroli dostępu) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Służy do testowania łączności z Internetem, gdy brama jest nieosiągalna przez usługę Power BI. |
| *.microsoftonline-p.com |443 |Używany do uwierzytelniania w zależności od konfiguracji. |
| dc.services.visualstudio.com  |443 |Używane przez AppInsights do zbierania danych telemetrycznych. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>Wymuszanie komunikacji HTTPS z usługą Azure Service Bus

Można wymusić bramy do komunikowania się z usługi Azure Service Bus przy użyciu protokołu HTTPS zamiast bezpośredniego protokołu TCP; jednak w ten sposób może znacznie zmniejszyć wydajność. Plik *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* można zmodyfikować, zmieniając `AutoDetect` `Https`wartość z . Ten plik zazwyczaj znajduje się w *c:\Program Files\Lokalna brama danych*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Następne kroki 

Następujące artykuły są zawarte w lokalnej zawartości ogólnej bramy danych, która ma zastosowanie do wszystkich usług, które obsługuje brama:

* [Lokalna brama danych — często zadawane pytania](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Korzystanie z aplikacji lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administracja na poziomie dzierżawy](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurowanie ustawień serwera proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Dostosowywanie ustawień komunikacji](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurowanie plików dziennika](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Rozwiązywanie problemów](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i optymalizowanie wydajności bramy](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
