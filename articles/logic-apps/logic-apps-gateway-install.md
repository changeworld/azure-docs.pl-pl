---
title: Zainstaluj lokalną bramę danych — Azure Logic Apps | Microsoft Docs
description: Aby można było uzyskać dostęp do danych lokalnych z Azure Logic Apps, Pobierz i zainstaluj lokalną bramę danych.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 61a9b319b9ea44f766bc6f014b76bc48d15efc57
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598458"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Zainstaluj lokalną bramę danych dla Azure Logic Apps

Przed nawiązaniem połączenia z lokalnymi źródłami danych z Azure Logic Apps Pobierz i zainstaluj lokalną bramę danych na komputerze lokalnym. Brama działa jako most, który zapewnia szybkie przesyłanie i szyfrowanie danych między źródłami danych lokalnymi (a nie w chmurze) i aplikacjami logiki. W tym artykule pokazano, jak można pobrać, zainstalować i skonfigurować lokalną bramę danych. 

Możesz użyć tej samej instalacji bramy z innymi usługami, takimi jak Power BI, Microsoft Flow, PowerApps i Azure Analysis Services. Dowiedz się więcej o [tym, jak działa Brama danych](#gateway-cloud-service).

<a name="supported-connections"></a>

Brama obsługuje [Łączniki lokalne](../connectors/apis-list.md#on-premises-connectors) w Azure Logic Apps dla tych źródeł danych:

*   BizTalk Server 2016
*   System plików
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   Oprogramowanie SharePoint Server
*   SQL Server
*   Teradata

Aby uzyskać informacje o sposobach korzystania z bramy z innymi usługami, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps lokalnej bramy danych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto służbowe](../active-directory/fundamentals/sign-up-organization.md) , które ma subskrypcję [platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) 

  Podczas instalacji bramy należy zalogować się do tego konta, aby umożliwić skojarzenie instalacji bramy z subskrypcją platformy Azure. 
  Później należy również użyć tego samego konta podczas tworzenia zasobu platformy Azure na potrzeby instalacji bramy w Azure Portal. 
  Jeśli nie masz jeszcze subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure</a>.

* Poniżej przedstawiono wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  * .NET Framework 4.5.2
  * 64 — bitowa wersja systemu Windows 7 lub Windows Server 2008 R2 (lub nowszego)

  **Zalecane wymagania**

  * 8-rdzeniowy procesor CPU
  * 8 GB pamięci
  * 64 — bitowa wersja systemu Windows Server 2012 R2 (lub nowszego)

* **Ważne zagadnienia**

  * Bramę danych lokalnych można zainstalować tylko na komputerze lokalnym, a nie na kontrolerze domeny. Nie trzeba jednak instalować bramy na tym samym komputerze, na którym znajduje się źródło danych. Ponadto potrzebna jest tylko jedna brama dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować opóźnienie, można zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze, który jest połączony z Internetem, zawsze włączone *i przejdź do* trybu uśpienia. W przeciwnym razie nie można uruchomić bramy. 
  Ponadto wydajność może być w sieci bezprzewodowej.

  * Podczas instalacji możesz zalogować się tylko przy użyciu [konta służbowego](../active-directory/sign-up-organization.md) , które jest zarządzane przez Azure Active Directory (Azure AD), na przykład @contoso.onmicrosoft.com, a nie konto B2B (gość) platformy Azure lub konto Microsoft osobiste, takie jak lub @hotmail.com @outlook.com. 
  Upewnij się, że używasz tego samego konta logowania podczas rejestrowania instalacji bramy w Azure Portal przez utworzenie zasobu bramy. 
  Następnie można wybrać ten zasób bramy podczas tworzenia połączenia z aplikacji logiki do lokalnego źródła danych. 
  [Dlaczego należy używać konta służbowego usługi Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Jeśli zarejestrujesz się w usłudze Office 365 i nie podano rzeczywistej służbowej poczty e-mail, być może adres logowania wygląda podobnie do tego przykładu:`username@domain.onmicrosoft.com` 
  >
  > Aby użyć konto Microsoft, która ma [subskrypcję programu Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/), należy najpierw [utworzyć katalog (dzierżawę) w Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md)lub użyć domyślnego katalogu z Twoim konto Microsoft. 
  > Dodaj użytkownika z hasłem do katalogu, a następnie nadaj temu użytkownikowi dostęp do subskrypcji. 
  > Następnie możesz zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

  * Region wybrany dla instalacji bramy określa lokalizację, w której później zarejestrowano swoją bramę na platformie Azure, przez utworzenie zasobu platformy Azure. 
  Podczas tworzenia tego zasobu bramy na platformie Azure należy wybrać tę *samą* lokalizację co instalacja bramy. Domyślnym regionem jest ta sama lokalizacja, w której znajduje się Twoja dzierżawa usługi Azure AD, która zarządza kontem platformy Azure, ale można zmienić lokalizację podczas instalacji bramy.

  * Jeśli masz już bramę, którą skonfigurowano z instalatorem wcześniejszym niż wersja 14.16.6317.4, nie możesz zmienić lokalizacji bramy przez uruchomienie ostatniego Instalatora. Można jednak użyć najnowszego Instalatora w celu skonfigurowania nowej bramy z zażądaną lokalizacją.
  
    Jeśli masz Instalatora bramy, który jest wcześniejszy niż wersja 14.16.6317.4, ale nie masz jeszcze zainstalowanej bramy, możesz pobrać i użyć najnowszego Instalatora.

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Lokalna Brama danych obsługuje wysoką dostępność, gdy istnieje więcej niż jedna instalacja bramy i ustawiana jako klaster. Jeśli masz istniejącą bramę po przejściu do tworzenia kolejnej bramy, możesz opcjonalnie utworzyć klastry o wysokiej dostępności. Te klastry organizują bramy w grupy, które mogą pomóc w uniknięciu pojedynczych punktów awarii. Ponadto wszystkie lokalne łączniki bramy danych obsługują teraz wysoką dostępność.

Aby korzystać z lokalnej bramy danych, zapoznaj się z następującymi wymaganiami i zagadnieniami:

* Musisz mieć już co najmniej jedną instalację bramy w ramach tej samej subskrypcji platformy Azure co Brama podstawowa i klucz odzyskiwania dla tej instalacji. 

* Brama podstawowa musi mieć uruchomioną aktualizację z listopada 2017 lub nowszą.

Po spełnieniu tych wymagań podczas tworzenia następnej bramy wybierz opcję **Dodaj do istniejącego klastra bramy**, wybierz bramę podstawową dla klastra i Podaj klucz odzyskiwania dla tej bramy podstawowej.
Aby uzyskać więcej informacji, zobacz [klastrów o wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz, Zapisz i uruchom Instalatora bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

2. Zaakceptuj domyślną ścieżkę instalacji lub określ lokalizację na komputerze, na którym chcesz zainstalować bramę.

3. Przejrzyj i zaakceptuj warunki użytkowania oraz zasady zachowania poufności informacji, a następnie wybierz pozycję **Zainstaluj**.

   ![Zaakceptuj warunki użytkowania i zasady zachowania poufności informacji](./media/logic-apps-gateway-install/accept-terms.png)

4. Po pomyślnym zainstalowaniu bramy Podaj adres e-mail konta służbowego, a następnie wybierz pozycję **Zaloguj się**.

   ![Zaloguj się przy użyciu konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Wybierz pozycję **zarejestruj nową bramę na tym komputerze** >  **, co**spowoduje zarejestrowanie instalacji bramy w [usłudze bramy w chmurze](#gateway-cloud-service). 

   ![Rejestrowanie bramy](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Podaj te informacje na potrzeby instalacji bramy:

   * Nazwa, którą chcesz zainstalować 

   * Klucz odzyskiwania, który chcesz utworzyć, który musi mieć co najmniej osiem znaków

     > [!IMPORTANT]
     > Zapisz i Zachowaj klucz odzyskiwania w bezpiecznym miejscu. Ten klucz jest wymagany w przypadku zmiany lokalizacji bramy lub migracji, odzyskania lub przejęcia istniejącej bramy.

   * Potwierdzenie klucza odzyskiwania 

     ![Konfigurowanie bramy](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Sprawdź region wybrany dla usługi bramy w chmurze i Azure Service Bus używany przez instalację bramy. 

   ![Sprawdź region](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Aby zmienić ten region po zakończeniu instalowania bramy, wymagany jest klucz odzyskiwania dla tej instalacji bramy. Ponadto należy odinstalować i ponownie zainstalować bramę. Aby uzyskać więcej informacji, zobacz [Zmienianie lokalizacji, migrowanie, odzyskiwanie lub przejmowanie istniejącej bramy](#update-gateway-installation).

   *Dlaczego należy zmienić region instalacji bramy?* 

   Na przykład w celu zmniejszenia opóźnień można zmienić region bramy na ten sam region, w którym znajduje się aplikacja logiki. 
   Możesz też wybrać region znajdujący się najbliżej lokalnego źródła danych. 
   *Zasób bramy na platformie Azure* i aplikacja logiki mogą mieć różne lokalizacje.

8. Aby zaakceptować domyślny region, wybierz pozycję **Konfiguruj**. Aby zmienić domyślny region, wykonaj następujące kroki:

   1. Obok bieżącego regionu wybierz pozycję **Zmień region**. 

      ![Zmień region](./media/logic-apps-gateway-install/change-region.png)

   2. Na następnej stronie Otwórz listę **Wybierz region** , wybierz żądany region, a następnie wybierz pozycję **gotowe**.

      ![Wybierz inny region](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Po wyświetleniu strony potwierdzenia wybierz pozycję **Zamknij**. 

   Instalator potwierdzi, że brama jest teraz w trybie online i jest gotowa do użycia.

   ![Zakończono bramę](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Teraz Zarejestruj swoją bramę na platformie Azure, [tworząc zasób platformy Azure na potrzeby instalacji bramy](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmień lokalizację, Migruj, Przywróć lub Przejmij istniejącą bramę

Jeśli konieczna jest zmiana lokalizacji bramy, przeniesienie instalacji bramy na nowy komputer, odzyskanie uszkodzonej bramy lub przejęcie na własność istniejącej bramy wymaga klucza odzyskiwania, który został dostarczony podczas instalacji bramy. Ta akcja spowoduje odłączenie starej bramy.

1. W **Panelu sterowania**komputera przejdź do pozycji **programy i funkcje**. Na liście Programy wybierz pozycję **lokalna Brama danych**, a następnie wybierz pozycję **Odinstaluj**.

2. [Ponownie zainstaluj lokalną bramę danych](https://aka.ms/on-premises-data-gateway-installer).

3. Po otwarciu Instalatora Zaloguj się przy użyciu tego samego konta służbowego, które było wcześniej używane do zainstalowania bramy.

4. Wybierz pozycję **Migruj, Przywróć lub przejęcie istniejącej bramy**, a następnie wybierz przycisk **dalej**.

   ![Wybieranie opcji "Migruj, Przywróć lub przejęcie istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. W obszarze **dostępne bramy** lub **dostępne klastry bramy**wybierz instalację bramy, którą chcesz zmienić. Wprowadź klucz odzyskiwania instalacji bramy. 

   ![Wybierz bramę podstawową](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Aby zmienić region, wybierz pozycję **Zmień region** i nowy region.

7. Gdy skończysz, wybierz pozycję **Konfiguruj**.

## <a name="configure-proxy-or-firewall"></a>Konfigurowanie serwera proxy lub zapory

Lokalna Brama danych tworzy połączenie wychodzące do [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Jeśli środowisko pracy wymaga, aby ruch przechodził przez serwer proxy w celu uzyskania dostępu do Internetu, to ograniczenie może uniemożliwić nawiązywanie połączenia między bramą danych a usługą bramy w chmurze. Aby określić, czy sieć używa serwera proxy, zapoznaj się z tym artykułem w witrynie superuser.com: 

[Jak mogę wiesz, z jakim serwerem proxy korzystam? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Aby zapewnić informacje o serwerze proxy dla bramy, zobacz [Konfigurowanie ustawień serwera proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Aby sprawdzić, czy Twój serwer proxy lub Zapora może blokować połączenia, sprawdź, czy maszyna może rzeczywiście łączyć się z Internetem i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). W wierszu polecenia programu PowerShell uruchom następujące polecenie:

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

## <a name="configure-ports"></a>Konfigurowanie portów

Brama tworzy połączenie wychodzące do [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) i komunikuje się na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 za pośrednictwem 9354. Brama nie wymaga portów przychodzących. Dowiedz się więcej o [Azure Service Bus i rozwiązaniach hybrydowych](../service-bus-messaging/service-bus-messaging-overview.md).

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

W niektórych przypadkach Azure Service Bus połączenia są nawiązywane przy użyciu adresów IP, a nie w pełni kwalifikowanych nazw domen. W związku z tym możesz chcieć dozwolonych adresy IP dla obszaru danych w zaporze. Aby dozwolonych adresy IP zamiast domen, można pobrać [listę zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych i korzystać z niej Microsoft Azure. Adresy IP na tej liście znajdują się w notacji [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

### <a name="force-https-communication-with-azure-service-bus"></a>Wymuś komunikację HTTPS z Azure Service Bus

Niektóre serwery proxy umożliwiają ruch tylko do portów 80 i 443. Domyślnie komunikacja z Azure Service Bus odbywa się na portach innych niż 443.
Można wymusić komunikację bramy z Azure Service Bus za pośrednictwem protokołu HTTPS zamiast bezpośredniego połączenia TCP, ale może to znacznie zmniejszyć wydajność. Aby wykonać to zadanie, wykonaj następujące kroki:

1. Przejdź do lokalizacji klienta lokalnej bramy danych, który zwykle można znaleźć tutaj:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   W przeciwnym razie, aby znaleźć lokalizację klienta, Otwórz konsolę usługi na tym samym komputerze, Znajdź **usługę lokalna Brama danych**i Wyświetl **ścieżkę do właściwości pliku wykonywalnego** .

2. Otwórz ten plik *konfiguracji* : **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Zmień wartość **ServiceBusSystemConnectivityModeString** z **autowykrywania** na **https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Konto usługi systemu Windows

Na komputerze, na którym jest instalowana lokalna Brama danych, Brama jest uruchamiana jako konto usługi systemu Windows o nazwie "lokalna Brama danych". Jednak brama używa nazwy "NT SERVICE\PBIEgwService" na potrzeby poświadczeń konta "Logowanie w trybie as". Domyślnie brama ma uprawnienia "Logowanie w trybie usługi" na komputerze, na którym zainstalowano bramę. Konto usługi systemu Windows dla bramy zwykle różni się od konta używanego do łączenia się z lokalnymi źródłami danych oraz z konta służbowego używanego do logowania się w usługach w chmurze.

Aby można było utworzyć i obsłużyć bramę w Azure Portal, to konto usługi systemu Windows musi mieć co najmniej uprawnienia współautora. Aby sprawdzić te uprawnienia, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Uruchom ponownie bramę

Brama danych działa jako usługa okna, tak jak jakakolwiek inna usługa systemu Windows, można uruchomić i zatrzymać bramę na różne sposoby. Na przykład można otworzyć wiersz polecenia z podwyższonym poziomem uprawnień na komputerze, na którym Brama jest uruchomiona, i uruchomić jedno z następujących poleceń:

* Aby zatrzymać usługę, uruchom następujące polecenie:
  
  `net stop PBIEgwService`

* Aby uruchomić usługę, uruchom następujące polecenie:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy 

Obecnie nie ma jednego miejsca, w którym administratorzy dzierżawy mogą zarządzać wszystkimi bramami zainstalowanymi i skonfigurowanymi przez innych użytkowników. Jeśli jesteś administratorem dzierżawy, możesz chcieć, aby użytkownicy w organizacji mogli dodać Cię jako administratora dla każdej bramy, którą zainstalują. W ten sposób można zarządzać wszystkimi bramami w organizacji za pomocą strony Ustawienia bramy lub [poleceń programu PowerShell](/data-integration/gateway/service-gateway-powershell-support). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Jak działa Brama?

Brama danych ułatwia szybką i bezpieczną komunikację między aplikacją logiki, usługą bramy w chmurze i lokalnym źródłem danych. Usługa bramy w chmurze szyfruje i przechowuje poświadczenia źródła danych oraz szczegóły bramy. Usługa kieruje także zapytania i ich wyniki między aplikacją logiki, lokalną bramą danych i źródłem danych lokalnego. 

Brama współpracuje z zaporami i używa tylko połączeń wychodzących. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Brama przekazuje dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem Azure Service Bus. Ta usługa Service Bus tworzy kanał między bramą a usługą wywołującą, ale nie przechowuje żadnych danych. Wszystkie dane przesyłane przez bramę są szyfrowane.

![Diagram — dla lokalnego-usługi Data-Gateway-Flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

W tych krokach opisano, co się dzieje, gdy użytkownik w chmurze współdziała z elementem połączonym z lokalnym źródłem danych:

1. Usługa bramy w chmurze tworzy zapytanie wraz z zaszyfrowanymi poświadczeniami dla źródła danych i wysyła zapytanie do kolejki w celu przetworzenia przez bramę.

2. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do Azure Service Bus.

3. Lokalna Brama danych sonduje Azure Service Bus dla oczekujących żądań.

4. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłem danych przy użyciu tych poświadczeń.

5. Brama wysyła zapytanie do źródła danych w celu wykonania.

6. Wyniki są wysyłane ze źródła danych z powrotem do bramy, a następnie do usługi bramy w chmurze. Następnie Usługa bramy w chmurze używa wyników.

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

* Instalacja bramy została już zarejestrowana i przejęta przez inny zasób bramy na platformie Azure. Instalacje bramy nie pojawiają się na liście wystąpień po utworzeniu zasobów bramy.
Aby sprawdzić rejestracje bramy w Azure Portal, przejrzyj wszystkie zasoby platformy Azure z typem **lokalnych bram danych** dla *wszystkich* subskrypcji platformy Azure. 

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
2. Z menu po lewej stronie wybierz pozycję **Diagnostyka**.
3. W obszarze **dzienniki bramy**wybierz pozycję **Eksportuj dzienniki**.

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
2. Rozwiń węzeł **Podgląd zdarzeń (lokalne)**  > **Dzienniki aplikacji i usług**. 
3. Wybierz pozycję **Usługa lokalnej bramy danych**.

   ![Wyświetlanie dzienników zdarzeń dla bramy](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Przejrzyj niską wydajność zapytań

Jeśli okaże się, że zapytania działają wolno przez bramę, można włączyć dodatkowe rejestrowanie, które generuje zapytania i ich czasy trwania. Te dzienniki mogą pomóc w znalezieniu, które zapytania są wolne lub długotrwałe. Aby dostroić wydajność zapytań, może być konieczne zmodyfikowanie źródła danych, na przykład dostosowanie indeksów dla zapytań SQL Server.

Aby określić czas trwania zapytania, wykonaj następujące kroki:

1. Przejdź do tej samej lokalizacji, w której znajduje się klient bramy, który zwykle można znaleźć tutaj:```C:\Program Files\On-premises data gateway```

   W przeciwnym razie, aby znaleźć lokalizację klienta, Otwórz konsolę usługi na tym samym komputerze, Znajdź **usługę lokalna Brama danych**i Wyświetl **ścieżkę do właściwości pliku wykonywalnego** .

2. Otwórz i edytuj te pliki konfiguracji zgodnie z opisem:

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

3. Aby znaleźć czas trwania zapytania, wykonaj następujące kroki:

   1. [](#logs) Wyeksportuj i Otwórz dziennik bramy.

   2. Aby znaleźć zapytanie, Wyszukaj typ działania, na przykład: 

      | Typ działania | Opis | 
      |---------------|-------------| 
      | MGEQ | Zapytania, które działają przez ADO.NET. | 
      | MGEO | Zapytania, które działają za pośrednictwem OLEDB. | 
      | MGEM | Zapytania uruchamiane z aparatu programu mashup. | 
      ||| 

   3. Zwróć uwagę na drugi identyfikator GUID, który jest IDENTYFIKATORem żądania.

   4. Kontynuuj wyszukiwanie typu działania, dopóki nie znajdziesz wpisu o nazwie "FireActivityCompletedSuccessfullyEvent", który ma czas trwania w milisekundach. 
   Potwierdź, że wpis ma ten sam identyfikator żądania, na przykład:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > Wpis "FireActivityCompletedSuccessfullyEvent" jest wpisem w trybie pełnym i nie jest rejestrowany, chyba że ustawienie "Traceverbosity" ma wartość Level 5.

### <a name="trace-traffic-with-fiddler"></a>Ruch śledzenia z programu Fiddler

[Programu Fiddler](https://www.telerik.com/fiddler) to bezpłatne narzędzie z Telerik, które monitoruje ruch HTTP. Ten ruch można sprawdzić za pomocą usługa Power BI z komputera klienckiego. Ta usługa może zawierać błędy i inne powiązane informacje.

## <a name="next-steps"></a>Kolejne kroki
    
* [Nawiązywanie połączenia z danymi lokalnymi z usługi Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
