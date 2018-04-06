---
title: Brama danych lokalnych | Dokumentacja firmy Microsoft
description: Jest to konieczne, jeśli serwer usług Analysis Services na platformie Azure zostaną podłączone do lokalnych źródeł danych brama lokalnego.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/03/2018
ms.author: owend
ms.openlocfilehash: f38faf4d75125201ee209eb40c9f223a0487f042
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Łączenie z lokalnych źródeł danych z bramą danych lokalnych Azure
Brama danych lokalna działa jako mostka zapewnianie bezpiecznego transferu danych między lokalnych źródeł danych i serwerów usług Azure Analysis Services w chmurze. Oprócz Praca z wieloma serwerami usług Azure Analysis Services, w tym samym regionie, najnowszą wersję bramy współdziała również z usługi Azure Logic Apps, usługi Power BI aplikacje zasilania i Flow firmy Microsoft. Wiele usług w tym samym regionie można skojarzyć z pojedynczą bramą. 

Pobieranie Instalatora z bramą po raz pierwszy jest procesem czteroczęściową:

- **Pobierz i uruchom Instalatora** -tego kroku Usługa bramy są instalowane na komputerze w organizacji. Możesz również logowanie do platformy Azure przy użyciu konta w sieci [dzierżawcy](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) usługi Azure AD. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane.

- **Zarejestruj bramę** — w tym kroku określ nazwę i odzyskiwanie klucza bramy i wybierz region, rejestrowanie bramy z usługi bramy w chmurze. Zasobu bramy może być zarejestrowane w dowolny region, ale zaleca się ona w tym samym regionie co serwery usług Analysis Services. 

- **Tworzenie zasobu bramy na platformie Azure** — w tym kroku tworzenia zasobu bramy w Twojej subskrypcji platformy Azure.

- **Połącz serwery z zasobu bramy** — po utworzeniu zasobu bramy w ramach subskrypcji, możesz rozpocząć nawiązywania serwerów. Możesz połączyć wiele serwerów i innych zasobów do niego.

