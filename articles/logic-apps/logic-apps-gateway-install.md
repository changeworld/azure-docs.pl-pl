---
title: Zainstaluj lokalną bramę danych — Azure Logic Apps
description: Aby można było uzyskać dostęp do danych lokalnych z Azure Logic Apps, Pobierz i zainstaluj lokalną bramę danych.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860772"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Zainstaluj lokalną bramę danych dla Azure Logic Apps

Przed nawiązaniem połączenia z lokalnymi źródłami danych z Azure Logic Apps Pobierz i zainstaluj lokalną bramę danych na komputerze lokalnym. Brama działa jako most, który zapewnia szybkie przesyłanie i szyfrowanie danych między źródłami danych lokalnymi (a nie w chmurze) i aplikacjami logiki. Możesz użyć tej samej instalacji bramy z innymi usługami w chmurze, takimi jak Power BI, Microsoft Flow, PowerApps i Azure Analysis Services. Aby uzyskać informacje o sposobach korzystania z bramy za pomocą tych usług, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps lokalnej bramy danych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

W tym artykule pokazano, jak pobrać, zainstalować i skonfigurować lokalną bramę danych, aby można było uzyskać dostęp do lokalnych źródeł danych z Azure Logic Apps. Możesz również dowiedzieć się więcej o tym [, jak Brama danych działa](#gateway-cloud-service) w dalszej części tego tematu.

<a name="supported-connections"></a>

Brama obsługuje [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) w Azure Logic Apps dla tych źródeł danych:

* BizTalk Server 2016
* System plików
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* Oprogramowanie SharePoint Server
* SQL Server
* Teradata

Chociaż sama Brama nie wiąże się z dodatkowymi kosztami, [model cenowy Logic Apps](../logic-apps/logic-apps-pricing.md) ma zastosowanie do tych łączników i innych operacji w Azure Logic Apps.

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  * Musisz użyć tego samego konta platformy Azure, aby zainstalować bramę i zarządzać nią. Podczas instalacji można użyć tego konta platformy Azure do skojarzenia bramy na komputerze z subskrypcją platformy Azure. Później można użyć tego samego konta platformy Azure podczas tworzenia zasobu platformy Azure w Azure Portal na potrzeby instalacji bramy. 

  * Musisz zalogować się przy użyciu konta służbowego, znanego również jako konto *organizacji* , które wygląda `username@contoso.com`następująco. Nie można używać kont B2B (gość) platformy Azure ani osobistych kont Microsoft, takich @hotmail.com jak @outlook.comlub.

    > [!TIP]
    > Jeśli zarejestrowano się w celu uzyskania oferty pakietu Office 365 i nie podano służbowego adresu e-mail, adres `username@domain.onmicrosoft.com`może wyglądać następująco. Twoje konto jest przechowywane w dzierżawie w Azure Active Directory (Azure AD). W większości przypadków główna nazwa użytkownika (UPN) dla konta usługi Azure AD jest taka sama jak w przypadku Twojego adresu e-mail.
    >
    > Aby użyć [subskrypcji programu Visual Studio w warstwie Standardowa](https://visualstudio.microsoft.com/vs/pricing/) skojarzonej z konto Microsoft, należy najpierw [utworzyć dzierżawę w usłudze Azure AD](../active-directory/develop/quickstart-create-new-tenant.md)lub użyć domyślnego katalogu. Dodaj użytkownika z hasłem do katalogu, a następnie nadaj temu użytkownikowi dostęp do subskrypcji. 
    > Następnie możesz zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

* Poniżej przedstawiono wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  * .NET Framework 4.6
  * 64 — bitowa wersja systemu Windows 7 lub Windows Server 2008 R2 (lub nowszego)

  **Zalecane wymagania**

  * 8-rdzeniowy procesor CPU
  * 8 GB pamięci
  * 64 — bitowa wersja systemu Windows Server 2012 R2 lub nowszego
  * Magazyn dysków półprzewodnikowych (SSD) do buforowania

  > [!NOTE]
  > Brama nie obsługuje systemu Windows Server 2016 Core.

* **Powiązane zagadnienia**

  * Bramę danych lokalnych można zainstalować tylko na komputerze lokalnym, a nie na kontrolerze domeny. Nie trzeba jednak instalować bramy na tym samym komputerze, na którym znajduje się źródło danych. Wymagana jest tylko jedna brama dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować opóźnienie, można zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze, który znajduje się w sieci przewodowej, połączony z Internetem, zawsze włączone i przejdź do trybu uśpienia. W przeciwnym razie nie można uruchomić bramy, a wydajność sieci bezprzewodowej może być niedostępna.

  * Jeśli planujesz używanie uwierzytelniania systemu Windows, upewnij się, że brama jest zainstalowana na komputerze, który jest członkiem tego samego środowiska Active Directory, co źródła danych.

  * Region wybrany dla instalacji bramy jest tą samą lokalizacją, którą należy wybrać podczas późniejszego tworzenia zasobu bramy platformy Azure dla aplikacji logiki. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD, która zarządza Twoim kontem platformy Azure. Można jednak zmienić lokalizację podczas instalacji bramy.

  * Brama ma dwa tryby: tryb standardowy i tryb osobisty, który ma zastosowanie tylko do Power BI. Na tym samym komputerze nie może znajdować się więcej niż jedna brama uruchomiona w tym samym trybie.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz i uruchom Instalatora bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

1. Po otwarciu Instalatora wybierz pozycję **dalej**.

   ![Wprowadzenie do Instalatora](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Wybierz pozycję **lokalna Brama danych (zalecane)** , która jest trybem standardowym, a następnie wybierz przycisk **dalej**.

   ![Wybierz tryb bramy](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Zapoznaj się z minimalnymi wymaganiami, Zachowaj domyślną ścieżkę instalacji, zaakceptuj warunki użytkowania, a następnie wybierz pozycję **Zainstaluj**.

   ![Przejrzyj wymagania i zaakceptuj warunki użytkowania](./media/logic-apps-gateway-install/accept-terms.png)

1. Po pomyślnym zainstalowaniu bramy Podaj adres e-mail konta organizacji, a następnie wybierz pozycję **Zaloguj się**, na przykład:

   ![Zaloguj się przy użyciu konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Jesteś teraz zalogowany na koncie.

1. Wybierz pozycję **zarejestruj nową bramę na tym komputerze** > **dalej**. Ten krok rejestruje instalację bramy w [usłudze bramy w chmurze](#gateway-cloud-service).

   ![Rejestrowanie bramy](./media/logic-apps-gateway-install/register-gateway.png)

1. Podaj te informacje na potrzeby instalacji bramy:

   * Nazwa bramy, która jest unikatowa w ramach dzierżawy usługi Azure AD
   * Klucz odzyskiwania, który musi mieć co najmniej osiem znaków, które mają być używane.
   * Potwierdzenie klucza odzyskiwania

   ![Konfigurowanie bramy](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Zapisz i Zachowaj klucz odzyskiwania w bezpiecznym miejscu. Ten klucz będzie potrzebny, jeśli kiedykolwiek chcesz zmienić lokalizację, przenieść, odzyskać lub przejmowanie instalacji bramy.

   Zwróć uwagę na to, że można **dodać do istniejącego klastra bramy**, który wybierasz podczas instalacji dodatkowych bram w [scenariuszach wysokiej dostępności](#high-availability).

1. Sprawdź region usługi bramy w chmurze i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) używany przez instalację bramy. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD dla Twojego konta platformy Azure.

   ![Sprawdź region](./media/logic-apps-gateway-install/check-region.png)

1. Aby zaakceptować domyślny region, wybierz pozycję **Konfiguruj**. Jeśli jednak domyślny region nie jest tym, który znajduje się najbliżej Ciebie, możesz zmienić region.

   *Dlaczego należy zmienić region instalacji bramy?*

   Na przykład w celu zmniejszenia opóźnień można zmienić region bramy na ten sam region, w którym znajduje się aplikacja logiki. Możesz też wybrać region znajdujący się najbliżej lokalnego źródła danych. *Zasób bramy na platformie Azure* i aplikacja logiki mogą mieć różne lokalizacje.

   1. Obok bieżącego regionu wybierz pozycję **Zmień region**.

      ![Zmień region](./media/logic-apps-gateway-install/change-region.png)

   1. Na następnej stronie Otwórz listę **Wybierz region** , wybierz żądany region, a następnie wybierz pozycję **gotowe**.

      ![Wybierz inny region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Przejrzyj informacje w oknie potwierdzenie końcowe. Ten przykład używa tego samego konta dla Logic Apps, Power BI, PowerApps i Microsoft Flow, aby brama była dostępna dla wszystkich tych usług. Gdy wszystko będzie gotowe, wybierz pozycję **Zamknij**.

   ![Zakończono bramę](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Teraz [Utwórz zasób platformy Azure na potrzeby instalacji bramy](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Aby uniknąć pojedynczych punktów awarii w przypadku dostępu do danych lokalnych, można mieć wiele instalacji bramy (tylko tryb standardowy) z każdym innym komputerem i skonfigurować je jako klaster lub grupę. W ten sposób, jeśli Brama podstawowa jest niedostępna, żądania danych są kierowane do drugiej bramy i tak dalej. Ponieważ na komputerze można zainstalować tylko jedną bramę standardową, należy zainstalować każdą dodatkową bramę znajdującą się w klastrze na innym komputerze. Wszystkie łączniki, które współpracują z lokalną bramą danych, obsługują wysoką dostępność. 

* Musisz mieć już co najmniej jedną instalację bramy w ramach tej samej subskrypcji platformy Azure co Brama podstawowa i klucz odzyskiwania dla tej instalacji.

* Brama podstawowa musi mieć uruchomioną aktualizację z listopada 2017 lub nowszą.

Po skonfigurowaniu bramy podstawowej po przejściu do pozycji zainstaluj inną bramę wybierz opcję **Dodaj do istniejącego klastra bramy**, wybierz bramę podstawową, która jest pierwszą zainstalowaną bramą, i Podaj klucz odzyskiwania dla tej bramy. Aby uzyskać więcej informacji, zobacz [klastrów o wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmień lokalizację, Migruj, Przywróć lub Przejmij istniejącą bramę

Jeśli konieczna jest zmiana lokalizacji bramy, przeniesienie instalacji bramy na nowy komputer, odzyskanie uszkodzonej bramy lub przejęcie na własność istniejącej bramy wymaga klucza odzyskiwania, który został dostarczony podczas instalacji bramy.

1. Uruchom Instalatora bramy na komputerze, który ma istniejącą bramę. Jeśli nie masz najnowszego Instalatora bramy, [Pobierz najnowszą wersję bramy](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Przed przystąpieniem do przywracania bramy na komputerze z oryginalną instalacją bramy należy najpierw odinstalować bramę na tym komputerze. Ta akcja rozłącza pierwotną bramę.
   > Po usunięciu lub usunięciu klastra bramy dla dowolnej usługi w chmurze nie można przywrócić tego klastra.

1. Po otwarciu Instalatora Zaloguj się przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. Wybierz pozycję **Migruj, Przywróć lub przejęcie istniejącej bramy** > **dalej**, na przykład:

   ![Wybieranie opcji "Migruj, Przywróć lub przejęcie istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Wybierz spośród dostępnych klastrów i bram, a następnie wprowadź klucz odzyskiwania dla wybranej bramy, na przykład:

   ![Wybierz bramę](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Aby zmienić region, wybierz pozycję **Zmień region**, a następnie wybierz nowy region.

1. Gdy wszystko będzie gotowe, wybierz pozycję **Konfiguruj** , aby można było zakończyć zadanie.

## <a name="configure-proxy-or-firewall"></a>Konfigurowanie serwera proxy lub zapory

Jeśli środowisko pracy wymaga, aby ruch przechodził przez serwer proxy w celu uzyskania dostępu do Internetu, to ograniczenie może uniemożliwić usłudze lokalna Brama danych łączenie się z usługą bramy w chmurze i [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień serwera proxy dla lokalnej bramy danych](https://docs.microsoft.com/power-bi/service-gateway-proxy).

Aby sprawdzić, czy Twój serwer proxy lub Zapora może blokować połączenia, sprawdź, czy komputer może rzeczywiście łączyć się z Internetem i Azure Service Bus. W wierszu polecenia programu PowerShell uruchom następujące polecenie:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> To polecenie testuje tylko łączność sieciową i łączność z Azure Service Bus. Polecenie nie wykonuje żadnych czynności z bramą ani usługą bramy w chmurze, która szyfruje i przechowuje poświadczenia oraz szczegóły bramy. 
>
> Ponadto to polecenie jest dostępne tylko w systemie Windows Server 2012 R2 lub nowszym, a Windows 8.1 lub nowszym. W starszych wersjach systemu operacyjnego można użyć programu Telnet do testowania łączności. Dowiedz się więcej o [Azure Service Bus i rozwiązaniach hybrydowych](../service-bus-messaging/service-bus-messaging-overview.md).

Wyniki powinny wyglądać podobnie do tego przykładu, gdy **TcpTestSucceeded** ma **wartość true**:

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

Jeśli **TcpTestSucceeded** nie ma **wartości true**, Brama może być blokowana przez zaporę. Jeśli chcesz uzyskać pełną konfigurację, Zastąp wartości **ComputerName** i **port** wartościami wymienionymi w sekcji [Konfigurowanie portów](#configure-ports) w tym artykule.

Zapora może także blokować połączenia, które Azure Service Bus w centrach danych platformy Azure. W takim scenariuszu Zatwierdź (Odblokuj) wszystkie adresy IP dla tych centrów danych w Twoim regionie. W przypadku tych adresów IP w [tym miejscu Pobierz listę adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Konfigurowanie portów

Brama tworzy połączenie wychodzące do Azure Service Bus i komunikuje się na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 za pośrednictwem 9354. Brama nie wymaga portów przychodzących. Dowiedz się więcej o [Azure Service Bus i rozwiązaniach hybrydowych](../service-bus-messaging/service-bus-messaging-overview.md).

Brama używa tych w pełni kwalifikowanych nazw domen:

| Nazwy domen | Porty wychodzące | Opis |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.microsoftonline-p.com | 443 | Używany do uwierzytelniania w zależności od konfiguracji. |
| *.msftncsi.com | 443 | Służy do testowania łączności z Internetem, gdy Brama jest nieosiągalna przez usługa Power BI. |
| *.servicebus.windows.net | 443, 9350-9354 | Odbiorniki na Service Bus Relay za pośrednictwem protokołu TCP (wymaga 443 na potrzeby pozyskiwania tokenów Access Control) |
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| login.microsoftonline.com | 443 | HTTPS |
||||

W niektórych przypadkach Azure Service Bus połączenia są nawiązywane przy użyciu adresów IP, a nie w pełni kwalifikowanych nazw domen. W związku z tym możesz chcieć odblokować adresy IP obszaru danych w zaporze. Aby umożliwić dostęp dla adresów IP zamiast domen, można pobrać i użyć [listy zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adresy IP na tej liście znajdują się w notacji [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Wymuś komunikację HTTPS z Azure Service Bus

Niektóre serwery proxy umożliwiają ruch tylko do portów 80 i 443. Domyślnie komunikacja z Azure Service Bus odbywa się na portach innych niż 443. Można wymusić komunikację bramy z Azure Service Bus za pośrednictwem protokołu HTTPS zamiast bezpośredniego połączenia TCP, ale może to znacznie zmniejszyć wydajność. Aby uzyskać więcej informacji, zobacz wymuszanie [komunikacji HTTPS z Azure Service Bus](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Konto usługi systemu Windows

Domyślnie instalacja bramy na komputerze lokalnym działa jako konto usługi systemu Windows o nazwie "lokalna Brama danych". Jednak instalacja bramy używa `NT SERVICE\PBIEgwService` nazwy dla poświadczeń konta "Zaloguj się jako" i ma uprawnienia "Logowanie w trybie usługi".

> [!NOTE]
> Konto usługi systemu Windows różni się od konta używanego do łączenia się z lokalnymi źródłami danych i konta platformy Azure, które jest używane podczas logowania się do usług w chmurze.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Uruchom ponownie bramę

Brama danych działa jako usługa okna, tak jak jakakolwiek inna usługa systemu Windows, można uruchomić i zatrzymać bramę na różne sposoby. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy

Aby uzyskać wgląd we wszystkie lokalne bramy danych w dzierżawie usługi Azure AD, Administratorzy globalni w tej dzierżawie mogą zalogować się do [Centrum administracyjnego platformy na platformie zarządzania](https://powerplatform.microsoft.com) jako Administrator dzierżawy i wybrać opcję **bramy danych** . Aby uzyskać więcej informacji, zobacz [administrowanie na poziomie dzierżawy dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak działa Brama

Brama danych ułatwia szybką i bezpieczną komunikację między aplikacją logiki, usługą bramy w chmurze i lokalnym źródłem danych. Usługa bramy w chmurze szyfruje i przechowuje poświadczenia źródła danych oraz szczegóły bramy. Usługa kieruje także zapytania i ich wyniki między aplikacją logiki, lokalną bramą danych i źródłem danych lokalnego.

Brama współpracuje z zaporami i używa tylko połączeń wychodzących. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Brama przekazuje dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Ta usługa Service Bus tworzy kanał między bramą a usługą wywołującą, ale nie przechowuje żadnych danych. Wszystkie dane przesyłane przez bramę są szyfrowane.

![Architektura bramy danych lokalnych](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

W tych krokach opisano, co się dzieje, gdy użytkownik w chmurze współdziała z elementem połączonym z lokalnym źródłem danych:

1. Usługa bramy w chmurze tworzy zapytanie wraz z zaszyfrowanymi poświadczeniami dla źródła danych i wysyła zapytanie do kolejki w celu przetworzenia przez bramę.

1. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do Azure Service Bus.

1. Lokalna Brama danych sonduje Azure Service Bus dla oczekujących żądań.

1. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłem danych przy użyciu tych poświadczeń.

1. Brama wysyła zapytanie do źródła danych w celu wykonania.

1. Wyniki są wysyłane ze źródła danych z powrotem do bramy, a następnie do usługi bramy w chmurze. Następnie Usługa bramy w chmurze używa wyników.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

**P**: Czy potrzebuję bramy dla źródeł danych w chmurze, takich jak Azure SQL Database? <br/>
**Odp.:** Nie, Brama łączy się tylko z lokalnymi źródłami danych.

**P**: Czy brama musi być zainstalowana na tym samym komputerze co źródło danych? <br/>
**Odp.:** Nie, Brama nawiązuje połączenie ze źródłem danych, korzystając z podanych informacji o połączeniu. W tym sensie Rozważmy bramę jako aplikację kliencką. Brama musi jedynie mieć możliwość nawiązania połączenia z podaną nazwą serwera.

<a name="why-azure-work-school-account"></a>

**P**: Dlaczego należy używać konta służbowego do logowania? <br/>
**Odp.:** Konta służbowego można używać tylko podczas instalacji lokalnej bramy danych. Twoje konto logowania jest przechowywane w dzierżawie, która jest zarządzana przez Azure Active Directory (Azure AD). Zazwyczaj główna nazwa użytkownika konta usługi Azure AD (UPN) jest zgodna z adresem e-mail.

**P**: Gdzie przechowywane są moje poświadczenia? <br/>
**Odp.:** Poświadczenia wprowadzone dla źródła danych są szyfrowane i przechowywane w usłudze bramy w chmurze. Poświadczenia są odszyfrowywane w lokalnej bramie danych.

**P**: Czy istnieją jakieś wymagania dotyczące przepustowości sieci? <br/>
**Odp.:** Sprawdź, czy połączenie sieciowe ma dobrą przepływność. Każde środowisko jest inne i ilość wysyłanych danych może mieć wpływ na wyniki. Aby zagwarantowanie poziomu przepływności między lokalnym źródłem danych i centrami Datacenter, wypróbuj [usługę Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Aby zwiększyć przepływność, wypróbuj zewnętrzne narzędzie, takie jak Azure Speed Test.

**P**: Jaki jest czas oczekiwania na wykonywanie zapytań do źródła danych z bramy? Co to jest Najlepsza architektura? <br/>
**Odp.:** Aby zmniejszyć opóźnienie sieci, należy zainstalować bramę jako blisko źródła danych, jak to możliwe. Jeśli można zainstalować bramę w rzeczywistym źródle danych, ta bliskość zmniejsza czas oczekiwania. Rozważ również bliskość centrów danych platformy Azure. Na przykład jeśli usługa używa zachodniego centrum danych USA, a SQL Server hostowana na maszynie wirtualnej platformy Azure, może być również potrzebna maszyna wirtualna platformy Azure w regionie zachodnie stany USA. Ta bliskość minimalizuje opóźnienia i pozwala uniknąć naliczania opłat wychodzących na maszynę wirtualną platformy Azure.

**P**: W jaki sposób wyniki są odsyłane do chmury? <br/>
**Odp.:** Wyniki są wysyłane przez Azure Service Bus.

**P**: Czy istnieją połączenia przychodzące do bramy z chmury? <br/>
**Odp.:** Nie, brama używa połączeń wychodzących do Azure Service Bus.

**P**: Co zrobić, jeśli blokuję połączenia wychodzące? Co muszę otworzyć? <br/>
**Odp.:** Zobacz porty i hosty, których używa brama.

**P**: Jaka jest nazwa rzeczywistej usługi systemu Windows? <br/>
**Odp.:** Na karcie usługi w Menedżerze zadań nazwa usługi to "PBIEgwService" lub Power BI usługi bramy Enterprise Gateway. W konsoli usługi nazwa usługi to "lokalna Brama danych". Usługa systemu Windows używa "NT SERVICE\PBIEgwService" jako identyfikatora SID usługi (SSID).

**P**: Czy Usługa bramy systemu Windows może być uruchamiana przy użyciu konta Azure Active Directory? <br/>
**Odp.:** Nie, usługa systemu Windows musi mieć prawidłowe konto systemu Windows.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

**P**: Jakie opcje są dostępne na potrzeby odzyskiwania po awarii? <br/>
**Odp.:** Do przywrócenia lub przeniesienia bramy można użyć klucza odzyskiwania. Po zainstalowaniu bramy Określ klucz odzyskiwania.

**P**: Co to jest korzyść klucza odzyskiwania? <br/>
**Odp.:** Klucz odzyskiwania zapewnia sposób migracji lub odzyskiwania ustawień bramy po awarii.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja dotyczy niektórych typowych problemów, które mogą wystąpić podczas konfigurowania lokalnej bramy danych i korzystania z niej.

**P**: Dlaczego instalacja bramy kończy się niepowodzeniem? <br/>
**Odp.:** Przyczyną tego problemu może być nieaktualna wersja oprogramowania antywirusowego na komputerze docelowym. Można zaktualizować oprogramowanie antywirusowe lub wyłączyć oprogramowanie chroniące przed wirusami, ale tylko podczas instalacji bramy, a następnie ponownie włączyć oprogramowanie.

**P**: Dlaczego nie widzę mojej instalacji bramy podczas tworzenia zasobu bramy na platformie Azure? <br/>
**Odp.:** Ten problem może wystąpić z następujących powodów:

* Instalacja bramy została już zarejestrowana i przejęta przez inny zasób bramy na platformie Azure. Instalacje bramy nie pojawiają się na liście wystąpień po utworzeniu zasobów bramy. Aby sprawdzić rejestracje bramy w Azure Portal, przejrzyj wszystkie zasoby platformy Azure z typem **lokalnych bram danych** dla *wszystkich* subskrypcji platformy Azure.

* Tożsamość usługi Azure AD dla osoby, która zainstalowała bramę, różni się od osoby, która zarejestrowano w Azure Portal. Sprawdź, czy zalogowano się za pomocą tej samej tożsamości, na której zainstalowano bramę.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**P**: Gdzie znajdują się dzienniki bramy? <br/>
**Odp.:** Zobacz sekcję [ **dzienniki** ](#logs) w dalszej części tego artykułu.

**P**: Jak mogę zobaczyć, jakie zapytania są wysyłane do lokalnego źródła danych? <br/>
**Odp.:** Możesz włączyć funkcję śledzenia zapytań, która obejmuje wysyłane zapytania. Pamiętaj, aby zmienić śledzenie zapytania z powrotem na pierwotną wartość po zakończeniu rozwiązywania problemów. Pozostawienie włączonego śledzenia zapytań powoduje utworzenie większych dzienników.

Możesz również zapoznać się z narzędziami, które są używane przez źródło danych do śledzenia zapytań. Można na przykład użyć zdarzeń rozszerzonych lub programu SQL Profiler dla SQL Server i Analysis Services.

### <a name="outdated-gateway-version"></a>Nieaktualna wersja bramy

Gdy wersja bramy stanie się nieaktualna, może się pojawić wiele problemów. Zgodnie z dobrą ogólną zasadą upewnij się, że masz najnowszą wersję. Jeśli Brama nie była aktualizowana przez miesiąc lub dłużej, warto rozważyć zainstalowanie najnowszej wersji bramy i sprawdzić, czy można odtworzyć problem.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Błąd: Nie można dodać użytkownika do grupy. (-2147463168 PBIEgwService Użytkownicy dzienników wydajności)

Ten błąd może wystąpić, jeśli spróbujesz zainstalować bramę na kontrolerze domeny, który nie jest obsługiwany. Upewnij się, że brama została wdrożona na komputerze, który nie jest kontrolerem domeny.

<a name="logs"></a>

### <a name="logs"></a>Dzienniki

Aby ułatwić rozwiązywanie problemów, należy zawsze zacząć od zbierania i przeglądania dzienników bramy. Istnieje kilka sposobów na zbieranie dzienników, ale najprostsza opcja po zainstalowaniu bramy polega na użyciu interfejsu użytkownika Instalatora bramy.

1. Na komputerze otwórz instalatora lokalnej bramy danych.

1. Z menu po lewej stronie wybierz pozycję **Diagnostyka**.

1. W obszarze **dzienniki bramy**wybierz pozycję **Eksportuj dzienniki**.

   ![Eksportuj dzienniki z Instalatora bramy](./media/logic-apps-gateway-install/export-logs.png)

Oto inne lokalizacje, w których można znaleźć różne dzienniki:

| Typ dziennika | Location |
|----------|----------|
| **Dzienniki Instalatora** | %localappdata%\Temp\On-premises_data_gateway_ <*rrrrmmdd*>. <*Number*>. log |
| **Dzienniki konfiguracji** | C:\Users\<*username*> \AppData\Local\Microsoft\On-premises Data gateway\GatewayConfigurator <*RRRRMMDD*>. <*Number*>. log |
| **Dzienniki usługi bramy przedsiębiorstwa** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises Data gateway\Gateway <*rrrrmmdd*>. <*Number*>. log |
|||

**Dzienniki zdarzeń**

Aby znaleźć dzienniki zdarzeń dla bramy, wykonaj następujące kroki:

1. Na komputerze z instalacją bramy Otwórz **Podgląd zdarzeń**.

1. Rozwiń węzeł **Podgląd zdarzeń (lokalne)**  > **Dzienniki aplikacji i usług**.

1. Wybierz pozycję **Usługa lokalnej bramy danych**.

   ![Wyświetlanie dzienników zdarzeń dla bramy](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Przejrzyj niską wydajność zapytań

Jeśli okaże się, że zapytania działają wolno przez bramę, można włączyć dodatkowe rejestrowanie, które generuje zapytania i ich czasy trwania. Te dzienniki mogą pomóc w znalezieniu, które zapytania są wolne lub długotrwałe. Aby dostroić wydajność zapytań, może być konieczne zmodyfikowanie źródła danych, na przykład dostosowanie indeksów dla zapytań SQL Server.

Aby określić czas trwania zapytania, wykonaj następujące kroki:

1. Przejdź do tej samej lokalizacji, w której znajduje się klient bramy, który zwykle można znaleźć tutaj:```C:\Program Files\On-premises data gateway```

   W przeciwnym razie, aby znaleźć lokalizację klienta, Otwórz konsolę usługi na tym samym komputerze, Znajdź **usługę lokalna Brama danych**i Wyświetl **ścieżkę do właściwości pliku wykonywalnego** .

1. Otwórz i edytuj te pliki konfiguracji zgodnie z opisem:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     W tym pliku Zmień wartość **EmitQueryTraces** z **false** na **true** , aby brama mogła rejestrować zapytania wysyłane z bramy do źródła danych:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Włączenie ustawienia EmitQueryTraces może znacząco zwiększyć rozmiar dziennika na podstawie użycia bramy. Po zakończeniu przeglądania dzienników Pamiętaj, aby ponownie zresetować EmitQueryTraces do **wartości false** , zamiast pozostawić to ustawienie dla długiego okresu.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Aby uzyskać pełne wpisy dziennika bramy, w tym wpisy pokazujące czas trwania, należy zmienić wartość **traceverbosity** z **4** na **5** , wykonując jedną z kroków:

     * W tym pliku konfiguracyjnym Zmień wartość **traceverbosity** z **4** na **5**

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Otwórz Instalator bramy, wybierz opcję **Diagnostyka**, Włącz **dodatkowe rejestrowanie**, a następnie wybierz **Zastosuj**:

       ![Włącz dodatkowe rejestrowanie](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Włączenie ustawienia Traceverbosity może znacząco zwiększyć rozmiar dziennika na podstawie użycia bramy. Po zakończeniu przeglądania dzienników należy wyłączyć **dodatkowe rejestrowanie** w instalatorze bramy lub ponownie zresetować traceverbosity do **4** w pliku konfiguracji, zamiast pozostawić to ustawienie dla długiego okresu.

1. Aby znaleźć czas trwania zapytania, wykonaj następujące kroki:

   1. [Wyeksportuj](#logs) i Otwórz dziennik bramy.

   1. Aby znaleźć zapytanie, Wyszukaj typ działania, na przykład:

      | Typ działania | Opis |
      |---------------|-------------|
      | MGEQ | Zapytania, które działają przez ADO.NET |
      | MGEO | Zapytania, które działają za pośrednictwem OLEDB |
      | MGEM | Zapytania uruchamiane z aparatu programu mashup |
      |||

   1. Zwróć uwagę na drugi identyfikator GUID, który jest IDENTYFIKATORem żądania.

   1. Kontynuuj wyszukiwanie typu działania, dopóki nie znajdziesz wpisu o nazwie "FireActivityCompletedSuccessfullyEvent", który ma czas trwania w milisekundach. Potwierdź, że wpis ma ten sam identyfikator żądania, na przykład:

      ```text
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE]
      > Wpis "FireActivityCompletedSuccessfullyEvent" jest wpisem w trybie pełnym i nie jest rejestrowany, chyba że ustawienie "Traceverbosity" ma wartość Level 5.

### <a name="trace-traffic-with-fiddler"></a>Ruch śledzenia z programu Fiddler

[Programu Fiddler](https://www.telerik.com/fiddler) to bezpłatne narzędzie z Telerik, które monitoruje ruch HTTP. Ten ruch można sprawdzić za pomocą usługa Power BI z komputera klienckiego. Ta usługa może zawierać błędy i inne powiązane informacje.

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z danymi lokalnymi z usługi Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
