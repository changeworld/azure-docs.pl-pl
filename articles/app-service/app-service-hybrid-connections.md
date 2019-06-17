---
title: Połączenia hybrydowe — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Jak utworzyć i dostęp do zasobów w różnych sieciach za pomocą połączeń hybrydowych
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4b125649dee51680625ac5a92b31bdc9f6830529
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069661"
---
# <a name="azure-app-service-hybrid-connections"></a>Połączenia hybrydowe usługi Azure App Service #

Połączenia hybrydowe to usługa platformy Azure i funkcji w usłudze Azure App Service. Jako usługa ma zastosowań i możliwości poza tymi, które są używane w usłudze App Service. Aby dowiedzieć się więcej na temat połączeń hybrydowych i ich użycia poza usługi App Service, zobacz [połączeń hybrydowych usługi Azure Relay][HCService].

W ramach usługi App Service połączeń hybrydowych może służyć do dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie uwzględnia alternatywne możliwość dostęp do Twoich aplikacji. W przypadku użycia w usłudze App Service, każde połączenie hybrydowe skorelowany jednej kombinacji hosta i portu TCP. Oznacza to, że punkt końcowy połączenia hybrydowego może być w dowolnym systemie operacyjnym i każdej aplikacji, możesz podać uzyskują dostęp do portu nasłuchiwania protokołu TCP. Funkcja połączeń hybrydowych nie wiedzieć, ani nie obchodzi, jest protokół aplikacji lub uzyskujesz dostęp. Jest on po prostu zapewnienie dostępu do sieci.  


## <a name="how-it-works"></a>Jak to działa ##
Funkcja połączeń hybrydowych składa się z dwóch połączenia wychodzące do usługi Azure Service Bus Relay. Brak połączenia z biblioteki na hoście, w którym Twoja aplikacja działa w usłudze App Service. Jest również połączenie z Menedżera połączeń hybrydowych (HCM) usługi Service Bus Relay. HCM jest wdrożony w ramach sieci hostingu zasób, do którego próbujesz uzyskać dostęp do usługi przekazywania. 

Za pomocą dwóch połączeń połączone Twoja aplikacja ma tunel protokołu TCP do kombinację stały port hosta: po drugiej stronie HCM. Połączenie używa protokołu TLS 1.2, zabezpieczeń i udostępnione podpisu (SAS) klucze dostępu do uwierzytelniania i autoryzacji.    

![Diagram przedstawiający ogólny przepływ połączenia hybrydowego][1]

Gdy aplikacja wykonuje żądanie DNS, które odpowiada skonfigurowanego punktu końcowego połączenia hybrydowego, nastąpi przekierowanie wychodzący ruch TCP za pośrednictwem połączenia hybrydowego.  

> [!NOTE]
> Oznacza to, że należy zawsze używaj nazwy DNS dla połączenia hybrydowego. Niektóre oprogramowanie klienckie nie wyszukiwania DNS, jeśli zamiast tego używa punktu końcowego adresu IP.
>

### <a name="app-service-hybrid-connection-benefits"></a>Korzyści połączenia hybrydowe usługi aplikacji ###

Istnieje szereg korzyści klientom możliwości połączeń hybrydowych, w tym:

- Aplikacje mogą uzyskiwać dostęp w systemach lokalnych i usług bezpieczne.
- Ta funkcja nie wymaga punktu końcowego dostępne za pośrednictwem Internetu.
- Jest szybkie i łatwe do skonfigurowania. 
- Każde połączenie hybrydowe pasuje do jednego hosta: port kombinacji, przydatne w przypadku zabezpieczeń.
- Zwykle nie jest wymagane otworów zapory. Połączenia są wszystkie wychodzące przez porty standardowe sieci web.
- Ponieważ ta funkcja jest poziomie sieci, jest niezależny od języka używanego przez aplikację i technologię używaną przez punkt końcowy.
- Może służyć do zapewnienia dostępu w wielu sieciach z pojedynczą aplikacją. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Czynności, które nie można wykonać za pomocą połączeń hybrydowych ###

Obejmuje to między innymi, nie będzie możliwe za pomocą połączeń hybrydowych:

