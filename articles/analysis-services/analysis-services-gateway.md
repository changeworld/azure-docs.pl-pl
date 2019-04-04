---
title: Brama danych lokalnych
description: Lokalna brama jest konieczne, jeśli serwer usług Analysis Services na platformie Azure połączy się z lokalnymi źródłami danych.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f13dd1282a6384a0acca4c6936fe7900a051795f
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896027"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Nawiązywanie połączenia z lokalnymi źródłami danych za pomocą lokalnej bramy danych
Lokalna brama danych zapewnia bezpieczny transfer danych między lokalnymi źródłami danych i serwerami usług Azure Analysis Services w chmurze. Oprócz Praca z wieloma serwerami usług Azure Analysis Services, w tym samym regionie, najnowszą wersję bramy współpracuje również z usługi Azure Logic Apps, usługa Power BI, Power Apps i Microsoft Flow. Można skojarzyć wiele usług w tej samej subskrypcji i tym samym regionie za pośrednictwem jednej bramy. 

Pobieranie Instalatora bramy po raz pierwszy jest procesem czteroczęściową:

- **Pobierz i uruchom Instalatora** — ta czynność powoduje zainstalowanie usługi bramy na komputerze w organizacji. Możesz też zarejestrować się w na platformie Azure przy użyciu konta w swojej [dzierżawcy](/previous-versions/azure/azure-services/jj573650(v=azure.100)#BKMK_WhatIsAnAzureADTenant) usługi Azure AD. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane.

- **Zarejestrować bramę** — w tym kroku należy określić nazwę i odzyskiwanie klucza dla bramy i wybierz region, rejestrowanie bramy przy użyciu usługi bramy w chmurze. Zasób bramy można zarejestrować w każdym regionie, ale zaleca się znajdować się w tym samym regionie jako serwery usług Analysis Services. 

- **Tworzenie zasobu bramy na platformie Azure** — w tym kroku tworzenia zasobu bramy w ramach subskrypcji platformy Azure.

- **Połącz serwery z zasobu bramy** — po utworzeniu zasobu bramy w ramach subskrypcji, możesz rozpocząć łączenie serwerów z nim. Można połączyć wiele serwerów i innych zasobów, pod warunkiem znajdują się w tej samej subskrypcji i tym samym regionie.

Aby natychmiast rozpocząć pracę, zobacz [zainstalować i skonfigurować lokalną bramę danych](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Jak to działa
Bramę można zainstalować na komputerze w organizacji jest uruchamiana jako usługa Windows **lokalnej bramy danych**. Ta usługa lokalna jest zarejestrowany w usłudze bramy w chmurze za pomocą usługi Azure Service Bus. Następnie utwórz zasób bramy Usługa bramy w chmurze w Twojej subskrypcji platformy Azure. Serwery usług Azure Analysis Services są następnie połączone do zasobu bramy. Gdy modele na serwerze należy połączyć się z danymi lokalnymi źródeł dla zapytań lub przetwarzania, zapytania i przepływu danych przesyłane za pośrednictwem zasobu bramy usługi Azure Service Bus, usługa lokalnej bramy danych i źródeł danych. 

![Jak to działa](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Zapytania i przepływu danych:

1. Zapytanie jest tworzony przez usługę w chmurze z zaszyfrowanymi poświadczeniami dla źródła danych w środowisku lokalnym. Następnie wysyłana do kolejki do przetworzenia przez bramę.
2. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do [usługi Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Lokalna brama danych sonduje usługę Azure Service Bus, poszukując żądań oczekujących.
4. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłami danych przy użyciu tych poświadczeń.
5. Brama wysyła zapytanie do źródła danych do wykonania.
6. Wyniki są odsyłane ze źródła danych do bramy a następnie na usługę w chmurze i serwera.

## <a name="windows-service-account"> </a>Konto usługi Windows
Lokalna brama danych jest skonfigurowany do używania *NT SERVICE\PBIEgwService* Windows poświadczenia logowania do usług. Domyślnie ma uprawnienia do logowania jako usługa; w kontekście komputer, na którym ją instalujesz bramę. To poświadczenie nie jest tego samego konta, które są używane do łączenia z lokalnymi źródłami danych ani konto platformy Azure.  

Jeśli wystąpią problemy z serwerem proxy z powodu uwierzytelniania, można zmienić konto usługi Windows użytkownika domeny lub zarządzane konto usługi.

## <a name="ports"> </a>Ports
Brama tworzy połączenie wychodzące do usługi Azure Service Bus. Komunikuje się ona na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 do 9354.  Brama nie wymaga portów przychodzących.

Firma Microsoft zaleca listy dozwolonych adresów IP dla używanego regionu danych w zaporze. Możesz pobrać [listy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ta lista jest aktualizowana co tydzień.

> [!NOTE]
> Adresy IP wymienione na liście adresów IP centrów danych platformy Azure znajdują się w notacji CIDR. Aby dowiedzieć się więcej, zobacz [Bezklasowego routingu międzydomenowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Poniżej znajdują się w pełni kwalifikowanych nazw domen używanych przez bramę.

| Nazwy domen | Porty ruchu wychodzącego | Opis |
| --- | --- | --- |
| *.powerbi.com |80 |Protokół HTTP używany do pobierania Instalatora. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Odbiorniki usługi Service Bus Relay za pośrednictwem protokołu TCP (wymaga portu 443 uzyskania tokenu kontroli dostępu) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Używane do testowania łączności z Internetem, jeśli brama jest nieosiągalna przez usługę Power BI. |
| *.microsoftonline-p.com |443 |Używany do uwierzytelniania w zależności od konfiguracji. |

### <a name="force-https"></a>Wymuszanie komunikacji HTTPS z usługą Azure Service Bus
Możliwość wymuszenia na bramie, nawiązać połączenia z usługą Azure Service Bus przy użyciu protokołu HTTPS zamiast bezpośredniego połączenia TCP; jednak wykonanie tej tak może znaczne obniżenie wydajności. Możesz zmodyfikować *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* pliku, zmieniając wartość z `AutoDetect` do `Https`. Ten plik znajduje się zwykle w *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy 

Obecnie jest nie jednym miejscu, gdzie Administratorzy dzierżawy mogliby zarządzać wszystkich bram, które zainstalowano i skonfigurowano innym użytkownikom.  Jeśli jesteś administratorem dzierżawy, zaleca się, że zadajesz użytkowników w Twojej organizacji, aby dodał Cię jako administratora do każdej zainstalowanej bramy. Dzięki temu można zarządzać wszystkimi bramami w organizacji za pomocą strony Ustawienia bramy lub za pomocą [poleceń programu PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

**Q**: Czy potrzebuję bramy dla źródeł danych w chmurze, takich jak Azure SQL Database? <br/>
**Odp.:** Nie. Brama jest niezbędna do łączenia się z lokalnymi źródłami danych tylko.

**Q**: Czy brama musi być zainstalowany na tym samym komputerze co źródło danych? <br/>
**Odp.:** Nie. Brama musi po prostu możliwość łączenia się z serwerem, zazwyczaj w tej samej sieci.

<a name="why-azure-work-school-account"></a>

**Q**: Dlaczego trzeba użyć konta firmowego lub szkolnego do logowania? <br/>
**Odp.:** Organizacji pracy lub nauki można używać tylko po zainstalowaniu lokalnej bramy danych. Oraz że konto musi być w tej samej dzierżawie, co w przypadku subskrypcji możesz przeprowadzana zasobu bramy. Konto logowania są przechowywane w dzierżawie, który jest zarządzany przez usługę Azure Active Directory (Azure AD). Zazwyczaj główna nazwa użytkownika konta usługi Azure AD (UPN) jest zgodny z adresem e-mail.

**Q**: Gdzie przechowywane są moje poświadczenia? <br/>
**Odp.:** Poświadczenia wprowadzone dla źródła danych są szyfrowane i przechowywane w usłudze bramy w chmurze. Poświadczenia są odszyfrowywane w lokalnej bramy danych.

**Q**: Czy istnieją jakiekolwiek wymagania dotyczące przepustowości sieci? <br/>
**Odp.:** Zalecane jest, że połączenie sieciowe jest dobrej przepływności. Każde środowisko jest inne, a ilość wysyłanych danych ma wpływ na wyniki. Przy użyciu usługi ExpressRoute może pomóc w zapewnieniu poziomu przepływności między magazynami lokalnymi i centrami danych platformy Azure.
Można użyć aplikacji Azure Speed Test narzędzia innej firmy, by zmierzyć przepływność.

**Q**: Co to jest opóźnienie przed wykonaniem zapytań do źródła danych z bramy? Co to jest najlepsza architektura? <br/>
**Odp.:** Do zmniejszenia opóźnienia sieci, należy zainstalować bramę jak najbliżej źródła danych, jak to możliwe. Jeśli bramę można zainstalować w rzeczywistym źródle danych, tym odległości minimalizuje opóźnienia. Rozważ zbyt centrach danych. Na przykład jeśli usługa korzysta z centrum danych zachodnie stany USA, a masz programu SQL Server hostowanego w Maszynie wirtualnej platformy Azure, maszyny Wirtualnej platformy Azure należy w zachodnich stanach USA za. Ta odległości minimalizuje opóźnienia i pozwala uniknąć opłat za ruch wychodzący na maszynie Wirtualnej platformy Azure.

**Q**: Jak wyniki są odsyłane do chmury? <br/>
**Odp.:** Wyniki są wysyłane za pośrednictwem usługi Azure Service Bus.

**Q**: Czy istnieją połączenia przychodzące do bramy z chmury? <br/>
**Odp.:** Nie. Brama używa połączeń wychodzących do usługi Azure Service Bus.

**Q**: Co się stanie, jeśli zablokuję połączenia wychodzące? Co muszę otworzyć? <br/>
**Odp.:** Zobacz, portów i hostów używanych przez bramę.

**Q**: Co to jest nazwa rzeczywistej usługi Windows o nazwie?<br/>
**Odp.:** W usługach brama ma nazwę lokalną usługę bramy danych.

**Q**: Brama Windows service uruchomić przy użyciu konta usługi Azure Active Directory? <br/>
**Odp.:** Nie. Usługa Windows musi mieć prawidłowe konto Windows. Domyślnie ta usługa działa z identyfikatorem SID usługi NT SERVICE\PBIEgwService.

**Q**: Jak mogę przejęcia bramy? <br/>
**Odp.:** Do przejęcia bramy (uruchamiając Instalator/Zmień w Panelu sterowania > Programy), musisz być właścicielem zasobu bramy na platformie Azure i masz klucz odzyskiwania. Właściciele zasobów bramy są konfigurowane w kontroli dostępu.

### <a name="high-availability"></a>Wysoka dostępność i odzyskiwanie po awarii

**Q**: Jak firma Microsoft ma wysokiej dostępności  
**Odp.:** Bramę można zainstalować na innym komputerze, aby utworzyć klaster. Aby dowiedzieć się więcej, zobacz [klastry wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters) w dokumentacji programu Power BI Gateway.

**Q**: Jakie opcje są dostępne do odzyskania po awarii? <br/>
**Odp.:** Klucz odzyskiwania służy do przywrócenia lub przeniesienia bramy. Klucz odzyskiwania jest określany podczas instalowania bramy.

**Q**: Co to jest korzyść klucza odzyskiwania? <br/>
**Odp.:** Klucz odzyskiwania umożliwia migrację lub odzyskiwanie ustawień bramy po awarii.

## <a name="troubleshooting"> </a>Rozwiązywanie problemów

**Q**: Dlaczego nie widzisz bramy w listy wystąpień bramy, podczas próby utworzenia zasobu bramy na platformie Azure? <br/>
**Odp.:** Istnieją dwie możliwe przyczyny. Najpierw jest już utworzony zasób dla bramy w bieżącej lub niektórych innych subskrypcji. Aby wyeliminować tę możliwość, wyliczanie zasobów o typie **na poziomie lokalnych bram danych** z poziomu portalu. Upewnij się, że wybrano wszystkie subskrypcje, podczas wyliczania wszystkich zasobów. Po utworzeniu zasobu bramy nie ma na liście wystąpienia bramy w środowisku portalu tworzenia zasobu bramy. Druga możliwość jest inny niż użytkownik zalogowany do witryny Azure portal usługi Azure AD tożsamości użytkownika, który zainstalowano bramę. Aby rozwiązać problem, zaloguj się do portalu jako użytkownik, który zainstalowano bramę przy użyciu tego samego konta.

**Q**: Jak mogę sprawdzić, co zapytania są wysyłane do źródła danych w środowisku lokalnym? <br/>
**Odp.:** Można włączyć funkcję śledzenie zapytań, który zawiera zapytania, które są wysyłane. Pamiętaj, aby zmienić kwerendę, Śledzenie wstecz do oryginalnej wartości po zakończeniu rozwiązywania problemów. Pozostawienie włączona funkcja śledzenia zapytań tworzy większe dzienniki.

Można również przeglądać narzędzia, które ma źródła danych w celu śledzenia zapytań. Na przykład można użyć Extended Events lub SQL Profiler for SQL Server i usług Analysis Services.

**Q**: Gdzie znajdują się dzienniki bramy? <br/>
**Odp.:** Zobacz dzienniki w dalszej części tego artykułu.

### <a name="update"></a>Zaktualizuj do najnowszej wersji

Wersja bramy stanie się nieaktualna, może pojawić się wiele problemów. Dobrym rozwiązaniem jest ogólne upewnij się, że używasz najnowszej wersji. Brama nie była aktualizowana przez miesiąc lub dłużej, może warto zainstalować najnowszą wersję bramy i zobacz, czy można odtworzyć problem.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Błąd: Nie można dodać użytkownika do grupy. (-2147463168 PBIEgwService Użytkownicy dzienników wydajności)

Możesz otrzymać ten błąd przy próbie zainstalowania bramy na kontrolerze domeny, który nie jest obsługiwany. Upewnij się, że wdrożyć bramę na komputerze, na którym nie jest kontrolerem domeny.

## <a name="logs"></a>Dzienniki

Pliki dziennika są ważnym podczas rozwiązywania problemów.

#### <a name="enterprise-gateway-service-logs"></a>Dzienniki usługi bramy przedsiębiorstwa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Dzienniki konfiguracji

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`


#### <a name="event-logs"></a>Dzienniki zdarzeń

Można znaleźć w dziennikach bramy zarządzania danymi i PowerBIGateway w obszarze **Dzienniki aplikacji i usług**.


## <a name="telemetry"></a>Dane telemetryczne
Dane telemetryczne może służyć do monitorowania i rozwiązywania problemów. Domyślnie

**Aby włączyć telemetrię**

1.  Sprawdź katalog klienta bramy danych On-premises na komputerze. Zazwyczaj jest **%systemdrive%\Program Files\On-premises data gateway**. Alternatywnie możesz otworzyć konsolę usług i Sprawdź ścieżkę do pliku wykonywalnego: Właściwości usługi lokalnej bramy danych.
2.  W pliku Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config z katalogu klienta. Zmień ustawienie SendTelemetry na wartość true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Zapisz zmiany i ponownie uruchom usługę Windows: Usługa bramy danych lokalnych.




## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie i konfigurowanie bramy danych lokalnych](analysis-services-gateway-install.md).   
* [Zarządzanie usług Analysis Services](analysis-services-manage.md)
* [Pobieranie danych z usług Azure Analysis Services](analysis-services-connect.md)
