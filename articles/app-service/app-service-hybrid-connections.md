---
title: Usługa aplikacji Azure połączeń hybrydowych | Dokumentacja firmy Microsoft
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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2017
ms.locfileid: "25990823"
---
# <a name="azure-app-service-hybrid-connections"></a>Połączenia hybrydowe usługi aplikacji Azure #

Połączenia hybrydowe jest usługa na platformie Azure i funkcji w usłudze Azure App Service. Usługa ma zastosowań i możliwości poza tymi, które są używane w usłudze App Service. Aby dowiedzieć się więcej na temat połączeń hybrydowych i ich użycia poza usługi aplikacji, zobacz [połączeń hybrydowych przekazywania Azure][HCService].

W ramach usługi aplikacji połączeń hybrydowych można uzyskać dostęp do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego w aplikacji. Nie umożliwia alternatywnych możliwości uzyskania dostępu do Twojej aplikacji. Używane w usłudze App Service, każde połączenie hybrydowe są powiązane z jednym kombinacji hosta i portu TCP. Oznacza to, że punktu końcowego połączenia hybrydowe można w dowolnym systemie operacyjnym i dowolnej aplikacji, należy podać uzyskują dostęp do portu nasłuchiwania protokołu TCP. Funkcja połączeń hybrydowych znasz lub obsługę protokołu aplikacji jest lub co uzyskujesz dostęp do. Po prostu zapewnia dostęp do sieci.  


## <a name="how-it-works"></a>Jak to działa ##
Funkcja połączeń hybrydowych składa się z dwóch wywołań wychodzących do przekaźnik magistrali usług Azure. Brak połączenia z biblioteki na hoście, w którym aplikacja jest uruchomiona w usłudze App Service. Brak połączenia z Menedżera połączeń hybrydowych (HCM) przekaźnik magistrali usług. HCM jest usługi przekazywania, który można wdrożyć w ramach hosting zasób, do którego próbujesz uzyskać dostęp do sieci. 

Przez dwa połączenia połączone aplikacja ma tunelu TCP kombinację stałych hosta: port. po drugiej stronie HCM. Połączenie korzysta z protokołu TLS 1.2 dla zabezpieczeń i klucze sygnatury dostępu Współdzielonego dostępu współdzielonego dla uwierzytelniania i autoryzacji.    

![Diagram przedstawiający połączenia hybrydowego przepływu wysokiego poziomu][1]

Gdy aplikacja wysyła żądanie DNS zgodny punktu końcowego skonfigurowanego połączenia hybrydowego, zostanie przekierowany na ruch wychodzący TCP za pośrednictwem połączenia hybrydowego.  

> [!NOTE]
> Oznacza to, że należy zawsze używać nazwy DNS dla połączenia hybrydowego. Niektóre oprogramowanie klienckie nie wyszukiwania DNS, jeśli zamiast tego punktu końcowego korzysta z adresu IP.
>
>

Funkcja połączeń hybrydowych ma dwa typy: połączeń hybrydowych, które są oferowane jako Usługa Service Bus Relay i połączeń hybrydowych starszej usługi BizTalk Azure. Te ostatnie są nazywane klasycznego połączeń hybrydowych w portalu. Istnieje więcej informacji na temat ich w dalszej części tego artykułu.

### <a name="app-service-hybrid-connection-benefits"></a>Korzyści połączenia hybrydowego usługi aplikacji ###

Istnieje wiele korzyści możliwości połączeń hybrydowych było możliwe, w tym:

- Aplikacje mogą uzyskać dostęp do systemów lokalnych i usług bezpieczny sposób.
- Funkcja nie wymaga punktu końcowego dostępny z Internetu.
- Jest to szybka i łatwa do skonfigurowania. 
- Każde połączenie hybrydowe pasuje do jednego hosta: port. kombinacji, przydatne do zabezpieczeń.
- Zwykle nie wymaga luk zapory. Połączenia są wszystkie wychodzące przez porty standardowe sieci web.
- Ponieważ funkcja jest poziomie sieci, jest niezależny od języka używanego przez aplikację i technologii używanych przez punkt końcowy.
- Może służyć do zapewnienia dostępu w wielu sieciach z jednej aplikacji. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Czynności, które nie można wykonywać z połączeń hybrydowych ###