- Zainstaluj dysk.
- Użyj protokołu UDP.
- Dostępu oparte na protokole TCP usługi, które używają portów dynamicznych, takie jak FTP w trybie pasywnym lub pasywnym trybie rozszerzonym.
- Obsługuje LDAP, ponieważ wymaga on UDP.
- Obsługuje usługi Active Directory, ponieważ nie można było przyłączenie do domeny usługi App Service procesu roboczego.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Dodawanie i tworzenie połączeń hybrydowych w swojej aplikacji ##

Aby utworzyć połączenie hybrydowe, przejdź do [witryny Azure portal] [ portal] i wybierz swoją aplikację. Wybierz **sieć** > **konfigurowania punktów końcowych połączenia hybrydowego**. Tutaj widać połączeń hybrydowych, które są skonfigurowane dla twojej aplikacji.  

![Zrzut ekranu połączenia hybrydowego listy][2]

Aby dodać nowe połączenie hybrydowe, wybierz **[+] Dodaj połączenie hybrydowe**.  Zobaczysz listę połączeń hybrydowych, który został już utworzony. Aby dodać co najmniej jeden z nich do swojej aplikacji, wybierz te, a następnie wybierz **Dodaj wybrane połączenie hybrydowe**.  

![Zrzut ekranu połączenia hybrydowego portalu][3]

Aby utworzyć nowe połączenie hybrydowe, należy zaznaczyć **Utwórz nowe połączenie hybrydowe**. Określ: 

- Nazwa połączenia hybrydowego.
- Nazwa hosta punktu końcowego.
- Port punktu końcowego.
- Przestrzeń nazw usługi Service Bus, którego chcesz użyć.

![Zrzut ekranu opcji Utwórz nowy hybrydowe połączenie — okno dialogowe][4]

Każde połączenie hybrydowe jest powiązany z przestrzeni nazw usługi Service Bus, a każda przestrzeń nazw usługi Service Bus znajduje się w regionie platformy Azure. Należy podjąć próbę użycia przestrzeni nazw usługi Service Bus w tym samym regionie co aplikacja, w celu wyeliminowania opóźnień sieciowych wywołane.

Jeśli chcesz usunąć połączenie hybrydowe z aplikacji, kliknij go prawym przyciskiem myszy, a następnie wybierz **rozłączenia**.  

Podczas połączenia hybrydowego jest dodawany do Twojej aplikacji, można wyświetlić szczegółowe informacje na go, po prostu wybierając go. 

