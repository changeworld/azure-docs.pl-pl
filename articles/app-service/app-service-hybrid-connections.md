---
title: Połączenia hybrydowe
description: Dowiedz się, jak tworzyć i używać połączeń hybrydowych w usłudze Azure App Service, aby uzyskać dostęp do zasobów w różnych sieciach.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047775"
---
# <a name="azure-app-service-hybrid-connections"></a>Połączenia hybrydowe usługi Azure App Service

Połączenia hybrydowe to zarówno usługa na platformie Azure, jak i funkcja w usłudze Azure App Service. Jako usługa ma użycia i możliwości poza te, które są używane w usłudze App Service. Aby dowiedzieć się więcej o połączeniach hybrydowych i ich użyciu poza usługą App Service, zobacz [Połączenia hybrydowe usługi Azure Relay][HCService].

W usłudze App Service połączenia hybrydowe mogą być używane do uzyskiwania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie umożliwia alternatywnej możliwości dostępu do aplikacji. Zgodnie z usługą App Service każde połączenie hybrydowe jest skorelowane z pojedynczą kombinacją hosta i portu TCP. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, pod warunkiem, że uzyskujesz dostęp do portu nasłuchującego TCP. Funkcja Połączenia hybrydowe nie wie ani nie dba o to, czym jest protokół aplikacji ani do czego uzyskujesz dostęp. Jest to po prostu zapewnienie dostępu do sieci.  


## <a name="how-it-works"></a>Jak to działa ##
Funkcja Połączenia hybrydowe składa się z dwóch wywołań wychodzących do usługi Azure Service Bus Relay. Istnieje połączenie z biblioteki na hoście, gdzie aplikacja jest uruchomiona w usłudze App Service. Istnieje również połączenie z Menedżera połączeń hybrydowych (HCM) do przekaźnika magistrali usług. HCM to usługa przekazywania, którą wdrażasz w sieci obsługującej zasób, do którego próbujesz uzyskać dostęp. 

Za pośrednictwem dwóch połączonych połączeń aplikacja ma tunel TCP do stałej kombinacji host:port po drugiej stronie modułu HCM. Połączenie używa protokołu TLS 1.2 do uwierzytelniania i autoryzacji kluczy podpisu dostępu współdzielonego (SYGNATURA WSPÓŁDZIELONEGO).    

![Diagram przepływu wysokiego poziomu połączenia hybrydowego][1]

Gdy aplikacja wysunie żądanie DNS zgodne ze skonfigurowanym punktem końcowym połączenia hybrydowego, ruch TCP wychodzący zostanie przekierowany za pośrednictwem połączenia hybrydowego.  

> [!NOTE]
> Oznacza to, że należy zawsze używać nazwy DNS dla połączenia hybrydowego. Niektóre oprogramowanie klienckie nie wykonuje wyszukiwania DNS, jeśli punkt końcowy używa adresu IP. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Korzyści związane z połączeniem hybrydowym usługi App Service ###

Istnieje wiele korzyści z możliwości połączeń hybrydowych, w tym:

- Aplikacje mogą bezpiecznie uzyskiwać dostęp do lokalnych systemów i usług.
- Ta funkcja nie wymaga punktu końcowego dostępnego dla Internetu.
- Jest szybki i łatwy w konfiguracji. 
- Każde połączenie hybrydowe jest zgodne z kombinacją jednego hosta:portu, co jest przydatne dla zabezpieczeń.
- Zwykle nie wymaga otworów zapory. Wszystkie połączenia są wychodzące za pośrednictwem standardowych portów internetowych.
- Ponieważ funkcja jest na poziomie sieci, jest niezależna od języka używanego przez aplikację i technologii używanej przez punkt końcowy.
- Może służyć do zapewnienia dostępu w wielu sieciach z jednej aplikacji. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Czynności, których nie można wykonać za pomocą połączeń hybrydowych ###

Między innymi:

- Zamontuj dysk.
- Użyj protokołu UDP.
- Uzyskaj dostęp do usług opartych na TCP, które używają portów dynamicznych, takich jak tryb pasywny FTP lub rozszerzony tryb pasywny.
- Obsługa protokołu LDAP, ponieważ może wymagać UDP.
- Obsługa usługi Active Directory, ponieważ nie można dołączyć do domeny pracownika usługi App Service.

### <a name="prerequisites"></a>Wymagania wstępne ###
 - Wymagana jest usługa aplikacji systemu Windows. Jest on dostępny tylko w systemie Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Dodawanie i tworzenie połączeń hybrydowych w aplikacji ##

