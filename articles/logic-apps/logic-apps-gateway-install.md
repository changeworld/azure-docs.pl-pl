---
title: Instalowanie bramy danych lokalnych - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Aby uzyskać dostęp do źródła danych na lokalnym, instalowanie bramy danych lokalnych, transfer danych szybki i szyfrowania między źródłami danych lokalnie i logic apps
keywords: dostęp do danych lokalnych, transfer danych, szyfrowania, źródła danych
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 6c1c954209c08c4ce1262bdc1620755e4a3a6d7e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalowanie bramy danych lokalnych dla usługi Azure Logic Apps

Przed aplikacje logiki można uzyskać dostępu do źródeł danych na lokalnym, należy zainstalować i skonfigurować bramę danych na lokalnym. Brama działa jako mostka zapewnia transfer danych szybki i szyfrowania między systemami lokalnymi i aplikacje logiki. Brama przekazuje dane z lokalnych źródeł w kanałach zaszyfrowane za pomocą usługi Azure Service Bus. Cały ruch pochodzi jako bezpieczny ruch wychodzący z agentem bramy. Dowiedz się więcej o [działanie bramy danych](#gateway-cloud-service).

Brama obsługuje połączenia z tych źródeł danych na lokalnym:

*   BizTalk Server 2016
*   DB2  
*   System plików
*   Informix
*   MQ
*   MySQL
*   Baza danych Oracle
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   Sharepoint
*   Oprogramowanie SQL Server
*   Teradata

Te kroki pokazują, jak najpierw zainstaluj bramę danych lokalnych przed [skonfigurować połączenie między bramą a aplikacje logiki](./logic-apps-gateway-connection.md). Aby uzyskać więcej informacji o obsługiwanych łączników, zobacz [łączniki dla usługi Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list). 

Aby uzyskać informacje o sposobie używania bramy z innymi usługami, zobacz następujące artykuły:

*   [Microsoft Power BI lokalnej bramy danych](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure bramy danych lokalnych usług Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Brama danych lokalnych Flow firmy Microsoft](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Brama danych lokalnych PowerApps firmy Microsoft](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Wymagania

**Minimum**

* .NET 4.5 framework
* 64-bitowej wersji systemu Windows 7 lub Windows Server 2008 R2 (lub nowszy)

**Zalecane**

* 8 rdzeni procesora CPU
* 8 GB pamięci
* 64-bitowej wersji systemu Windows 2012 R2 (lub nowszy)

**Ważne uwagi**

* Instalowanie bramy danych lokalnych tylko na komputerze lokalnym.
Nie można zainstalować bramę na kontrolerze domeny.

   > [!TIP]
   > Nie trzeba zainstalować bramę na tym samym komputerze co źródła danych. Aby zminimalizować czas oczekiwania, możesz zainstalować bramę, jak najbliżej ze źródłem danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

* Nie należy instalować bramy na komputerze, który zostanie wyłączony, przechodzi w stan uśpienia, lub nie łączyć się z Internetem, ponieważ brama nie można uruchomić w tych warunkach. Ponadto w sieci bezprzewodowej może pogorszyć wydajność bramy.

* Podczas instalacji, musisz zalogować się przy użyciu [konto służbowe](https://docs.microsoft.com/azure/active-directory/sign-up-organization) zarządzanym przez usługę Azure Active Directory (Azure AD), nie konta Microsoft.

  > [!TIP]
  > Jeśli chcesz użyć konta Microsoft, które ma Visual Studio z subskrypcją MSDN, najpierw [Utwórz katalog (dzierżawcy) w usłudze Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) z konta Microsoft, lub użyj domyślnego katalogu. Dodawanie użytkownika za pomocą hasła do katalogu, a następnie nadaj tej dostęp użytkownika do subskrypcji. Następnie można zalogować podczas instalacji bramy z tej nazwy użytkownika i hasła.

  Należy użyć tego samego konta firmowego lub szkolnego później w portalu Azure, podczas tworzenia i skojarzyć z instalacją bramy zasobów bramy. Podczas tworzenia połączenia między aplikację logiki i lokalnego źródła danych, następnie wybierz tego zasobu bramy. [Dlaczego trzeba użyć roboczych usługi Azure AD lub konta służbowego?](#why-azure-work-school-account)

  > [!TIP]
  > Jeśli konta dla oferty usługi Office 365, a nie dostarczył rzeczywiste służbowego adresu e-mail, adresu logowania może wyglądać jeff@contoso.onmicrosoft.com. 

* Jeśli masz istniejącą bramę skonfigurowanego z Instalatorem, która jest starsza niż wersja 14.16.6317.4, nie można zmienić lokalizacji bramy sieci, uruchamiając najnowszą wersję Instalatora. Jednak można użyć najnowszą wersję Instalatora, aby skonfigurować nową bramę z lokalizacji, w której chcesz zamiast tego.
  
  Jeśli Instalator bramy, która jest starsza niż wersja 14.16.6317.4, ale nie zostały zainstalowane bramy można jeszcze miejsca, pobranie i użycie najnowszą wersję Instalatora.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz i uruchom Instalator bramy na komputerze lokalnym](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Przeczytaj i zaakceptuj warunki użytkowania i ochrony prywatności.

3. Określ ścieżkę na komputerze lokalnym, na którym chcesz zainstalować bramę.

4. Po wyświetleniu monitu zaloguj się przy użyciu konta służbowego, nie konta Microsoft Azure pracy.

   ![Zaloguj się przy użyciu pracy Azure lub konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Teraz zarejestrować zainstalowana brama z [usługi bramy w chmurze](#gateway-cloud-service). Wybierz **zarejestrować nową bramę na tym komputerze**.

   Usługi w chmurze bramy są szyfrowane i przechowywane poświadczenia źródła danych, a szczegóły bramy. 
   Usługa również kieruje lokalnie zapytań i ich wyników między aplikację logiki, brama lokalna danych i źródła danych.

6. Podaj nazwę dla tej instalacji bramy. Twórz klucza odzyskiwania, a następnie potwierdź klucz odzyskiwania. 

   > [!IMPORTANT] 
   > Klucz odzyskiwania musi zawierać co najmniej osiem znaków. Upewnij się, czy zapisać, a klucz należy przechowywać w bezpiecznym miejscu. Należy również ten klucz umożliwia migrację, Przywróć lub Przejmij na istniejącą bramę.

   1. Aby zmienić domyślny region usługi bramy w chmurze i używane przez instalację bramy usługi Azure Service Bus, wybierz **zmienić regionu**.

      ![Zmiana regionu](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      Domyślnego regionu jest regionu skojarzonego z dzierżawą usługi Azure AD.

   2. Otwórz na następne okienko **wybierz Region** aby wybrać inny region.

      ![Wybierz inny region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Na przykład wybierz region, w tym samym jako aplikację logiki, lub wybierz region najbliższy do lokalnego źródła danych, aby ograniczyć czas oczekiwania. Bramy aplikacji logiki i zasobów mogą mieć różnych lokalizacjach.

      > [!IMPORTANT]
      > Ten region nie można zmienić po zakończeniu instalacji. Ten region również określa i ogranicza lokalizacji, w którym można utworzyć zasobów platformy Azure dla bramy. Dlatego podczas tworzenia zasobu bramy na platformie Azure, upewnij się, że lokalizacja zasobu zgodny z ustawieniami regionalnymi wybranym podczas instalacji bramy.
      > 
      > Jeśli chcesz później przy użyciu w innym regionie bramy, należy skonfigurować nową bramę.

   3. Gdy wszystko jest gotowe, wybierz pozycję **gotowe**.

7. Teraz wykonaj następujące kroki w portalu Azure, można więc [tworzenie zasobów platformy Azure dla bramy](../logic-apps/logic-apps-gateway-connection.md). 

Dowiedz się więcej o [działanie bramy danych](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migracja, Przywróć lub Przejmij na istniejącą bramę

Aby wykonać te zadania, musi mieć klucz odzyskiwania, który został określony podczas instalacji bramy.

1. Z menu Start komputera, wybierz **bramy danych lokalnych**.

2. Po otwarciu Instalator, zaloguj się przy użyciu tego samego pracy Azure konta służbowego, który był wcześniej używany do instalacji bramy.

3. Wybierz **migracji, Przywróć lub Przejmij istniejącą bramę**.

4. Podaj nazwę i odzyskiwanie klucza bramy, który chcesz migrować, Przywróć lub Przejmij na.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Konto usługi systemu Windows

Brama danych lokalna działa jako usługa systemu Windows i jest skonfigurowany do użycia `NT SERVICE\PBIEgwService` dla systemu Windows usługi poświadczeń logowania. Domyślnie bramy ma prawo "Zaloguj jako usługa" dla komputera, w którym zainstalowano bramę. Do tworzenia i obsługi bramy w portalu Azure, konto usługi systemu Windows musi mieć co najmniej **współautora** uprawnienia. 

> [!NOTE]
> Konto usługi systemu Windows różni się od konto używane do łączenia się z danymi lokalnymi źródeł, a z platformy Azure służbowe konto używane do logowania do usługi w chmurze.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Ponownie uruchom bramę

Podobnie jak inne usługi systemu Windows można uruchomić i zatrzymać usługę na wiele sposobów. Można na przykład, otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze, na którym jest uruchomiona brama i uruchamiania obu tych poleceń:

* Aby zatrzymać usługę, uruchom następujące polecenie:
  
    `net stop PBIEgwService`

* Aby uruchomić usługę, uruchom następujące polecenie:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Konfigurowanie zapory lub serwera proxy

Brama tworzy wychodzące połączenie [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Aby podać informacje o serwerze proxy dla bramy, zobacz [Skonfiguruj ustawienia serwera proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Aby sprawdzić, czy zapory lub serwera proxy, może zablokować połączenia, upewnij się, czy komputer faktycznie można połączyć się z Internetem i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Uruchom to polecenie z wiersza polecenia programu PowerShell:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> To polecenie tylko testy, łączności sieciowej i łączności do usługi Azure Service Bus. To polecenie nie ma związek z bramy lub bramy usługi w chmurze są szyfrowane i przechowywane poświadczenia i szczegóły bramy. 
>
> Ponadto to polecenie jest tylko dostępne w systemie Windows Server 2012 R2 lub nowszy oraz Windows 8.1 lub nowszy. We wcześniejszych wersjach systemu operacyjnego można użyć programu Telnet do testowania łączności. Dowiedz się więcej o [Azure Service Bus i hybrydowe rozwiązania](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Wyniki powinny wyglądać podobnie do tego przykładu:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Jeśli **TcpTestSucceeded** nie jest ustawiony na **True**, może zostać zablokowany przez zaporę. Jeśli chcesz mieć kompleksowe, zamiast **ComputerName** i **portu** wartości z wartości na liście [skonfigurować porty](#configure-ports) w tym artykule.

Zapora może również zablokować połączeń, które powoduje Azure Service Bus w centrach danych platformy Azure. W przypadku tego scenariusza należy zatwierdzić (odblokuj) wszystkie adresy IP dla tych centrach danych w danym regionie. Dla tych adresów IP [pobrania listy adresów IP platformy Azure w tym miejscu](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Skonfiguruj porty

Brama tworzy wychodzące połączenie [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) i komunikuje się portów wychodzących: TCP 443 (ustawienie domyślne), 5671, 5672, 9350 za pośrednictwem 9354. Brama nie wymaga portów przychodzących. Dowiedz się więcej o [Azure Service Bus i hybrydowe rozwiązania](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| Nazwy domen | Porty wyjściowe | Opis |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Zaawansowane kolejkowania wiadomości protokołu (protokół AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Obiekty nasłuchujące na przekaźnik magistrali usług za pośrednictwem protokołu TCP (wymaga 443 dla tokenu przejęcie kontroli dostępu) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Używany do sprawdzania łączności z Internetem, gdy brama jest nieosiągalny przez usługę Power BI. | 
||||

Jeśli masz zatwierdzić adresów IP zamiast domeny, możesz pobrać i użyć [Microsoft Azure Datacenter IP zakresów listy](https://www.microsoft.com/download/details.aspx?id=41653). W niektórych przypadkach połączenia usługi Azure Service Bus są nawiązywane z adresu IP, a nie w pełni kwalifikowanych nazw domen.

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-data-gateway-work"></a>Jak działa bramę danych?

Brama danych umożliwia szybkie i bezpieczne komunikację między aplikację logiki, usługi bramy w chmurze i lokalne źródła danych. 

![Diagram-for-on-premises-Data-Gateway-Flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Tak, gdy użytkownik w chmurze współdziała z elementem, który jest podłączony do lokalnego źródła danych:

1. Usługa w chmurze bramy tworzy zapytanie, wraz z zaszyfrowane poświadczenia dla źródła danych i wysyła zapytanie do kolejki dla bramy do przetworzenia.

2. Usługi w chmurze bramy analizuje zapytania i wypychanie żądania do usługi Azure Service Bus.

3. Brama danych lokalnych sonduje Azure Service Bus dla żądań oczekujących.

4. Bramy pobiera kwerendy odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłem danych przy użyciu tych poświadczeń.

5. Brama wysyła zapytanie do źródła danych do wykonania.

6. Wyniki są wysyłane ze źródła danych, wróć do bramy, a następnie do usługi bramy w chmurze. Usługi w chmurze bramy następnie używa wyników.

<a name="faq"></a>

## <a name="tenant-level-administration"></a>Administracja poziomu dzierżawy 

Nie ma żadnych jednego miejsca, w której Administratorzy dzierżawy mogą zarządzać wszystkich bram, które inni użytkownicy mają zainstalowany i skonfigurowany.  Jeśli jesteś administratorem dzierżawy, zaleca się poproszenie użytkowników w organizacji na dodawanie użytkownika z uprawnieniami administratora do każdej instalacji bramy. Dzięki temu można zarządzać wszystkich bram w organizacji za pomocą strony Ustawienia bramy lub za pomocą [poleceń programu PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

**Q**: należy bramy dla źródeł danych w chmurze, np. SQL Azure? <br/>
**A**: nie. Brama łączy się tylko źródła danych lokalnych.

**Q**: czy bramy musi być zainstalowany na tym samym komputerze jako źródło danych? <br/>
**A**: nie. Brama łączy się ze źródłem danych, korzystając z informacji połączenia, który został dostarczony. Jako aplikacja klienta, w tym sensie, należy wziąć pod uwagę bramy. Bramy wystarczy możliwość nawiązywania nazwę serwera, który został dostarczony.

<a name="why-azure-work-school-account"></a>

**Q**: Dlaczego mam Azure pracy lub szkołą konta? <br/>
**A**: można używać Praca Azure lub tylko konta służbowego, po zainstalowaniu bramy danych lokalnych. Twoje konto logowania są przechowywane w dzierżawie, który jest zarządzany przez usługę Azure Active Directory (Azure AD). Główna nazwa użytkownika konta usługi Azure AD (UPN) jest zgodna zwykle adres e-mail.

**Q**: moich poświadczeń przechowywania? <br/>
**A**: poświadczenia dla źródła danych są szyfrowane i przechowywane w usłudze w chmurze bramy. Poświadczenia są odszyfrowywane w bramie danych lokalnych.

**Q**: istnieją wszelkie wymagania dotyczące przepustowości sieci? <br/>
**A**: zaleca się, że połączenie sieciowe ma dobrej przepływności. Każde środowisko jest inne, a ilość danych wysyłanych ma wpływ na wyniki. Użycie usługi ExpressRoute może pomóc do zapewnienia poziomu przepływności między lokalnymi i centrach danych platformy Azure.
Można użyć aplikacji Azure szybkości testowanie narzędzia innej firmy by zmierzyć przepustowość sieci.

**Q**: co to jest czas oczekiwania na wykonywanie zapytań do źródła danych z bramy? Co to jest najlepszym architektura? <br/>
**A**: do zmniejszenia opóźnienia sieci, należy zainstalować bramę maksymalnie zbliżony źródła danych, jak to możliwe. Po zainstalowaniu bramy w źródle danych rzeczywistych, to zbliżeniowe minimalizuje opóźnienie wprowadzane. Rozważ zbyt centrach danych. Na przykład jeśli usługa używa datacenter zachodnie stany USA, a masz programu SQL Server w maszynie Wirtualnej platformy Azure, maszyny Wirtualnej Azure należy w zachodnie stany USA zbyt. Ta bliskości zmniejsza opóźnienia i pozwala uniknąć opłaty za wyjście na maszynie Wirtualnej Azure.

**Q**: jak są wyniki odsyłane do chmury? <br/>
**A**: wyniki są wysyłane za pośrednictwem usługi Azure Service Bus.

**Q**: istnieją wszystkie połączenia przychodzące do bramy w chmurze? <br/>
**A**: nie. Brama korzysta z połączenia wychodzące do usługi Azure Service Bus.

**Q**: co zrobić, jeśli zablokowanie połączeń wychodzących? Co trzeba otworzyć? <br/>
**A**: Zobacz porty i hostów, które używa bramy.

**Q**: co to jest rzeczywiste usługi systemu Windows o nazwie?<br/>
**A**: usług w bramie jest nazywany Usługa bramy Power BI Enterprise.

**Q**: można uruchomić bramy usługi systemu Windows przy użyciu konta usługi Azure Active Directory? <br/>
**A**: nie. Usługa systemu Windows musi mieć prawidłowe konto systemu Windows. Domyślnie usługa jest uruchamiana z identyfikatorem SID usługi NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

**Q**: jakie opcje są dostępne dla odzyskiwania po awarii? <br/>
**A**: klucz odzyskiwania służy do przywracania, lub Przenieś bramy. Po zainstalowaniu bramy, należy określić klucz odzyskiwania.

**Q**: co to jest korzyść klucz odzyskiwania? <br/>
**A**: klucz odzyskiwania pozwala przeprowadzić migrację lub odzyskiwanie po awarii ustawienia bramy.

**Q**: czy jest planowane umożliwiających realizację scenariuszy wysokiej dostępności z bramą? <br/>
**A**: niektóre łączniki obsługuje scenariuszach wysokiej dostępności, na przykład łącznik systemu plików i inne osoby w taki sposób. Aby uzyskać więcej informacji, zobacz [klastrów wysokiej dostępności dla bramy danych lokalnych](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: jak wyświetlić co zapytania są wysyłane do lokalnego źródła danych? <br/>
**A**: można włączyć funkcja śledzenia zapytań, który zawiera zapytania, które są wysyłane. Pamiętaj, aby zmienić zapytania śledzenia wstecz do oryginalnej wartości po zakończeniu rozwiązywania problemów. Jeśli pozostanie włączona funkcja śledzenia zapytań tworzy większe dzienniki.

Można też przyjrzeć się narzędzia, które ma źródła danych śledzenia zapytań. Na przykład można użyć zdarzeń rozszerzonych lub profilera SQL dla programu SQL Server i usług Analysis Services.

**Q**: gdzie są dzienniki bramy? <br/>
**A**: Zobacz narzędzia w dalszej części tego artykułu.

### <a name="update-to-the-latest-version"></a>Aktualizowanie do najnowszej wersji

Wiele problemów można powierzchni, gdy wersja bramy stanie się nieaktualna. Dobrym rozwiązaniem ogólne upewnij się, że używasz najnowszej wersji. Brama nie były aktualizowane przez miesiąc lub dłużej, może być zaleca się zainstalowanie najnowszej wersji bramy i zobacz, czy można odtworzyć problem.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Błąd: Nie można dodać użytkownika do grupy. (Użytkownicy dzienników wydajności PBIEgwService-2147463168)

Ten błąd może pobrać, Jeśli spróbujesz zainstalować bramę na kontrolerze domeny, który nie jest obsługiwany. Upewnij się, że wdrożyć bramę na komputerze, na którym nie jest kontrolerem domeny.

## <a name="tools"></a>Narzędzia

### <a name="collect-logs-from-the-gateway-configurer"></a>Zbieranie dzienników z configurer bramy

Można zbierać kilka dzienniki bramy. Zawsze uruchamiaj z dziennikami!

#### <a name="installer-logs"></a>Dzienniki Instalatora

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Dzienniki konfiguracji

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Dzienniki usługi bramy przedsiębiorstwa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Dzienniki zdarzeń

Można znaleźć w dziennikach brama zarządzania danymi i PowerBIGateway w obszarze **Dzienniki aplikacji i usług**.

### <a name="fiddler-trace"></a>Śledzenie fiddler

[Fiddler](http://www.telerik.com/fiddler) to bezpłatne narzędzie ze strony firmy Telerik, który monitoruje ruch HTTP. Widać tego ruchu z usługi Power BI z komputera klienta. Ta usługa mogą być wyświetlane błędy oraz inne powiązane informacje.

## <a name="next-steps"></a>Kolejne kroki
    
* [Połącz się z lokalnymi danymi z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
