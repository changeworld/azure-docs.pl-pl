---
title: Lokalna Brama danych dla Azure Analysis Services | Microsoft Docs
description: Brama lokalna jest wymagana, jeśli serwer Analysis Services na platformie Azure będzie łączył się z lokalnymi źródłami danych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35ffc7f3c97ca7ab14f94c3607560ffb6ea0b399
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146852"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Łączenie z lokalnymi źródłami danych za pomocą lokalnej bramy danych

Lokalna Brama danych zapewnia bezpieczny transfer danych między lokalnymi źródłami danych i serwerami Azure Analysis Services w chmurze. Oprócz pracy z wieloma serwerami Azure Analysis Services w tym samym regionie, Najnowsza wersja bramy działa również w przypadku Azure Logic Apps, Power BI, aplikacji zaawansowanych i Microsoft Flow. Można skojarzyć wiele usług w tej samej subskrypcji i w tym samym regionie z pojedynczą bramą. Podczas gdy instalowana Brama jest taka sama we wszystkich tych usługach, Azure Analysis Services i Logic Apps mają kilka dodatkowych kroków.

Aby uzyskać Azure Analysis Services, należy pobrać Instalatora z bramą po raz pierwszy jest to proces składający się z czterech części:

- **Pobierz i uruchom Instalatora** — ten krok powoduje zainstalowanie usługi bramy na komputerze w organizacji. Logujesz się również na platformie Azure przy użyciu konta w usłudze Azure AD swojej [dzierżawy](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Konta B2B (gość) platformy Azure nie są obsługiwane.

- **Rejestrowanie bramy** — w tym kroku należy określić nazwę i klucz odzyskiwania dla bramy i wybrać region, zarejestrować bramę w usłudze bramy w chmurze. Zasób bramy można zarejestrować w dowolnym regionie, ale zalecamy, aby znajdował się w tym samym regionie co serwery Analysis Services. 

- **Tworzenie zasobu bramy na platformie Azure** — w tym kroku utworzysz zasób bramy w ramach subskrypcji platformy Azure.

- **Połącz serwery z zasobem bramy** — gdy masz zasób bramy w ramach subskrypcji, możesz rozpocząć połączenie z serwerem. Można połączyć wiele serwerów i innych zasobów, pod warunkiem, że znajdują się one w tej samej subskrypcji i w tym samym regionie.

## <a name="how-it-works"> </a>Jak to działa
Brama zainstalowana na komputerze w organizacji działa jako usługa systemu Windows, **lokalna Brama danych**. Ta usługa lokalna jest zarejestrowana w usłudze bramy w chmurze za pomocą Azure Service Bus. Następnie utworzysz zasób lokalnej bramy danych dla Twojej subskrypcji platformy Azure. Serwery Azure Analysis Services są następnie połączone z zasobem bramy platformy Azure. Gdy modele na serwerze muszą łączyć się z lokalnymi źródłami danych w celu wykonywania zapytań lub przetwarzania, przepływy zapytań i danych przechodzą przez zasób bramy, Azure Service Bus, lokalna usługa lokalnej bramy danych i źródła danych. 

![Zasady działania](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Zapytania i przepływ danych:

1. Usługa w chmurze tworzy zapytanie z zaszyfrowanymi poświadczeniami lokalnego źródła danych. Jest następnie wysyłany do kolejki w celu przetworzenia przez bramę.
2. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Lokalna Brama danych sonduje Azure Service Bus dla oczekujących żądań.
4. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłami danych przy użyciu tych poświadczeń.
5. Brama wysyła zapytanie do źródła danych w celu wykonania.
6. Wyniki są wysyłane ze źródła danych, z powrotem do bramy, a następnie do usługi w chmurze i serwera.

## <a name="installing"></a>Budowy

W przypadku instalowania środowiska Azure Analysis Services należy wykonać kroki opisane w temacie [Instalowanie i Konfigurowanie lokalnej bramy danych dla Azure Analysis Services](analysis-services-gateway-install.md). Ten artykuł dotyczy Azure Analysis Services. Zawiera dodatkowe kroki wymagane do skonfigurowania zasobu lokalnej bramy danych na platformie Azure i połączenia serwera Azure Analysis Services z zasobem.

## <a name="ports-and-communication-settings"></a>Ustawienia portów i komunikacji

Brama tworzy połączenie wychodzące do Azure Service Bus. Komunikuje się on na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 do 9354.  Brama nie wymaga portów przychodzących.

Może być konieczne dołączenie adresów IP do obszaru danych w zaporze. Możesz pobrać [listę adresów IP Microsoft Azure centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Ta lista jest aktualizowana co tydzień. Adresy IP wymienione na liście adresów IP centrum danych platformy Azure są w notacji CIDR. Aby dowiedzieć się więcej, zobacz [Routing bezklasowy między domenami](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Poniżej znajdują się w pełni kwalifikowane nazwy domen używane przez bramę.

| Nazwy domen | Porty wychodzące | Opis |
| --- | --- | --- |
| *. powerbi.com |80 |Protokół HTTP używany do pobierania instalatora. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Odbiorniki na Service Bus Relay za pośrednictwem protokołu TCP (wymaga 443 na potrzeby pozyskiwania tokenów Access Control) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Służy do testowania łączności z Internetem, jeśli Brama jest nieosiągalna przez usługa Power BI. |
| *. microsoftonline-p.com |443 |Używany do uwierzytelniania w zależności od konfiguracji. |
| dc.services.visualstudio.com  |443 |Używane przez AppInsights do zbierania danych telemetrycznych. |

### <a name="force-https"></a>Wymuszanie komunikacji HTTPS z Azure Service Bus

Można wymusić, aby brama mogła komunikować się z Azure Service Bus przy użyciu protokołu HTTPS zamiast bezpośredniego protokołu TCP. Jednak może to znacznie zmniejszyć wydajność. Można zmodyfikować plik *Microsoft. PowerBI. Datamove. Pipeline. GatewayCore. dll. config* , zmieniając wartość z `AutoDetect` na `Https`. Ten plik zazwyczaj znajduje się w *katalogu C:\Program Files\On-premises Data Gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Następne kroki 

Następujące artykuły są zawarte w ogólnej zawartości lokalnej bramy danych, która dotyczy wszystkich usług obsługiwanych przez bramę:

* [Lokalna brama danych — często zadawane pytania](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Korzystanie z lokalnej aplikacji bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administracja na poziomie dzierżawy](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurowanie ustawień serwera proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Dostosuj ustawienia komunikacji](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurowanie plików dziennika](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Rozwiązywanie problemów](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i Optymalizowanie wydajności bramy](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