Aby utworzyć połączenie hybrydowe, przejdź do [witryny Azure portal][portal] i wybierz aplikację. Wybierz **pozycję Konfigurowanie** > sieci**konfiguruj punkty końcowe połączenia hybrydowego**. W tym miejscu można zobaczyć połączenia hybrydowe skonfigurowane dla aplikacji.  

![Zrzut ekranu przedstawiający listę połączeń hybrydowych][2]

Aby dodać nowe połączenie hybrydowe, wybierz **[+] Dodaj połączenie hybrydowe**.  Zostanie wyświetlona lista połączeń hybrydowych, które zostały już utworzone. Aby dodać jeden lub więcej z nich do aplikacji, wybierz te, które chcesz, a następnie wybierz pozycję **Dodaj wybrane połączenie hybrydowe**.  

![Zrzut ekranu przedstawiający portal połączenia hybrydowego][3]

Jeśli chcesz utworzyć nowe połączenie hybrydowe, wybierz pozycję **Utwórz nowe połączenie hybrydowe**. Określ: 

- Nazwa połączenia hybrydowego.
- Nazwa hosta punktu końcowego.
- Port punktu końcowego.
- Obszar nazw usługi Service Bus, którego chcesz użyć.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego połączenia hybrydowego][4]

Każde połączenie hybrydowe jest powiązane z obszarem nazw usługi Service Bus, a każdy obszar nazw usługi Service Bus znajduje się w regionie platformy Azure. Ważne jest, aby spróbować użyć obszaru nazw usługi Service Bus w tym samym regionie co aplikacja, aby uniknąć opóźnień wywołanych przez sieć.

Jeśli chcesz usunąć połączenie hybrydowe z aplikacji, kliknij je prawym przyciskiem myszy i wybierz polecenie **Rozłącz**.  

Po dodaniu połączenia hybrydowego do aplikacji możesz zobaczyć szczegóły na jej temat, po prostu je wybierając. 