Aby rozpocząć pracę od razu, zobacz [Zainstaluj i skonfiguruj bramę danych lokalnych](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Jak to działa
Bramy, zainstalować na komputerze w organizacji działa jako usługa systemu Windows, **bramy danych lokalnych**. Ta usługa lokalna jest zarejestrowany z usługi bramy w chmurze za pomocą usługi Azure Service Bus. Następnie można utworzyć zasobu bramy usługi bramy w chmurze dla subskrypcji platformy Azure. Serwery usług Azure Analysis Services są następnie podłączone do zasobu bramy. Jeśli modele na serwerze muszą połączyć się z lokalnymi danymi źródła dla zapytań lub przetwarzania, przechodzi przez przepływu zapytań i danych zasobów bramy, usługi Azure Service Bus, Usługa bramy lokalnej lokalnymi danymi i źródeł danych. 

![Jak to działa](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Zapytania i przepływ danych:

1. Zapytanie jest tworzony przez usługę w chmurze z zaszyfrowane poświadczenia dla źródła danych lokalnych. Następnie jest wysyłane do kolejki dla bramy do przetworzenia.
2. Usługi w chmurze bramy analizuje zapytania i żądanie wypchnięcia [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Brama danych lokalnych sonduje Azure Service Bus dla żądań oczekujących.
4. Bramy pobiera kwerendy odszyfrowuje poświadczenia i nawiązuje połączenie ze źródłami danych z tych poświadczeń.
5. Brama wysyła zapytanie do źródła danych do wykonania.
6. Wyniki są wysyłane ze źródła danych, wróć do bramy, a następnie na usługę w chmurze i serwer.

## <a name="windows-service-account"> </a>Konto usługi systemu Windows
Brama danych lokalnych jest skonfigurowana do używania *NT SERVICE\PBIEgwService* dla poświadczeń logowania usługi systemu Windows. Domyślnie ma uprawnienie do logowania jako usługa; w kontekście urządzenia, na którym instalujesz bramę na. To poświadczenie nie jest kontem używanym do łączenia się ze źródłami danych lokalnych lub konta platformy Azure.  

Jeśli wystąpią problemy z serwerem proxy z powodu uwierzytelniania, można zmienić konto usługi systemu Windows dla użytkownika domeny lub konta usługi zarządzanego.

## <a name="ports"> </a>Porty
Brama tworzy wychodzące połączenie do usługi Azure Service Bus. Komunikuje się ona portów wychodzących: TCP 443 (ustawienie domyślne), 5671, 5672, 9350 za pośrednictwem 9354.  Brama nie jest wymagane porty dla ruchu przychodzącego.

Firma Microsoft zaleca dozwolonych adresów IP w Twoim regionie danych w zaporze. Możesz pobrać [listy Microsoft Azure Datacenter IP](https://www.microsoft.com/download/details.aspx?id=41653). Ta lista jest aktualizowana co tydzień.

> [!NOTE]
> Adresy IP na liście adresów IP centrum danych Azure na liście znajdują się w notacji CIDR. Aby dowiedzieć się więcej, zobacz [Bezklasowego routingu międzydomenowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Poniżej przedstawiono w pełni kwalifikowane nazwy domeny używane przez bramę.

| Nazwy domen | Porty wyjściowe | Opis |
| --- | --- | --- |
| *.powerbi.com |80 |Protokół HTTP używany do pobierania Instalatora. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Zaawansowane kolejkowania wiadomości protokołu (protokół AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Obiekty nasłuchujące na przekaźnik magistrali usług za pośrednictwem protokołu TCP (wymaga 443 dla tokenu przejęcie kontroli dostępu) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Używany do sprawdzania łączności z Internetem, gdy brama jest nieosiągalny przez usługę Power BI. |
| *.microsoftonline-p.com |443 |Używany do uwierzytelniania w zależności od konfiguracji. |

### <a name="force-https"></a>Wymuszanie komunikację HTTPS z usługi Azure Service Bus
Możesz wymusić bramy do komunikacji z usługi Azure Service Bus przy użyciu protokołu HTTPS zamiast bezpośredniego TCP; Jednak to tak może znacznie zmniejszyć wydajność. Można zmodyfikować *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* pliku, zmieniając wartość `AutoDetect` do `Https`. Ten plik znajduje się zwykle w *bramy danych lokalnych Files\On C:\Program*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Często zadawane pytania

### <a name="general"></a>Ogólne

**Q**: należy bramy dla źródeł danych w chmurze, takich jak bazy danych SQL Azure? <br/>
**A**: nie. Brama jest niezbędne do nawiązywania połączenia z lokalną tylko źródła danych.

**Q**: czy bramy musi być zainstalowany na tym samym komputerze jako źródło danych? <br/>
**A**: nie. Bramy wystarczy możliwość łączenia się z serwerem, zwykle w tej samej sieci.

<a name="why-azure-work-school-account"></a>

**Q**: Dlaczego należy używać do logowania konta firmowego lub szkolnego? <br/>
**A**: można używać tylko organizacji konta firmowego lub szkolnego po zainstalowaniu bramy danych lokalnych. I że konto musi należeć do tej samej dzierżawy jako subskrypcji możesz konfigurowania zasobu bramy. Twoje konto logowania są przechowywane w dzierżawie, który jest zarządzany przez usługę Azure Active Directory (Azure AD). Główna nazwa użytkownika konta usługi Azure AD (UPN) jest zgodna zwykle adres e-mail.

**Q**: moich poświadczeń przechowywania? <br/>
**A**: poświadczenia dla źródła danych są szyfrowane i przechowywane w usłudze w chmurze bramy. Poświadczenia są odszyfrowywane w bramie danych lokalnych.

**Q**: istnieją wszelkie wymagania dotyczące przepustowości sieci? <br/>
**A**: zalecane jest dobrym przepływności ma połączenie sieciowe. Każde środowisko jest inne, a ilość danych wysyłanych ma wpływ na wyniki. Użycie usługi ExpressRoute może pomóc do zapewnienia poziomu przepływności między lokalnymi i centrach danych platformy Azure.
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
**A**: usług w bramie nosi nazwę lokalną usługę bramy danych.

**Q**: można uruchomić bramy usługi systemu Windows przy użyciu konta usługi Azure Active Directory? <br/>
**A**: nie. Usługa systemu Windows muszą mieć prawidłowe konto systemu Windows. Domyślnie usługa jest uruchamiana z identyfikatorem SID usługi NT SERVICE\PBIEgwService.

**Q**: jak przejęcia bramę? <br/>
**A**: przejęcia bramy (uruchamiając ustawienia/zmiany w Panelu sterowania > Programy), musisz być właścicielem zasobu bramy na platformie Azure i klucz odzyskiwania. Właścicieli zasobów bramy są konfigurowane w kontroli dostępu.

### <a name="high-availability"></a>Wysoka dostępność i odzyskiwanie po awarii

**Q**: jakie opcje są dostępne dla odzyskiwania po awarii? <br/>
**A**: klucz odzyskiwania służy do przywracania, lub Przenieś bramy. Po zainstalowaniu bramy, należy określić klucz odzyskiwania.

**Q**: co to jest korzyść klucz odzyskiwania? <br/>
**A**: klucz odzyskiwania pozwala przeprowadzić migrację lub odzyskiwanie po awarii ustawienia bramy.

## <a name="troubleshooting"> </a>Rozwiązywanie problemów

**Q**: Dlaczego nie widzę mojego bramy na liście wystąpieniach bramy podczas próby utworzenia zasobu bramy na platformie Azure? <br/>
**A**: istnieją dwie możliwe przyczyny. Najpierw jest już utworzony zasób dla bramy w bieżącej lub niektóre z innej subskrypcji. Aby wyeliminować tej możliwości, wyliczyć zasobów typu **bram danych lokalnych** z portalu. Upewnij się wybrać wszystkie subskrypcje podczas wyliczania wszystkich zasobów. Po utworzeniu zasobu bramy nie znajdują się na liście wystąpienie bramy w portalu środowisko tworzenia zasobu bramy. Druga możliwość jest to użytkownik zalogowany do portalu Azure usługi Azure AD tożsamości użytkownika, który zainstalował bramy. Aby rozwiązać, zaloguj się do portalu jako użytkownik, który zainstalował bramy przy użyciu tego samego konta.

**Q**: jak wyświetlić co zapytania są wysyłane do lokalnego źródła danych? <br/>
**A**: można włączyć funkcja śledzenia zapytań, który zawiera zapytania, które są wysyłane. Pamiętaj, aby zmienić zapytania śledzenia wstecz do oryginalnej wartości po zakończeniu rozwiązywania problemów. Jeśli pozostanie włączona funkcja śledzenia zapytań tworzy większe dzienniki.

Można też przyjrzeć się narzędzia, które ma źródła danych śledzenia zapytań. Na przykład można użyć zdarzeń rozszerzonych lub profilera SQL dla programu SQL Server i usług Analysis Services.

**Q**: gdzie są dzienniki bramy? <br/>
**A**: Zobacz dzienniki w dalszej części tego artykułu.

### <a name="update"></a>Aktualizowanie do najnowszej wersji

Wiele problemów można powierzchni, gdy wersja bramy stanie się nieaktualna. Dobrym rozwiązaniem ogólne upewnij się, że używasz najnowszej wersji. Brama nie były aktualizowane przez miesiąc lub dłużej, może być zaleca się zainstalowanie najnowszej wersji bramy i zobacz, czy można odtworzyć problem.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Błąd: Nie można dodać użytkownika do grupy. (Użytkownicy dzienników wydajności PBIEgwService-2147463168)

Ten błąd może pobrać, Jeśli spróbujesz zainstalować bramę na kontrolerze domeny, który nie jest obsługiwany. Upewnij się, że wdrożyć bramę na komputerze, na którym nie jest kontrolerem domeny.

## <a name="logs"></a>Dzienniki

Pliki dziennika są zasobów ważne podczas rozwiązywania problemów.

#### <a name="enterprise-gateway-service-logs"></a>Dzienniki usługi bramy przedsiębiorstwa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Dzienniki konfiguracji

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Dzienniki zdarzeń

Można znaleźć w dziennikach brama zarządzania danymi i PowerBIGateway w obszarze **Dzienniki aplikacji i usług**.


## <a name="telemetry"></a>Telemetrii
Dane telemetryczne może służyć do monitorowania i rozwiązywania problemów. Domyślnie

**Aby włączyć telemetrii**

1.  Sprawdź katalogu lokalnego danych bramy klienta na komputerze. Zazwyczaj jest **bramy danych %systemdrive%\Program lokalnych Files\On**. Lub, otwórz konsolę usługi i Sprawdź ścieżkę do pliku wykonywalnego: właściwość lokalnego usługi bramy danych.
2.  W pliku Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config z katalogu klienta. Zmień ustawienie SendTelemetry na wartość true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Zapisz zmiany i ponownie uruchomić usługę systemu Windows: Usługa bramy danymi lokalnymi.




## <a name="next-steps"></a>Kolejne kroki
* [Zainstaluj i skonfiguruj bramę danych lokalnych](analysis-services-gateway-install.md).   
* [Zarządzanie usług Analysis Services](analysis-services-manage.md)
* [Pobieranie danych z usług Azure Analysis Services](analysis-services-connect.md)