Istnieje kilka rzeczy, które nie można wykonać za pomocą połączeń hybrydowych, w tym:

- Zainstalowanie dysku.
- Przy użyciu protokołu UDP.
- Uzyskiwanie dostępu do usług opartych na protokole TCP, które używają portów dynamicznych, takie jak FTP w trybie pasywnym lub w trybie pasywnym rozszerzony.
- Obsługa protokołu LDAP, ponieważ czasami wymaga protokołu UDP.
- Obsługa usługi Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Dodawanie i tworzenie połączeń hybrydowych w aplikacji ##

Można utworzyć połączenia hybrydowe przez aplikację usługi aplikacji w portalu Azure lub z przekaźnika usługi Azure w portalu Azure. Zaleca się utworzenie połączeń hybrydowych było możliwe za pośrednictwem aplikacji usługi aplikacji, który ma być używany z połączeń hybrydowych. Aby utworzyć połączenie hybrydowe, przejdź do [portalu Azure] [ portal] i wybierz aplikację. Wybierz **sieci** > **skonfiguruj punkty końcowe połączenia hybrydowego**. W tym miejscu widać połączeń hybrydowych, które są skonfigurowane dla aplikacji.  

![Lista zrzut ekranu połączenia hybrydowego][2]

Aby dodać nowe połączenie hybrydowe, zaznacz **Dodaj połączenie hybrydowe**.  Zobaczysz listę połączeń hybrydowych, które zostały już utworzone. Aby dodać co najmniej jeden z nich do aplikacji, wybierz te, a następnie wybierz **Dodaj wybrane połączenie hybrydowe**.  

![Zrzut ekranu połączenia hybrydowego portalu][3]

Jeśli chcesz utworzyć nowe połączenie hybrydowe, wybierz **Utwórz nowe połączenie hybrydowe**. Określ: 

- Nazwa punktu końcowego.
- Nazwa hosta punktu końcowego.
- Port punktu końcowego.
- Przestrzeń nazw magistrali usług, które chcesz użyć.

![Zrzut ekranu Utwórz nowy hybrydowego połączenia — okno dialogowe][4]

Każdy połączenia hybrydowego jest związany z przestrzeni nazw usługi Service Bus i każdej przestrzeni nazw usługi Service Bus znajduje się w regionie Azure. Należy próbować użyć nazw usługi Service Bus w tym samym regionie co aplikacji, aby uniknąć opóźnienia sieci powstaniu.

Jeśli chcesz usunąć połączenie hybrydowe z aplikacji, kliknij go prawym przyciskiem myszy i wybierz **rozłączenia**.  

Gdy połączenie hybrydowe zostanie dodany do aplikacji, można wyświetlić szczegóły na nim po prostu przez zaznaczenie go. 