![Zrzut ekranu przedstawiający szczegóły połączeń hybrydowych][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Tworzenie połączenia hybrydowego w portalu Azure Relay ###

Oprócz środowiska portalu z poziomu aplikacji można tworzyć połączenia hybrydowe z poziomu portalu usługi Azure Relay. Aby połączenie hybrydowe było używane przez usługę App Service, musi:

* Wymagaj autoryzacji klienta.
* Mieć element metadanych o nazwie punkt końcowy, który zawiera kombinację host:port jako wartość.

## <a name="hybrid-connections-and-app-service-plans"></a>Połączenia hybrydowe i plany usługi app service ##

Połączenia hybrydowe usługi App Service są dostępne tylko w jednostkach SKU cen podstawowych, standardowych, premium i izolowanych. Istnieją limity związane z planem cenowym.  

| Plan cenowy | Liczba połączeń hybrydowych, które można nawiązyć do stosowania w planie |
|----|----|
| Podstawowa (Basic) | 5 |
| Standardowa | 25 |
| Premium | 200 |
| Izolowany | 200 |

Interfejs użytkownika planu usługi aplikacji pokazuje, ile połączeń hybrydowych jest używanych i przez jakie aplikacje.  

![Zrzut ekranu przedstawiający właściwości planu usługi app service][6]

Wybierz połączenie hybrydowe, aby wyświetlić szczegóły. Możesz zobaczyć wszystkie informacje, które widziałeś w widoku aplikacji. Można również zobaczyć, ile innych aplikacji w tym samym planie korzysta z tego połączenia hybrydowego.

Istnieje limit liczby punktów końcowych połączenia hybrydowego, które mogą być używane w planie usługi app service. Każde używane połączenie hybrydowe może być jednak używane w dowolnej liczbie aplikacji w tym planie. Na przykład pojedyncze połączenie hybrydowe używane w pięciu oddzielnych aplikacjach w planie usługi app service jest liczone jako jedno połączenie hybrydowe.

### <a name="pricing"></a>Cennik ###

Oprócz tego, że istnieje wymaganie SKU planu usługi aplikacji, istnieje dodatkowy koszt korzystania z połączeń hybrydowych. Istnieje opłata za każdy odbiornik używany przez połączenie hybrydowe. Odbiornik jest Menedżer połączenia hybrydowego. Jeśli masz pięć połączeń hybrydowych obsługiwanych przez dwa menedżerów połączeń hybrydowych, byłoby to 10 odbiorników. Aby uzyskać więcej informacji, zobacz [Ceny usługi Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Menedżer połączeń hybrydowych ##

Funkcja Połączenia hybrydowe wymaga agenta przekazywania w sieci, który obsługuje punkt końcowy połączenia hybrydowego. Ten agent przekaźnika jest nazywany Menedżerem połączeń hybrydowych (HCM). Aby pobrać program HCM, z aplikacji w [witrynie Azure portal][portal]wybierz pozycję **Konfiguruj** > **punkty końcowe połączenia hybrydowego.**  

To narzędzie działa w systemie Windows Server 2012 i nowszych. Moduł HCM działa jako usługa i łączy się z usługą Azure Relay na porcie 443.  

Po zainstalowaniu HCM, można uruchomić HybridConnectionManagerUi.exe do korzystania z interfejsu użytkownika dla narzędzia. Ten plik znajduje się w katalogu instalacji Menedżera połączeń hybrydowych. W systemie Windows 10 możesz również po prostu wyszukać *interfejs użytkownika Menedżera połączeń hybrydowych* w polu wyszukiwania.  

![Zrzut ekranu przedstawiający Menedżera połączeń hybrydowych][7]

Po uruchomieniu interfejsu użytkownika HCM pierwszą rzeczą, którą widzisz, jest tabela, która zawiera listę wszystkich połączeń hybrydowych, które są skonfigurowane z tym wystąpieniem HCM. Jeśli chcesz wprowadzić zmiany, najpierw uwierzytelnij się za pomocą platformy Azure. 

Aby dodać jedno lub więcej połączeń hybrydowych do hcm:

1. Uruchom interfejs użytkownika HCM.
2. Wybierz **pozycję Konfiguruj inne połączenie hybrydowe**.
![Zrzut ekranu przedstawiający konfigurowanie nowych połączeń hybrydowych][8]

1. Zaloguj się za pomocą konta platformy Azure, aby uzyskać dostęp do połączeń hybrydowych w ramach subskrypcji. Moduł HCM nie nadal używa konta platformy Azure poza tym. 
1. wybierz subskrypcję.
1. Wybierz połączenia hybrydowe, które mają być przekazywane przez moduł HCM.
![Zrzut ekranu przedstawiający połączenia hybrydowe][9]

1. Wybierz **pozycję Zapisz**.

Teraz możesz zobaczyć dodane połączenia hybrydowe. Można również wybrać skonfigurowane połączenie hybrydowe, aby wyświetlić szczegóły.

![Zrzut ekranu przedstawiający szczegóły połączenia hybrydowego][10]

Aby obsługiwać połączenia hybrydowe, za pomocą które jest skonfigurowane, HCM wymaga:

- Dostęp TCP do platformy Azure za pomocą portu 443.
- Dostęp TCP do punktu końcowego połączenia hybrydowego.
- Możliwość wykonywania wyszukiwania DNS na hoście punktu końcowego i obszarze nazw usługi Service Bus.

> [!NOTE]
> Usługa Azure Relay korzysta z gniazd sieci Web w celu łączności. Ta funkcja jest dostępna tylko w systemie Windows Server 2012 lub nowszym. Z tego powodu HCM nie jest obsługiwany na nic wcześniej niż Windows Server 2012.
>

### <a name="redundancy"></a>Nadmiarowość ###

Każdy moduł HCM może obsługiwać wiele połączeń hybrydowych. Ponadto dowolne połączenie hybrydowe może być obsługiwane przez wiele hcmów. Domyślnym zachowaniem jest kierowanie ruchu przez skonfigurowane hcms dla danego punktu końcowego. Jeśli potrzebujesz wysokiej dostępności w połączeniach hybrydowych z sieci, uruchom wiele hcmów na oddzielnych komputerach. Algorytm dystrybucji obciążenia używany przez usługę Przekazywania do dystrybucji ruchu do hcms jest losowe przypisanie. 

### <a name="manually-add-a-hybrid-connection"></a>Ręczne dodawanie połączenia hybrydowego ###

Aby umożliwić osobie spoza subskrypcji hostowanie wystąpienia HCM dla danego połączenia hybrydowego, udostępnij im ciąg połączenia bramy dla połączenia hybrydowego. Ciąg połączenia bramy można zobaczyć we właściwościach połączenie hybrydowe w [witrynie Azure portal][portal]. Aby użyć tego ciągu, wybierz pozycję **Wprowadź ręcznie** w hcm i wklej w ciągu połączenia bramy.

![Ręczne dodawanie połączenia hybrydowego][11]

### <a name="upgrade"></a>Uaktualnienie ###

Menedżer połączeń hybrydowych jest okresowy, aby rozwiązać problemy lub wprowadzić ulepszenia. Po wydaniu uaktualnień w interfejsie użytkownika HCM pojawi się okno podręczne. Zastosowanie uaktualnienia spowoduje zastosowanie zmian i ponowne uruchomienie hcm. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programowe dodawanie połączenia hybrydowego do aplikacji ##

Interfejsy API wymienione poniżej mogą służyć bezpośrednio do zarządzania połączeniami hybrydowymi połączonymi z aplikacjami. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Obiekt JSON skojarzony z połączeniem hybrydowym wygląda następująco:

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

Jednym ze sposobów użycia tych informacji jest z armclient, które można uzyskać z projektu [ARMClient][armclient] GitHub. Oto przykład dołączania istniejącego połączenia hybrydowego do aplikacji. Tworzenie pliku JSON na powyższy schemat, taki jak:

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

Aby korzystać z tego interfejsu API, potrzebny jest identyfikator zasobu klucza wysyłania i przekazywania. Jeśli informacje zostały zapisane za pomocą pliku hctest.json, wydaj to polecenie, aby dołączyć połączenie hybrydowe do aplikacji: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Zabezpiecz połączenia hybrydowe ##

Istniejące połączenie hybrydowe może zostać dodane do innych aplikacji sieci Web usługi App Service przez dowolnego użytkownika, który ma wystarczające uprawnienia do podstawowego przekazywania usługi Azure Service Bus Relay. Oznacza to, że jeśli należy uniemożliwić innym osobom ponowne użycie tego samego połączenia hybrydowego (na przykład gdy zasób docelowy jest usługą, która nie ma żadnych dodatkowych środków bezpieczeństwa w celu zapobiegania nieautoryzowanemu dostępowi), należy zablokować dostęp do platformy Azure. Przekaźnik magistrali usług.

Każda `Reader` osoba mająca dostęp do przekazywania będzie mogła _zobaczyć_ połączenie hybrydowe podczas próby dodania go do aplikacji sieci Web w witrynie Azure Portal, ale nie będzie mogła _dodać_ go, ponieważ nie ma uprawnień do pobierania ciągu połączenia, który jest używany do ustanawiania połączenia przekazywania. Aby pomyślnie dodać połączenie hybrydowe, muszą `listKeys` mieć`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`uprawnienie ( ). Rola `Contributor` lub inna rola, która zawiera to uprawnienie w programie Relay, umożliwi użytkownikom korzystanie z połączenia hybrydowego i dodanie jej do własnych aplikacji sieci Web.

## <a name="troubleshooting"></a>Rozwiązywanie problemów ##

Stan "Połączony" oznacza, że co najmniej jeden moduł HCM jest skonfigurowany z tym połączeniem hybrydowym i może osiągnąć platformę Azure. Jeśli stan połączenia hybrydowego nie mówi **Connected**, połączenie hybrydowe nie jest skonfigurowany na żadnym HCM, który ma dostęp do platformy Azure.

Głównym powodem, dla którego klienci nie mogą połączyć się z punktem końcowym, jest określenie punktu końcowego przy użyciu adresu IP zamiast nazwy DNS. Jeśli aplikacja nie może osiągnąć żądanego punktu końcowego i użyto adresu IP, przełącz się na przy użyciu nazwy DNS, która jest prawidłowa na hoście, na którym jest uruchomiony moduł HCM. Sprawdź również, czy nazwa DNS jest poprawnie rozpoznawana na hoście, na którym działa moduł HCM. Upewnij się, że istnieje łączność z hosta, gdzie HCM jest uruchomiony do punktu końcowego połączenia hybrydowego.  

W usłudze App Service narzędzie wiersza polecenia **tcpping** można wywołać z konsoli Zaawansowane narzędzia (Kudu). To narzędzie może stwierdzić, czy masz dostęp do punktu końcowego TCP, ale nie informuje, czy masz dostęp do punktu końcowego połączenia hybrydowego. Korzystając z narzędzia w konsoli względem punktu końcowego połączenia hybrydowego, potwierdzasz tylko, że używa kombinacji host:port.  

Jeśli masz klienta wiersza polecenia dla punktu końcowego, można przetestować łączność z konsoli aplikacji. Na przykład można przetestować dostęp do punktów końcowych serwera sieci web przy użyciu curl.

## <a name="biztalk-hybrid-connections"></a>Połączenia hybrydowe systemu BizTalk ##

Wczesna forma tej funkcji została nazwana połączeniami hybrydowymi BizTalk. Ta zdolność zakończyła się 31 maja 2018 roku i zaprzestała działalności. Połączenia hybrydowe systemu BizTalk zostały usunięte ze wszystkich aplikacji i nie są dostępne za pośrednictwem portalu lub interfejsu API. Jeśli nadal masz te starsze połączenia skonfigurowane w Menedżerze połączeń hybrydowych, zobaczysz stan Przerwane i wyświetly instrukcję Końca życia u dołu.

![Połączenia hybrydowe BizTalk w hcm][12]


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
