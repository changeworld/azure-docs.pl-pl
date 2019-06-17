---
title: Instalowanie lokalnej bramy danych — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zanim uzyskujesz dostęp do danych w środowisku lokalnym, z usługi Azure Logic Apps, pobieranie i instalowanie lokalnej bramy danych
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 10a6e5c33f6a3c23d98e6eb3380de0d6dc6ac216
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544485"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Zainstaluj lokalną bramę danych dla usługi Azure Logic Apps

Przed nawiązaniem połączenia ze źródłami danych lokalnych z usługi Azure Logic Apps, Pobierz i zainstaluj lokalną bramę danych na komputerze lokalnym. Brama działa jako mostek zapewniający szybki transfer i danych szyfrowania między źródłami danych w środowisku lokalnym (nie w chmurze) i aplikacji usługi logic apps. W tym artykule pokazano, jak można pobrać, zainstalować i skonfigurować bramę danych lokalnych. 

Można użyć tego samego instalacji bramy z innymi usługami, takie jak Power BI, Microsoft Flow, PowerApps i usług Azure Analysis Services. Dowiedz się więcej o [sposobu działania bramy danych](#gateway-cloud-service).

<a name="supported-connections"></a>

Brama obsługuje [łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) w usłudze Azure Logic Apps dla tych źródeł danych:

*   BizTalk Server 2016
*   System plików
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Baza danych Oracle
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   Oprogramowanie SharePoint Server
*   Oprogramowanie SQL Server
*   Teradata

Aby uzyskać informacje o sposobie używania bramy z innymi usługami, zobacz następujące artykuły:

* [Brama danych lokalnych usługi Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Brama danych lokalnych Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Brama danych lokalnych Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Usługa Azure Analysis Services na lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* A [konto służbowe](../active-directory/fundamentals/sign-up-organization.md) zawierający [subskrypcji platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Podczas instalacji bramy możesz Zaloguj się do tego konta, dzięki czemu można skojarzyć instalacji bramy z subskrypcją platformy Azure. 
  Później możesz również użyć tego samego konta podczas tworzenia zasobu platformy Azure dla instalacji bramy w witrynie Azure portal. 
  Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Poniżej przedstawiono wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  * .NET Framework 4.5.2
  * 64-bitowej wersji systemu Windows 7 lub Windows Server 2008 R2 (lub nowszy)

  **Zalecane wymagania**

  * 8-rdzeniowy Procesor
  * 8 GB pamięci RAM
  * 64-bitowej wersji systemu Windows Server 2012 R2 (lub nowszym)

* **Istotne zagadnienia**

  * Lokalna brama danych można zainstalować tylko na komputerze lokalnym, a nie na kontrolerze domeny. Jednakże nie trzeba zainstalować bramę na tym samym komputerze co źródło danych. Ponadto należy tylko jedną bramę dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować czas oczekiwania, możesz zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze, który jest połączony z Internetem, zawsze włączone i *nie* Przejdź w tryb uśpienia. W przeciwnym razie nie można uruchomić bramy. 
  Ponadto może to spowodować obniżenie wydajności w sieci bezprzewodowej.

  * Podczas instalacji, można się zarejestrować tylko przy użyciu [konto służbowe](../active-directory/sign-up-organization.md) zarządzanym przez usługę Azure Active Directory (Azure AD), na przykład @contoso.onmicrosoft.com, a nie B2B w usłudze Azure konta (Gość) lub osobiste Microsoft konta, takie jak @hotmail.com lub @outlook.com. 
  Upewnij się, że używasz tego samego konta logowania po zarejestrowaniu instalację bramy w witrynie Azure portal, tworząc zasobu bramy. 
  Następnie można wybrać tego zasobu bramy, podczas tworzenia połączenia z aplikacji logiki ze źródłem danych lokalnych. 
  [Dlaczego należy używać usługi Azure AD praca lub konta służbowego?](#why-azure-work-school-account)

  > [!TIP]
  > Jeśli zarejestrowali się do oferty usługi Office 365 i nie zapewnia rzeczywistego służbowego adresu e-mail, Niewykluczone, że adres logowania, który wygląda następująco: `username@domain.onmicrosoft.com` 
  >
  > Aby użyć konta Microsoft, które ma [subskrypcji programu Visual Studio standardowej](https://visualstudio.microsoft.com/vs/pricing/)najpierw [Utwórz katalog (dzierżawca) w usłudze Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), lub użyj domyślnego katalogu z Twoim kontem Microsoft. 
  > Dodawanie użytkownika za pomocą hasła do katalogu, a następnie udzielić użytkownikom dostępu do Twojej subskrypcji. 
  > Można następnie zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

  * Region, który wybierzesz instalacji bramy Określa lokalizację, gdzie możesz później zarejestrować bramy na platformie Azure, tworząc zasobu platformy Azure. 
  Podczas tworzenia tego zasobu bramy na platformie Azure, należy wybrać *tego samego* lokalizacji instalacji bramy. Domyślnym regionem jest tę samą lokalizację dzierżawy usługi Azure AD, która zarządza kontem platformy Azure, ale można zmieniać lokalizację podczas instalacji bramy.

  * Jeśli masz już skonfigurowaną z Instalatorem wcześniejszej niż wersja 14.16.6317.4 bramy, nie można zmienić lokalizacji bramy sieci uruchomić najnowszą wersję Instalatora. Jednak można użyć najnowszą wersję Instalatora, aby zdefiniować nową bramę z lokalizacji, w których chcesz zamiast tego.
  
    Jeśli masz Instalatora bramy, która jest starsza niż wersja 14.16.6317.4, ale nie został jeszcze zainstalowany bramy można jeszcze, pobrać i zamiast tego użyj najnowszą wersję Instalatora.

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Lokalna brama danych obsługuje wysoką dostępność, gdy masz więcej niż jeden instalacji bramy i ustawić je jako klastry. Jeśli masz istniejącą bramę, po przejściu do tworzenia bramy innej, można opcjonalnie utworzyć klastry wysokiej dostępności. Tych klastrów organizować bram w grupach, które mogą pomóc uniknąć pojedynczych punktów awarii. Ponadto wszystkie łączniki bramy danych lokalnych obsługują obecnie wysokiej dostępności.

Aby korzystać z lokalnej bramy danych, należy przejrzeć wymagania i uwagi:

* Musi już mieć co najmniej jedną bramę instalację w ramach tej samej subskrypcji platformy Azure jako bramy podstawowej i klucz odzyskiwania dla tej instalacji. 

* Bramy podstawowej musi być uruchomiona aktualizacja bramy z listopada 2017 r. lub później.

Po spełnieniu tych wymagań, po utworzeniu bramy następnym wybierz **dodać do istniejącego klastra bramy**, wybierz bramy podstawowej dla klastra i podaj klucz odzyskiwania dla tej bramy podstawowej.
Aby uzyskać więcej informacji, zobacz [klastry wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz, Zapisz i uruchom Instalatora bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

2. Zaakceptuj domyślną ścieżkę instalacji lub określ lokalizację na komputerze, na którym chcesz zainstalować bramę.

3. Przejrzyj i zaakceptuj warunki użycia i zasady zachowania poufności, a następnie wybierz **zainstalować**.

   ![Zaakceptuj warunki użycia i zasady zachowania poufności](./media/logic-apps-gateway-install/accept-terms.png)

4. Po pomyślnym zainstalowaniu bramy należy podać adres e-mail dla swojego konta firmowego lub szkolnego i wybierz polecenie **Zaloguj**.

   ![Zaloguj się przy użyciu konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Wybierz **Zarejestruj nową bramę na tym komputerze** > **dalej**, który rejestruje instalację bramy z [Usługa bramy w chmurze](#gateway-cloud-service). 

   ![Rejestrowanie bramy](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Podaj te informacje dotyczące instalacji bramy:

   * Nazwę, która ma dla tej instalacji 

   * Klucz odzyskiwania, który chcesz utworzyć, który musi mieć co najmniej osiem znaków

     > [!IMPORTANT]
     > Zapisz i zachowaj klucz odzyskiwania w bezpiecznym miejscu. Będzie on potrzebny po zmianie lokalizacji bramy lub podczas migracji, odzyskiwanie lub przejmowanie istniejącej bramy.

   * Potwierdzenie klucz odzyskiwania 

     ![Konfigurowanie bramy](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Sprawdź, wybranego regionu Usługa bramy w chmurze i Azure Service Bus, który jest używany przez instalację bramy. 

   ![Sprawdź region](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Aby zmienić ten region, po zakończeniu instalowania bramy, potrzebny jest klucz odzyskiwania dla tej instalacji bramy. Należy również odinstalować i ponownie zainstaluj bramę. Aby uzyskać więcej informacji, zobacz [zmienić lokalizację, migracja, odzyskiwanie lub przejmowanie istniejącej bramy](#update-gateway-installation).

   *Dlaczego zmieniono regionie dla Twojej instalacji bramy?* 

   Na przykład aby zmniejszyć czas oczekiwania, może zmienić regionu bramy sieci na tym samym regionie, co aplikacja logiki. 
   Lub możesz wybrać region najbliżej źródła danych w środowisku lokalnym. 
   Twoje *zasobu bramy na platformie Azure* i aplikacja logiki może mieć różne lokalizacje.

8. Aby zaakceptować domyślnym regionem, wybierz **Konfiguruj**. Lub, aby zmienić domyślny region, wykonaj następujące kroki:

   1. Obok bieżący region, wybierz **Zmień Region**. 

      ![Zmień region](./media/logic-apps-gateway-install/change-region.png)

   2. Na następnej stronie Otwórz **wybierz Region** listy, wybierz region i wybierz polecenie **gotowe**.

      ![Wybierz inny region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Po wyświetleniu strony potwierdzenia wybierz **Zamknij**. 

   Instalator potwierdza, że brama jest obecnie dostępna online i gotowa do użycia.

   ![Zakończono bramy](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Teraz zarejestrować bramę na platformie Azure dzięki [tworzenie zasobu platformy Azure dla Twojej instalacji bramy](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmienić lokalizację, migrowanie, przywracanie lub przejmowanie istniejącej bramy

Jeśli musisz zmienić lokalizację bramy sieci, przeniesienia instalacji programu bramy na nowym komputerze odzyskać uszkodzoną bramę lub przejęcie na własność dla istniejącej bramy, należy się klucz odzyskiwania, które zostało podane podczas instalacji bramy. Ta akcja odłączy stara brama.

1. Z tego komputera **Panelu sterowania**, przejdź do **programy i funkcje**. Na liście programów zaznacz **lokalnej bramy danych**, a następnie wybierz **Odinstaluj**.

2. [Ponownie zainstaluj lokalną bramę danych](https://aka.ms/on-premises-data-gateway-installer).

3. Po otwarciu Instalatora, zaloguj się przy użyciu tego samego konta służbowego, które zostało wcześniej użyte do zainstalowania bramy.

4. Wybierz **migracji, przywracania lub przejęcia istniejącej bramy**, a następnie wybierz **dalej**.

   ![Wybierz pozycję "Migracji, przywracania lub przejęcia istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. W obszarze **dostępnych bram** lub **dostępne klastry bram**, wybierz opcję instalacji bramy, które chcesz zmienić. Wprowadź klucz odzyskiwania dla instalacji bramy. 

   ![Wybierz bramy podstawowej](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Aby zmienić region, wybierz **regionie, zmienić** i nowy region.

7. Gdy wszystko będzie gotowe, wybierz pozycję **Konfiguruj**.

## <a name="configure-proxy-or-firewall"></a>Konfigurowanie serwera proxy lub zapory

Lokalna brama danych tworzy połączenie wychodzące do [usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Jeśli środowisko pracy wymaga, że ruch jest przesyłany za pośrednictwem serwera proxy w celu uzyskania dostępu do Internetu, to ograniczenie może uniemożliwiać bramie danych nawiązanie Usługa bramy w chmurze. Aby ustalić, czy sieć używa serwera proxy, należy przejrzeć ten artykuł w superuser.com: 

[Jak sprawdzić, jakiego serwera proxy używam? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Aby podać informacje o serwerze proxy dla bramy, zobacz [Skonfiguruj ustawienia serwera proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Aby sprawdzić, czy usługi serwera proxy lub zapory mogą blokować połączenia, upewnij się, czy komputer może faktycznie połączenie z Internetem i [usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/). W wierszu polecenia programu PowerShell uruchom następujące polecenie:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> To polecenie spowoduje przetestowanie tylko łączności sieciowej i łączności z usługą Azure Service Bus. Polecenie nie wykonuje żadnych czynności przy użyciu bramy lub Usługa bramy w chmurze i przechowuje swoje poświadczenia i informacje szczegółowe bramy. 
>
> Ponadto to polecenie jest tylko dostępne w systemie Windows Server 2012 R2 lub nowszy i Windows 8.1 lub nowszym. We wcześniejszych wersjach systemu operacyjnego można użyć Telnet, aby przetestować łączność. Dowiedz się więcej o [usługi Azure Service Bus i hybrydowych rozwiązań](../service-bus-messaging/service-bus-messaging-overview.md).

Wyniki powinny wyglądać podobnie do tego przykładu z **TcpTestSucceeded** równa **True**:

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

Jeśli **TcpTestSucceeded** nie jest ustawiony na **True**, brama może być blokowany przez zaporę. Pełne, należy zamienić **ComputerName** i **portu** wartości z wartości na liście [skonfigurować porty](#configure-ports) w tym artykule.

Zapora może także blokować połączenia, które usługi Azure Service Bus nawiązuje do centrów danych platformy Azure. W przypadku tego scenariusza zatwierdzania (należy je odblokować) wszystkie adresy IP tych centrów danych w Twoim regionie. Dla tych adresów IP [pobieranie listy adresów IP platformy Azure w tym miejscu](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Konfigurowanie portów

Brama tworzy połączenie wychodzące do [usługi Azure Service Bus](https://azure.microsoft.com/services/service-bus/) i komunikuje się na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 do 9354. Brama nie wymaga portów przychodzących. Dowiedz się więcej o [usługi Azure Service Bus i hybrydowych rozwiązań](../service-bus-messaging/service-bus-messaging-overview.md).

Brama używa te w pełni kwalifikowanych nazw domen:

| Nazwy domen | Porty ruchu wychodzącego | Opis | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Używany do uwierzytelniania w zależności od konfiguracji. | 
| *.msftncsi.com | 443 | Używane do testowania łączności z Internetem, gdy brama jest nieosiągalna przez usługę Power BI. | 
| *.servicebus.windows.net | 443, 9350-9354 | Odbiorniki usługi Service Bus Relay za pośrednictwem protokołu TCP (wymaga portu 443 uzyskania tokenu kontroli dostępu) | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

W niektórych przypadkach połączenia usługi Azure Service Bus są przekazywane przy użyciu adresów IP, a nie w pełni kwalifikowanych nazw domen. Tak możesz chcieć umieścić na liście dozwolonych adresów IP dla używanego regionu danych w zaporze. Aby umieścić na liście dozwolonych adresów IP zamiast domen, możesz pobrać i używać [listy zakresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adresy IP na tej liście znajdują się w [Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notacji.

### <a name="force-https-communication-with-azure-service-bus"></a>Wymuszanie komunikacji HTTPS z usługą Azure Service Bus

Niektóre serwery proxy zezwala na ruch za pośrednictwem tylko do portów 80 i 443. Domyślnie komunikacja z usługą Azure Service Bus odbywa się na porty inne niż 443.
Możliwość wymuszenia na bramie, nawiązać połączenia z usługą Azure Service Bus za pośrednictwem protokołu HTTPS zamiast bezpośredniego połączenia TCP, ale wykonanie tej tak może znacznie zmniejszyć wydajność. Aby wykonać to zadanie, wykonaj następujące kroki:

1. Przejdź do lokalizacji dla klienta bramy danych lokalnych, co zwykle można znaleźć tutaj: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   W przeciwnym razie można znaleźć lokalizacji klienta, otwórz konsolę usługi na tym samym komputerze, znajdowanie **Usługa bramy danych lokalnych**i wyświetlić **ścieżka do pliku wykonywalnego** właściwości.

2. Otwórz *konfiguracji* pliku: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Zmiana **ServiceBusSystemConnectivityModeString** wartość z **Autowykrywanie** do **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Konto usługi Windows

Na komputerze, na którym instalujesz bramę danych lokalnych brama działa jako konto usługi Windows o nazwie "On-premises data gateway service". Jednak brama używa nazwy "NT SERVICE\PBIEgwService" do przedstawienia poświadczeń "Logowanie jako" konto. Domyślnie brama ma uprawnienia "Logowanie jako usługa" na komputerze, na którym instalujesz bramę. Konto usługi Windows bramy zwykle różni się od konta używanego do połączeń z lokalnymi źródłami danych oraz od pracy lub nauki, którego używasz do logowania do usług w chmurze.

Umożliwia tworzenie i obsługa bramy w witrynie Azure portal, to konto usługi Windows musi mieć co najmniej **Współautor** uprawnienia. Aby sprawdzić te uprawnienia, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Uruchom ponownie bramę

Brama danych działa jako usługa okna, tak jak wszystkich innych usług Windows można uruchamiać i zatrzymywać bramy na różne sposoby. Można na przykład, otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze, na którym jest uruchomiona brama i uruchom polecenie albo:

* Aby zatrzymać usługę, uruchom następujące polecenie:
  
  `net stop PBIEgwService`

* Aby uruchomić usługę, uruchom następujące polecenie:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy 

Obecnie nie ma żadnych jednym miejscu, gdzie Administratorzy dzierżawy mogliby zarządzać wszystkich bram, które zainstalowano i skonfigurowano innym użytkownikom. Jeśli jesteś administratorem dzierżawy, możesz chcieć niektórzy użytkownicy w Twojej organizacji dodał Cię jako administratora dla każdej zainstalowanej bramy. W ten sposób można zarządzać wszystkimi bramami w organizacji za pomocą strony Ustawienia bramy lub za pomocą [poleceń programu PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Jak działa brama?

Brama danych ułatwia szybkie i bezpieczne komunikacji między aplikację logiki, Usługa bramy w chmurze i lokalne źródła danych. Usługa bramy w chmurze są szyfrowane i przechowywane są Twoje poświadczenia źródła danych i informacje szczegółowe bramy. Usługa również kieruje zapytania i ich wyniki między aplikacją logiki, lokalnej bramy danych i źródła danych w środowisku lokalnym. 

Brama działa z zapór i używa tylko dla połączeń wychodzących. Cały ruch pochodzący jako bezpieczny ruch wychodzący z agenta bramy. Brama przekazuje dane ze źródeł lokalnych w kanałach zaszyfrowanych za pomocą usługi Azure Service Bus. Ta usługa Service bus tworzy kanału między bramą i wywoływania usługi, ale nie przechowuje żadnych danych. Wszystkie dane, które jest wysyłane za pośrednictwem bramy są szyfrowane.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Poniższe kroki opisują, co się dzieje, gdy użytkownik w chmurze korzysta z elementu, który jest podłączony do źródła danych w środowisku lokalnym:

1. Usługa bramy w chmurze tworzy zapytanie, wraz z zaszyfrowanymi poświadczeniami dla źródła danych i wysyła zapytanie do kolejki do przetworzenia przez bramę.

2. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do usługi Azure Service Bus.

3. Lokalna brama danych sonduje usługę Azure Service Bus, poszukując żądań oczekujących.

4. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłem danych przy użyciu tych poświadczeń.

5. Brama wysyła zapytanie do źródła danych do wykonania.

6. Wyniki są odsyłane ze źródła danych do bramy a następnie do usługi bramy w chmurze. Następnie Usługa bramy w chmurze na podstawie wyników.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

**Q**: Czy potrzebuję bramy dla źródeł danych w chmurze, takich jak Azure SQL Database? <br/>
**Odp.:** Nie, brama nawiązuje połączenie ze źródłami danych w środowisku lokalnym, tylko.

**Q**: Czy brama musi być zainstalowany na tym samym komputerze co źródło danych? <br/>
**Odp.:** Nie, brama nawiązuje połączenie ze źródłem danych przy użyciu podanych informacji o połączeniu. Jako aplikację kliencką w tym sensie, należy wziąć pod uwagę bramy. Brama musi jedynie możliwość podłączania do nazwy serwera, który został dostarczony.

<a name="why-azure-work-school-account"></a>

**Q**: Dlaczego należy używać konta służbowego lub szkolnego do logowania? <br/>
**Odp.:** Konto służbowe lub szkolne może używać tylko w przypadku, po zainstalowaniu lokalnej bramy danych. Konto logowania są przechowywane w dzierżawie, który jest zarządzany przez usługę Azure Active Directory (Azure AD). Zazwyczaj główna nazwa użytkownika konta usługi Azure AD (UPN) jest zgodny z adresem e-mail.

**Q**: Gdzie przechowywane są moje poświadczenia? <br/>
**Odp.:** Poświadczenia wprowadzone dla źródła danych są szyfrowane i przechowywane w usłudze bramy w chmurze. Poświadczenia są odszyfrowywane w lokalnej bramy danych.

**Q**: Czy istnieją jakiekolwiek wymagania dotyczące przepustowości sieci? <br/>
**Odp.:** Sprawdź, czy połączenie sieciowe ma dobrej przepływności. Każde środowisko jest inne, a ilość wysyłanych danych może mieć wpływ na wyniki. Zapewnieniu poziomu przepływności między źródłem danych lokalnych i centrami danych platformy Azure, spróbuj [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Aby ocenić przepływność, wypróbuj narzędzie zewnętrzne, takie jak Azure Speed Test.

**Q**: Co to jest opóźnienie przed wykonaniem zapytań do źródła danych z bramy? Co to jest najlepsza architektura? <br/>
**Odp.:** Do zmniejszenia opóźnienia sieci, należy zainstalować bramę jak najbliżej źródła danych, jak to możliwe. Jeśli bramę można zainstalować w rzeczywistym źródle danych, tym odległości minimalizuje opóźnienia. Ponadto należy wziąć pod uwagę bliskość centrów danych platformy Azure. Na przykład jeśli usługa korzysta z centrum danych zachodnie stany USA, a masz programu SQL Server hostowanego w Maszynie wirtualnej platformy Azure, następnie można maszyny Wirtualnej platformy Azure w regionie zachodnie stany USA za. Ta odległości minimalizuje opóźnienia i pozwala uniknąć opłat za ruch wychodzący na maszynie Wirtualnej platformy Azure.

**Q**: Jak wyniki są odsyłane do chmury? <br/>
**Odp.:** Wyniki są wysyłane za pośrednictwem usługi Azure Service Bus.

**Q**: Czy istnieją połączenia przychodzące do bramy z chmury? <br/>
**Odp.:** Nie, brama używa połączeń wychodzących do usługi Azure Service Bus.

**Q**: Co się stanie, jeśli zablokuję połączenia wychodzące? Co muszę otworzyć? <br/>
**Odp.:** Zobacz, portów i hostów używanych przez bramę.

**Q**: Co to jest nazwa rzeczywistej usługi Windows o nazwie? <br/>
**Odp.:** Na karcie usługi w Menedżerze zadań nazwa usługi jest "PBIEgwService" lub Power BI Enterprise Gateway Service. W konsoli usługi nazwa usługi jest "Usługa bramy danych lokalnych". Usługa Windows używa "NT SERVICE\PBIEgwService" jako identyfikatora SID usług (SSID).

**Q**: Brama Windows service uruchomić przy użyciu konta usługi Azure Active Directory? <br/>
**Odp.:** Nie, usługa Windows musi mieć prawidłowe konto Windows.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

**Q**: Jakie opcje są dostępne do odzyskania po awarii? <br/>
**Odp.:** Klucz odzyskiwania służy do przywrócenia lub przeniesienia bramy. Klucz odzyskiwania jest określany podczas instalowania bramy.

**Q**: Co to jest korzyść klucza odzyskiwania? <br/>
**Odp.:** Klucz odzyskiwania umożliwia migrację lub odzyskiwanie ustawień bramy po awarii.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W tej sekcji omówiono niektóre typowe problemy, które może być podczas konfigurowania i korzystania z lokalnej bramy danych.

**Q**: Dlaczego nie Moja instalacja bramy? <br/>
**Odp.:** Ten problem może się zdarzyć, jeśli oprogramowanie antywirusowe na komputerze docelowym jest nieaktualna. Można albo zaktualizuj oprogramowanie antywirusowe lub wyłączenia oprogramowania antywirusowego, ale tylko podczas instalacji bramy i ponownie włączyć oprogramowanie.

**Q**: Dlaczego nie widzisz Moja instalacja bramy, podczas tworzenia zasobu bramy na platformie Azure? <br/>
**Odp.:** Ten problem może się zdarzyć z tych powodów:

* Instalację bramy już jest zarejestrowany i zgłoszone przez inny zasób bramy na platformie Azure. Instalacje bramy nie są wyświetlane na liście wystąpienia, po utworzeniu zasoby bramy dla nich.
Aby sprawdzić rejestracje bramy w witrynie Azure portal, zapoznaj się z wszystkich zasobów platformy Azure za pomocą **na poziomie lokalnych bram danych** wpisz *wszystkich* subskrypcji platformy Azure. 

* Tożsamość usługi Azure AD dla osoby, która zainstalowała bramy różni się od osoby, która podpisała witrynie Azure Portal. Sprawdź, czy po zarejestrowaniu się przy użyciu tej samej tożsamości, który zainstalowano bramę.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: Gdzie znajdują się dzienniki bramy? <br/>
**Odp.:** Zobacz [ **dzienniki** sekcji](#logs) w dalszej części tego artykułu.

**Q**: Jak mogę sprawdzić, co zapytania są wysyłane do źródła danych w środowisku lokalnym? <br/>
**Odp.:** Można włączyć funkcję śledzenie zapytań, który zawiera zapytania, które są wysyłane. Pamiętaj, aby zmienić kwerendę, Śledzenie wstecz do oryginalnej wartości po zakończeniu rozwiązywania problemów. Pozostawienie włączona funkcja śledzenia zapytań tworzy większe dzienniki.

Można również przeglądać narzędzia, które ma źródła danych w celu śledzenia zapytań. Na przykład można użyć Extended Events lub SQL Profiler for SQL Server i usług Analysis Services.

### <a name="outdated-gateway-version"></a>Wersja przestarzałe bramy

Wersja bramy stanie się nieaktualna, może pojawić się wiele problemów. Dobrym rozwiązaniem jest ogólne upewnij się, że masz najnowszą wersję. Brama nie była aktualizowana przez miesiąc lub dłużej, może warto zainstalować najnowszą wersję bramy i zobacz, czy można odtworzyć problem.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Błąd: Nie można dodać użytkownika do grupy. (-2147463168 PBIEgwService Użytkownicy dzienników wydajności)

Możesz otrzymać ten błąd przy próbie zainstalowania bramy na kontrolerze domeny, który nie jest obsługiwany. Sprawdź, czy wdrożyć bramę na komputerze, na którym nie jest kontrolerem domeny.

<a name="logs"></a>

### <a name="logs"></a>Dzienniki

Aby ułatwić rozwiązywanie problemów z zawsze uruchamiaj zbieranie i przeglądając dzienniki bramy. Istnieje kilka sposobów zbierania dzienników to najprostsza opcja, po zainstalowaniu bramy jest jednak za pomocą interfejsu użytkownika Instalatora bramy. 

1. Na komputerze otwórz Instalator bramy danych lokalnych.
2. W menu po lewej stronie wybierz **diagnostyki**.
3. W obszarze **dzienniki bramy**, wybierz opcję **Eksportuj dzienniki**.

   ![Eksportuj dzienniki za pomocą Instalatora bramy](./media/logic-apps-gateway-install/export-logs.png)

Poniżej przedstawiono inne lokalizacje, gdzie można znaleźć różnych dzienników:

| Typ dziennika | Lokalizacja | 
|----------|----------| 
| **Dzienniki Instalatora** | %localappdata%\Temp\On-premises_data_gateway_ <*RRRRMMDD*>. <*numer*> .log | 
| **Dzienniki konfiguracji** | C:\Users\<*username*> gateway\GatewayConfigurator danych \AppData\Local\Microsoft\On-premises <*RRRRMMDD*>. <*numer*>. Dziennik | 
| **Dzienniki usługi bramy przedsiębiorstwa** | Gateway\Gateway danych C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises <*RRRRMMDD*>. <*numer*> .log | 
||| 

**Dzienniki zdarzeń**

Aby bramy na ten temat można znaleźć w dziennikach zdarzeń, wykonaj następujące kroki:

1. Na komputerze z instalacją bramy Otwórz **Podgląd zdarzeń**. 
2. Rozwiń **podglądu zdarzeń (lokalne)**  > **Dzienniki aplikacji i usług**. 
3. Wybierz **Usługa bramy danych lokalnych**.

   ![Przeglądanie dzienników zdarzeń dla bramy](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Przegląd wydajności wolnych zapytań

Jeśli okaże się zapytania spowolnienie działania za pośrednictwem bramy, można włączyć dodatkowe rejestrowanie, który wyprowadza zapytań i czasie ich trwania. Te dzienniki mogą pomóc w znalezieniu, które zapytania są powolne lub długotrwałe. Celem poprawy wydajności zapytań, trzeba będzie zmodyfikować źródła danych, na przykład, dostosować indeksów dla zapytań programu SQL Server.

Aby określić czas trwania dla kwerendy, wykonaj następujące kroki:

1. Przejdź do tej samej lokalizacji co klient bramy, który zwykle można znaleźć tutaj: ```C:\Program Files\On-premises data gateway```

   W przeciwnym razie można znaleźć lokalizacji klienta, otwórz konsolę usługi na tym samym komputerze, znajdowanie **Usługa bramy danych lokalnych**i wyświetlić **ścieżka do pliku wykonywalnego** właściwości.

2. Otwieranie i edytowanie tych plików konfiguracji, zgodnie z opisem:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     W tym pliku, należy zmienić **właściwość EmitQueryTraces** wartość z **false** do **true** , dzięki czemu Brama może rejestrować zapytań wysyłanych z bramy do źródła danych:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Włączenie ustawienia właściwości EmitQueryTraces może znacznie zwiększyć rozmiar dziennika zależnie od użycia bramy. Po zakończeniu wprowadzania zmian, przeglądając dzienniki, upewnij się, możesz zresetować właściwość EmitQueryTraces na **false** ponownie, a nie, pozostaw to ustawienie w długim okresie.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Aby brama rejestrowania pełnych wpisów, w tym wpisów obejmujących czas trwania, należy zmienić **TracingVerbosity** wartość z **4** do **5** , wykonując na każdym kroku: 

     * W tym pliku konfiguracji, należy zmienić **TracingVerbosity** wartość z **4** do **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Otwórz Instalatora bramy, wybierz opcję **diagnostyki**, Włącz **dodatkowe rejestrowanie**, a następnie wybierz **Zastosuj**:

       ![Włączyć dodatkowe rejestrowanie](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Włączenie ustawienia TracingVerbosity może znacznie zwiększyć rozmiar dziennika zależnie od użycia bramy. Po zakończeniu wprowadzania zmian, przeglądając dzienniki, upewnij się, że możesz wyłączyć **dodatkowe rejestrowanie** w oknie Instalatora bramy lub zresetować TracingVerbosity do **4** ponownie w pliku konfiguracji, zamiast pozostawić to ustawienie na w perspektywie długoterminowej.

3. Aby znaleźć czas trwania dla zapytania, wykonaj następujące kroki:

   1. [Eksportuj](#logs) , a następnie otwórz dziennik bramy.

   2. Aby znaleźć zapytania, na przykład wyszukaj typ działania: 

      | Typ działania | Opis | 
      |---------------|-------------| 
      | TYP MGEQ | Zapytania, które są uruchamiane za pośrednictwem platformy ADO.NET. | 
      | MGEO | Zapytania, które są uruchamiane za pośrednictwem interfejsu OLEDB. | 
      | MGEM | Zapytania, które są uruchamiane w aparacie Mashupów. | 
      ||| 

   3. Uwaga drugi identyfikator GUID, który jest identyfikatorem żądania.

   4. Kontynuuj wyszukiwanie typu działania, aż znajdziesz wpis o nazwie "FireActivityCompletedSuccessfullyEvent", który ma czas trwania (w milisekundach). 
   Upewnij się, że wpis ma ten sam identyfikator żądania, na przykład:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Wpis "FireActivityCompletedSuccessfullyEvent" jest wpisem pełnym i nie jest zalogowany, chyba że ustawienie "TracingVerbosity" jest na poziomie 5.

### <a name="trace-traffic-with-fiddler"></a>Śledzenie ruchu przy użyciu programu Fiddler

[Narzędzie fiddler](https://www.telerik.com/fiddler) to bezpłatne narzędzie firmy telerik, które monitoruje ruch HTTP. Możesz przejrzeć ten ruch z usługą Power BI z komputera klienckiego. Ta usługa może wyświetlać błędy i inne istotne informacje.

## <a name="next-steps"></a>Kolejne kroki
    
* [Łączenie z danymi w środowisku lokalnym z aplikacji logiki](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