![Zrzut ekranu hybrydowego szczegóły połączenia][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Tworzenie połączenia hybrydowego w portalu Azure przekazywania ###

Oprócz portalu doświadczenia z aplikacji można tworzyć z połączeń hybrydowych było możliwe, w portalu Azure przekazywania. Połączenia hybrydowego używanego przez usługę App Service musi:

* Wymaga autoryzacji klienta.
* Ma element metadanych o nazwie punktu końcowego, który zawiera połączenie hosta: port. jako wartość.

## <a name="hybrid-connections-and-app-service-plans"></a>Plany połączeń hybrydowych i usługi aplikacji ##

Połączenia hybrydowe funkcja jest dostępna tylko w Basic, Standard, Premium i izolowany cennik jednostki SKU. Istnieją powiązane planu cenowego ograniczenia.  

> [!NOTE] 
> Można tworzyć tylko nowych połączeń hybrydowych oparte na przekaźnika usługi Azure. Nie można utworzyć nowego połączenia hybrydowe BizTalk.
>

| Cennik planu | Liczba połączeń hybrydowych, której można używać w planie |
|----|----|
| Podstawowa | 5 |
| Standardowa | 25 |
| Premium | 200 |
| Izolowane | 200 |

Należy pamiętać, że plan usługi aplikacji pokazuje liczbę połączeń hybrydowych są używane i jakie aplikacje.  

![Właściwości planu usługi zrzut ekranu aplikacji][6]

Wybierz połączenia hybrydowego, aby wyświetlić szczegóły. Widać informacje wyświetlane w widoku aplikacji. Można również sprawdzić, jak wiele aplikacji w tym samym planie korzystają z tego połączenia hybrydowego.

Istnieje limit liczby punkty końcowe połączenia hybrydowego, których można użyć w planie usługi aplikacji. Każde połączenie hybrydowe używane, jednak można w dowolnej liczby aplikacji w tym planie. Na przykład pojedynczego połączenia hybrydowe używanego w pięciu osobnych aplikacji w planie usługi aplikacji jest traktowana jako jedno połączenie hybrydowe.

Brak dodatkowych kosztów za pomocą połączeń hybrydowych było możliwe. Aby uzyskać więcej informacji, zobacz [cennik usługi Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Menedżera połączeń hybrydowych ##

Funkcja połączenia hybrydowe wymaga agenta przekazywania w sieci, który jest hostem punktu końcowego połączenia hybrydowego. Agent przekazywania jest nazywany Menedżera połączeń hybrydowych (HCM). Aby pobrać HCM, z aplikacji w [portalu Azure][portal], wybierz pozycję **sieci** > **skonfiguruj punkty końcowe połączenia hybrydowego**.  

To narzędzie jest uruchamiane w systemie Windows Server 2012 lub nowszym. Po zainstalowaniu HCM działa jako usługa, która łączy się przekaźnik magistrali usługi, oparte na skonfigurowanym punktów końcowych. Połączenia z HCM są wychodzących na platformie Azure za pośrednictwem portu 443.    

Po zainstalowaniu HCM, możesz uruchomić HybridConnectionManagerUi.exe używać interfejsu użytkownika narzędzia. Ten plik znajduje się w katalogu instalacji Menedżera połączeń hybrydowych. W systemie Windows 10, możesz także po prostu wyszukać *interfejsu użytkownika Menedżera połączeń hybrydowych* w polu wyszukiwania.  

![Zrzut ekranu Menedżera połączeń hybrydowych][7]

Po uruchomieniu interfejsu użytkownika HCM, zostanie wyświetlony w pierwszej kolejności jest tabelę, która zawiera listę wszystkich połączeń hybrydowych, które są skonfigurowane przy użyciu tego wystąpienia HCM. Jeśli chcesz wprowadzić zmiany, najpierw uwierzytelnić się przy użyciu platformy Azure. 

Aby dodać jeden lub więcej połączeń hybrydowych było możliwe do Twojej HCM:

1. Uruchom HCM interfejsu użytkownika.
1. Wybierz **skonfigurować inne połączenie hybrydowe**.
![Zrzut ekranu przedstawiający konfigurowanie nowych połączeń hybrydowych][8]

1. Zaloguj się przy użyciu konta platformy Azure.
1. Wybierz subskrypcję.
1. Wybierz połączeń hybrydowych, który ma HCM do przekazywania.
![Zrzut ekranu przedstawiający połączeń hybrydowych][9]

1. Wybierz pozycję **Zapisz**.

Połączenia hybrydowe dodaniu będą teraz widoczne. Można również wybrać skonfigurowane połączenie hybrydowe, aby wyświetlić szczegóły.

![Zrzut ekranu przedstawiający szczegóły połączenia hybrydowego][10]

Do obsługi połączeń hybrydowych jest konfigurowana, HCM wymaga:

- TCP dostęp do platformy Azure przez porty 80 i 443.
- TCP dostęp do punktu końcowego połączenia hybrydowego.
- Zdolność do wyszukania DNS na hoście punktu końcowego i przestrzeń nazw magistrali usług.

HCM obsługuje połączeń hybrydowych i nowych połączeń hybrydowych BizTalk.

> [!NOTE]
> Azure przekazywania polega na gniazda sieci Web dla połączenia. Ta funkcja jest tylko dostępna w systemie Windows Server 2012 lub nowszym. Z tego powodu HCM nie jest obsługiwane na niczego starszych niż Windows Server 2012.
>

### <a name="redundancy"></a>Nadmiarowość ###

Każdy HCM może obsługiwać wiele połączeń hybrydowych. Ponadto wszystkie danego połączenia hybrydowego mogą być obsługiwane przez wiele HCMs. Domyślnym zachowaniem jest kierować ruchem między skonfigurowanego HCMs dla dowolnego danego punktu końcowego. Wysoka dostępność połączeń hybrydowych z sieci, należy uruchomić wiele HCMs na oddzielnych komputerach. 

### <a name="manually-add-a-hybrid-connection"></a>Ręcznie Dodaj połączenie hybrydowe ###

Aby włączyć osoby spoza subskrypcji do obsługi wystąpienia HCM dla danego połączenia hybrydowe, należy udostępnić parametry połączenia bramy połączenia hybrydowego z nimi. Można to zobaczyć we właściwościach połączenia hybrydowego, w [portalu Azure][portal]. Aby użyć tego ciągu, wybierz opcję **ręcznie wprowadź** w HCM, a następnie wklej w parametrach połączenia bramy.


## <a name="troubleshooting"></a>Rozwiązywanie problemów ##

Stan "Połączony" oznacza, że co najmniej jeden HCM skonfigurowano z tego połączenia hybrydowego i jest dostęp do usługi Azure. Jeśli stan połączenia hybrydowego nie powiedz **połączony**, połączenia hybrydowe nie jest skonfigurowany na dowolnym HCM, który ma dostęp do usługi Azure.

Podstawowym powodem, że klienci nie mogą łączyć się ich punktu końcowego jest, ponieważ punkt końcowy został określony przy użyciu adresu IP zamiast nazwy DNS. Jeśli aplikacja nie może połączyć się żądanego punktu końcowego i adres IP jest używany, przełącz się do przy użyciu nazwy DNS, która jest nieprawidłowa na hoście, którym jest uruchomiona HCM. Ponadto sprawdź, czy nazwy DNS rozpoznaje poprawnie na hoście, na którym jest uruchomiona HCM. Upewnij się, że istnieje łączność z hosta, na którym działa HCM do punktu końcowego połączenia hybrydowego.  

W usłudze App Service narzędzie tcpping może być wywoływany z konsoli narzędzia zaawansowane (Kudu). To narzędzie można stwierdzić, czy masz dostęp do punktu końcowego TCP, ale go nie informujące, jeśli użytkownik ma dostęp do punktu końcowego połączenia hybrydowego. Podczas korzystania z narzędzia w konsoli dla punktu końcowego połączenia hybrydowego są tylko potwierdzenie, że korzysta ona z kombinacji hosta: port.  

## <a name="biztalk-hybrid-connections"></a>Połączenia hybrydowe BizTalk ##

Starsze możliwości połączeń hybrydowych BizTalk został zamknięty do nowych połączeń hybrydowych BizTalk. Możesz nadal korzystać z istniejących połączeń hybrydowych BizTalk z aplikacjami, ale należy zmigrować do nowego połączenia hybrydowe, który używać przekaźnika usługi Azure. Korzyści w nową usługę BizTalk wersji należą:

- Żadne dodatkowe konto usług BizTalk — wersja nie jest wymagane.
- Protokół TLS to zamiast wersji 1.0 w wersji 1.2.
- Komunikacja jest przez porty 80 i 443 i nazwa DNS jest używana do Azure zamiast adresów IP i zakres dodatkowych portów.  

Aby dodać istniejące połączenie hybrydowe BizTalk do aplikacji, przejdź do aplikacji w [portalu Azure][portal]i wybierz **sieci** > **Konfiguruj punktów końcowych połączenia hybrydowego**. W tabeli klasycznego połączeń hybrydowych było możliwe, wybierz **Dodaj połączenie hybrydowe klasycznego**. Następnie można wyświetlić listę połączeń hybrydowych BizTalk.  


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

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