![Zrzut ekranu z hybrydowego szczegóły połączenia][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Tworzenie połączenia hybrydowego w portalu usługi Azure Relay ###

Oprócz portalu doświadczenia z poziomu aplikacji można utworzyć połączenia hybrydowe z w portalu usługi Azure Relay. Połączenia hybrydowego, który będzie używany przez usługi App Service musi:

* Wymaga autoryzacji klienta.
* Element metadanych, nazwę punktu końcowego, który zawiera kombinację portów: hosta jako wartość.

## <a name="hybrid-connections-and-app-service-plans"></a>Połączenia hybrydowe i usługa App Service planów ##

Połączenia hybrydowe usługi App Service są dostępne tylko w podstawowa, standardowa, Premium i izolowany wycena jednostek SKU. Istnieją limity powiązane z cennikiem.  

| Plan taryfowy | Liczba połączeń hybrydowych, które można używać w ramach planu |
|----|----|
| Podstawowa | 5 |
| Standardowa (Standard) | 25 |
| Premium | 200 |
| Izolowane | 200 |

Plan usługi App Service interfejs użytkownika pokazuje, jak wiele połączeń hybrydowych są używane i jakie aplikacje.  

![Właściwości planu zrzut ekranu usługi App Service][6]

Wybierz połączenie hybrydowe, aby wyświetlić szczegóły. Widać wszystkie informacje, który był wyświetlany w widoku aplikacji. Widać również, jak wiele innych aplikacji, w tym samym planie korzystają z tego połączenia hybrydowego.

Ma limitu liczby punktów końcowych połączenie hybrydowe, które mogą być używane w planie usługi App Service. Każde połączenie hybrydowe używane, jednak można w dowolnej liczbie aplikacji w tym planie. Na przykład pojedyncze połączenie hybrydowe, używanym w pięciu osobnych aplikacji w ramach planu usługi App Service jest liczona jako jeden połączenie hybrydowe.

### <a name="pricing"></a>Cennik ###

Oprócz nie jest to wymaganie dotyczące jednostki SKU planu usługi App Service Brak dodatkowych kosztów za pomocą połączeń hybrydowych. Istnieje opłata za każdego odbiornika używany przez połączenie hybrydowe. Odbiornik jest Menedżera połączeń hybrydowych. Jeśli masz 5 połączeń hybrydowych, obsługiwane przez dwa Menedżerowie połączeń hybrydowych, wyniesie 10 odbiorników. Aby uzyskać więcej informacji, zobacz [cennika usługi Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Menedżer połączeń hybrydowych ##

Funkcja połączeń hybrydowych wymaga agenta przekazywania w sieci, który jest hostem punktu końcowego połączenia hybrydowego. Ten agent przekazywania nosi nazwę menedżera połączeń hybrydowych (HCM). Aby pobrać HCM, z poziomu aplikacji w [witryny Azure portal][portal], wybierz opcję **sieci** > **Konfigurowanie punktów końcowychpołączeniahybrydowego**.  

To narzędzie działa w systemie Windows Server 2012 i nowszych. HCM działa jako usługa i nawiązuje połączenie wychodzące do usługi Azure Relay na porcie 443.  

Po zainstalowaniu HCM, możesz uruchomić HybridConnectionManagerUi.exe korzystać z interfejsu użytkownika dla narzędzia. Ten plik znajduje się w katalogu instalacji Menedżera połączeń hybrydowych. W systemie Windows 10, możesz też po prostu wyszukać *interfejsu użytkownika Menedżera połączeń hybrydowych* w polu wyszukiwania.  

![Zrzut ekranu Menedżera połączeń hybrydowych][7]

Po uruchomieniu interfejsu użytkownika HCM, pierwszą rzeczą, jaką widzisz to tabelę, która zawiera listę wszystkich połączeń hybrydowych, które są skonfigurowane z tym wystąpieniem HCM. Jeśli chcesz wprowadzić zmiany, najpierw uwierzytelnić się za pomocą platformy Azure. 

Aby dodać co najmniej jednego połączenia hybrydowe do Twojej HCM:

1. Rozpocznij HCM interfejsu użytkownika.
2. Wybierz **skonfigurować inne połączenie hybrydowe**.
![Zrzut ekranu przedstawiający Konfigurowanie nowego połączenia hybrydowe][8]

1. Zaloguj się przy użyciu konta platformy Azure, można pobrać połączeń hybrydowych dostępne z subskrypcjami. HCM, nie mogą korzystać z Twojego konta platformy Azure, poza tym. 
1. wybierz subskrypcję.
1. Wybierz połączeń hybrydowych, które HCM przekazanie.
![Zrzut ekranu przedstawiający połączeń hybrydowych][9]

1. Wybierz pozycję **Zapisz**.

Teraz widać połączeń hybrydowych, został dodany. Możesz również wybrać skonfigurowane połączenie hybrydowe, aby wyświetlić szczegóły.

![Zrzut ekranu przedstawiający szczegółów połączenia hybrydowego][10]

Do obsługi połączeń hybrydowych jest konfigurowana, HCM wymaga:

- TCP korzystanie z platformy Azure za pośrednictwem portu 443.
- TCP dostęp do punktu końcowego połączenia hybrydowego.
- Możliwości w celu wyszukania DNS na hoście punktu końcowego i przestrzeni nazw usługi Service Bus.

> [!NOTE]
> Usługa Azure Relay opiera się na gniazda sieci Web dla połączenia. Ta funkcja jest tylko dostępne w systemie Windows Server 2012 lub nowszym. Z tego powodu HCM nie jest obsługiwana w żadnych starszych niż Windows Server 2012.
>

### <a name="redundancy"></a>Nadmiarowość ###

Każdy HCM, może obsługiwać wiele połączeń hybrydowych. Ponadto wszelkie danego połączenia hybrydowego mogą być obsługiwane przez wiele HCMs. Domyślnym zachowaniem jest kierowanie ruchu skonfigurowane HCMs dla dowolnego danego punktu końcowego. Wysoka dostępność połączeń hybrydowych z Twojej sieci, należy uruchomić wiele HCMs na oddzielnych komputerach. Algorytmu dystrybucji obciążenia, które są używane przez usługi przekazywania do dystrybucji ruchu do HCMs jest przypisanie losowej. 

### <a name="manually-add-a-hybrid-connection"></a>Ręcznie Dodaj połączenie hybrydowe ###

Aby włączyć osoby spoza Twojej subskrypcji do hostowania wystąpienia usługi HCM dla danego połączenia hybrydowe, udostępniać parametry połączenia bramy połączenia hybrydowego ze sobą. Możesz zobaczyć parametry połączenia bramy we właściwościach połączenia hybrydowego w [witryny Azure portal][portal]. Aby użyć tych parametrów, wybierz pozycję **Wprowadź ręcznie** w HCM, a następnie wklej parametry połączenia bramy.

![Ręcznie Dodaj połączenie hybrydowe][11]

### <a name="upgrade"></a>Uaktualnienie ###

Brak okresowe aktualizacje do Menedżera połączeń hybrydowych rozwiązywania problemów lub ulepszenia. Jeśli uaktualnienia zostaną zwolnione, okna podręcznego będą widoczne w Interfejsie użytkownika HCM. Stosowanie uaktualnienia zastosowania zmian i ponowne uruchomienie HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programowe Dodawanie połączenia hybrydowego do aplikacji ##

Interfejsy API wskazanych poniżej może służyć bezpośrednio, aby zarządzać połączeń hybrydowych, w połączonych aplikacjach. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Obiekt JSON skojarzony z połączenia hybrydowego wygląda następująco:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Jest jednym ze sposobów dzięki tym informacjom armclient, którą można pobrać z [ARMClient] [ armclient] projektu GitHub. Oto przykład podczas dołączania istniejące połączenie hybrydowe z aplikacją. Utwórz plik JSON na powyższym schematu, takich jak:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Aby użyć tego interfejsu API, należy Wyślij klucz i przekazywania identyfikator zasobu. Zapisanie informacji o hctest.json nazwę pliku, należy wydać to polecenie, aby dołączyć swoje połączenie hybrydowe z aplikacją: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Rozwiązywanie problemów ##

Stan "Połączono" oznacza, że co najmniej jeden HCM skonfigurowano przy użyciu tego połączenia hybrydowego i jest w stanie dociera do platformy Azure. Jeśli stan połączenia hybrydowego nie określa **połączono**, połączenia hybrydowe nie jest skonfigurowany na dowolnym HCM, które ma dostęp do platformy Azure.

Głównym powodem, że klienci nie można połączyć z ich punktu końcowego jest, ponieważ punkt końcowy został określony przy użyciu adresu IP zamiast nazwy DNS. Jeśli aplikacja nie może nawiązać połączenia żądanego punktu końcowego i użycie adresu IP, przełączyć się przy użyciu nazwy DNS, który jest niepoprawna na hoście, gdzie jest uruchomione HCM. Sprawdź także, czy nazwa DNS rozpoznaje poprawnie na hoście, na którym działa HCM. Upewnij się, że istnieje łączność z hosta, w którym HCM, działa na punkt końcowy połączenia hybrydowego.  

W usłudze App Service **tcpping** narzędzia wiersza polecenia mogą być wywoływane z poziomu konsoli narzędzia zaawansowane (Kudu). To narzędzie może określić, jeśli masz dostęp do punktu końcowego TCP, ale go nie informujące, jeśli masz dostęp do punktu końcowego połączenia hybrydowego. Gdy używasz narzędzia w konsoli względem punktu końcowego połączenia hybrydowego tylko potwierdzasz korzysta z kombinacji hosta: port.  

Jeśli masz klienta wiersza polecenia dla punktu końcowego usługi, możesz przetestować łączność z aplikacji konsoli. Na przykład można przetestować dostęp do punktów końcowych serwera sieci web, przy użyciu programu curl.

## <a name="biztalk-hybrid-connections"></a>Połączenia hybrydowe BizTalk ##

Wczesne formularza ta funkcja została wywołana połączenia hybrydowe BizTalk. Ta funkcja poszło zakończenia eksploatacji 31 maja 2018 r i zakończeniu operacji. Połączenia hybrydowe BizTalk zostały usunięte ze wszystkich aplikacji i nie są dostępne za pośrednictwem interfejsu API lub portalu. Jeśli nadal masz te starsze połączeń, skonfigurowanych Menedżera połączeń hybrydowych, będzie wyświetlany stan wycofany i wyświetlić instrukcji zakończenia eksploatacji u dołu.

![Połączenia hybrydowe BizTalk w HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
