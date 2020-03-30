---
title: Monitorowanie stanu replikacji usługi Active Directory
description: Pakiet rozwiązań Stanu replikacji usługi Active Directory regularnie monitoruje środowisko usługi Active Directory pod kątem błędów replikacji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055900"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorowanie stanu replikacji usługi Active Directory za pomocą usługi Azure Monitor

![Symbol stanu replikacji usługi AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Usługa Active Directory jest kluczowym składnikiem korporacyjnego środowiska IT. Aby zapewnić wysoką dostępność i wysoką wydajność, każdy kontroler domeny ma własną kopię bazy danych usługi Active Directory. Kontrolery domeny replikują się ze sobą w celu propagowania zmian w całym przedsiębiorstwie. Błędy w tym procesie replikacji może spowodować wiele problemów w całym przedsiębiorstwie.

Rozwiązanie Stanu replikacji usługi AD regularnie monitoruje środowisko usługi Active Directory pod kątem wszelkich błędów replikacji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

### <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie stanu replikacji usługi AD wymaga obsługiwanej wersji programu .NET Framework 4.6.2 lub nowszej zainstalowanej na każdym komputerze z zainstalowanym agentem usługi Log Analytics dla systemu Windows (zwanym również agentem microsoft monitoring agent (MMA)).  Agent jest używany przez System Center 2016 — Operations Manager, Operations Manager 2012 R2 i Azure Monitor.
* Rozwiązanie obsługuje kontrolery domeny z systemami Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2 oraz Windows Server 2016.
* Obszar roboczy usługi Log Analytics w celu dodania rozwiązania do sprawdzania kondycji usługi Active Directory z portalu Azure w portalu Azure. Nie jest wymagana żadna dodatkowa konfiguracja.


### <a name="install-agents-on-domain-controllers"></a>Instalowanie agentów na kontrolerach domeny
Należy zainstalować agentów na kontrolerach domeny, które są członkami domeny, które mają zostać ocenione. Można też zainstalować agentów na serwerach członkowskich i skonfigurować agentów do wysyłania danych replikacji usługi AD do usługi Azure Monitor. Aby dowiedzieć się, jak połączyć komputery z systemem Windows z usługą Azure Monitor, zobacz [Łączenie komputerów z systemem Windows z monitorem Azure](../../azure-monitor/platform/agent-windows.md). Jeśli kontroler domeny jest już częścią istniejącego środowiska programu Operations Manager centrum systemowego, które chcesz połączyć z usługą Azure Monitor, zobacz [Łączenie programu Operations Manager z usługą Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Włączanie kontrolera nie-kontrolera domeny
Jeśli nie chcesz łączyć żadnych kontrolerów domeny bezpośrednio z usługą Azure Monitor, możesz użyć dowolnego innego komputera w domenie połączonej z usługą Azure Monitor do zbierania danych dla pakietu rozwiązań Stany replikacji usługi AD i wysyłania danych.

1. Sprawdź, czy komputer jest członkiem domeny, którą chcesz monitorować przy użyciu rozwiązania Stan replikacji usługi AD.
2. [Podłącz komputer z systemem Windows do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md) lub [połącz go przy użyciu istniejącego środowiska programu Operations Manager z usługą Azure Monitor](../../azure-monitor/platform/om-agents.md), jeśli nie jest jeszcze połączony.
3. Na tym komputerze ustaw następujący klucz rejestru:<br>Klucz: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Parametry\Grupy\<zarządzania ManagementGroupName>\Solutions\ADReplication**<br>Wartość: **IsTarget**<br>Dane wartości: **true**

   > [!NOTE]
   > Te zmiany nie zostaną wprowadzone do czasu ponownego uruchomienia usługi Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Rozwiązanie instalacyjne
   > Postępuj zgodnie z procesem opisanym w [zainstaluj rozwiązanie do monitorowania,](solutions.md#install-a-monitoring-solution) aby dodać rozwiązanie stanu **replikacji usługi Active Directory** do obszaru roboczego usługi Log Analytics. Nie są wymagane żadne dalsze czynności konfiguracyjne.


## <a name="ad-replication-status-data-collection-details"></a>Szczegóły zbierania danych o stanie replikacji usługi AD
W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych dla stanu replikacji usługi AD.

| platforma | Agent bezpośredni | Agent SCOM | Azure Storage | SCOM wymagane? | Dane agenta SCOM przesyłane za pośrednictwem grupy zarządzania | częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |co pięć dni |



## <a name="understanding-replication-errors"></a>Opis błędów replikacji

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Kafelek Stan replikacji usługi AD wyświetla liczbę błędów replikacji. **Błędy replikacji krytycznej** to błędy, które są na poziomie lub powyżej 75% [okresu istnienia reliktu](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) dla lasu usługi Active Directory.

![Kafelek stanu replikacji usługi AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknięciu kafelka można wyświetlić więcej informacji o błędach.
![Pulpit nawigacyjny stanu replikacji usługi AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stan serwera docelowego i stan serwera źródłowego
W tych kolumnach są wyświetlane stan serwerów docelowych i serwerów źródłowych, na których występują błędy replikacji. Numer po każdej nazwie kontrolera domeny wskazuje liczbę błędów replikacji na tym kontrolerze domeny.

Błędy zarówno dla serwerów docelowych, jak i serwerów źródłowych są wyświetlane, ponieważ niektóre problemy są łatwiejsze do rozwiązania z punktu widzenia serwera źródłowego, a inne z punktu widzenia serwera docelowego.

W tym przykładzie widać, że wiele serwerów docelowych ma mniej więcej taką samą liczbę błędów, ale istnieje jeden serwer źródłowy (ADDC35), który ma o wiele więcej błędów niż wszystkie inne. Jest prawdopodobne, że istnieje jakiś problem na ADDC35, który powoduje, że nie można wysłać danych do swoich partnerów replikacji. Rozwiązanie problemów na ADDC35 może rozwiązać wiele błędów, które pojawiają się w obszarze serwera docelowego.

### <a name="replication-error-types"></a>Typy błędów replikacji
Ten obszar zawiera informacje o typach błędów wykrytych w całym przedsiębiorstwie. Każdy błąd ma unikatowy kod numeryczny i komunikat, który może pomóc w określeniu głównej przyczyny błędu.

Pączek u góry daje wyobrażenie, które błędy pojawiają się coraz rzadziej w twoim środowisku.

Pokazuje, gdy wiele kontrolerów domeny doświadcza tego samego błędu replikacji. W takim przypadku można odnajdywać lub identyfikować rozwiązanie na jednym kontrolerze domeny, a następnie powtarzać je na innych kontrolerach domeny, których dotyczy ten sam błąd.

### <a name="tombstone-lifetime"></a>Okres istnienia nagrobka
Okres istnienia reliktu określa, jak długo usunięty obiekt, określany jako relikt, jest zachowywany w bazie danych usługi Active Directory. Gdy usunięty obiekt przechodzi okres istnienia reliktu, proces wyrzucania elementów bezużytecznych automatycznie usuwa go z bazy danych usługi Active Directory.

Domyślny okres istnienia reliktu wynosi 180 dni dla najnowszych wersji systemu Windows, ale w starszych wersjach było to 60 dni i może zostać zmieniona jawnie przez administratora usługi Active Directory.

Ważne jest, aby wiedzieć, czy masz błędy replikacji, które zbliżają się lub są przeszłości okresu istnienia reliktu. Jeśli na dwóch kontrolerach domeny wystąpi błąd replikacji, który utrzymuje się po okresie istnienia reliktu, replikacja jest wyłączona między tymi dwoma kontrolerami domeny, nawet jeśli błąd replikacji podstawowej jest naprawiony.

Obszar Okres istnienia reliktu pomaga zidentyfikować miejsca, w których replikacja wyłączona jest zagrożona. Każdy błąd w kategorii **Ponad 100% TSL** reprezentuje partycję, która nie została zreplikowana między serwerem źródłowym i docelowym przez co najmniej okres istnienia reliktu dla lasu.

W tej sytuacji po prostu naprawienie błędu replikacji nie wystarczy. Przed ponownym uruchomieniem replikacji należy co najmniej ręcznie zbadać, aby zidentyfikować i oczyścić obiekty powieszające. Może być nawet konieczne wycofanie kontrolera domeny.

Oprócz identyfikowania błędów replikacji, które utrwaliły się po okresie istnienia reliktu, należy również zwrócić uwagę na wszelkie błędy wpadające w kategorie **TSL 50-75%** lub **75-100% TSL.**

Są to błędy, które są wyraźnie utrzymujące się, a nie przejściowe, więc prawdopodobnie potrzebują interwencji, aby rozwiązać. Dobrą wiadomością jest to, że nie osiągnęły jeszcze życia nagrobka. Jeśli problem ten zostanie szybko rozwiązany i *zanim* osiągną okres istnienia reliktu, replikacja może zostać ponownie uruchomiona przy minimalnej interwencji ręcznej.

Jak wspomniano wcześniej, kafelek pulpitu nawigacyjnego dla rozwiązania stan replikacji usługi AD pokazuje liczbę błędów replikacji *krytycznej* w środowisku, który jest zdefiniowany jako błędy, które są ponad 75% okresu istnienia reliktu (w tym błędy, które są ponad 100% TSL). Staraj się utrzymać tę liczbę na 0.

> [!NOTE]
> Wszystkie obliczenia procentowe okresu istnienia reliktu są oparte na rzeczywistym okresie istnienia reliktu dla lasu usługi Active Directory, dzięki czemu można ufać, że te wartości procentowe są dokładne, nawet jeśli ustawiono niestandardową wartość okresu istnienia reliktu.
>
>

### <a name="ad-replication-status-details"></a>Szczegóły stanu replikacji usługi AD
Po kliknięciu dowolnego elementu na jednej z list, zobaczysz dodatkowe szczegóły na jego temat za pomocą kwerendy dziennika. Wyniki są filtrowane, aby wyświetlić tylko błędy związane z tym elementem. Na przykład po kliknięciu pierwszego kontrolera domeny wymienionego w obszarze **Stan serwera docelowego (ADDC02)** zostaną wyświetlone wyniki kwerendy filtrowane w celu wyświetlenia błędów z tym kontrolerem domeny wymienionym jako serwer docelowy:

![Błędy stanu replikacji usługi AD w wynikach kwerendy](./media/ad-replication-status/oms-ad-replication-search-details.png)

W tym miejscu można filtrować dalej, modyfikować kwerendę dziennika i tak dalej. Aby uzyskać więcej informacji na temat używania zapytań dziennika w usłudze Azure Monitor, zobacz [Analizowanie danych dziennika w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

Pole **HelpLink** zawiera adres URL strony TechNet z dodatkowymi szczegółami dotyczącymi tego konkretnego błędu. Możesz skopiować i wkleić to łącze do okna przeglądarki, aby wyświetlić informacje dotyczące rozwiązywania problemów i naprawiania błędu.

Można również kliknąć przycisk **Eksportuj,** aby wyeksportować wyniki do programu Excel. Eksportowanie danych może pomóc w wizualizacji danych błędu replikacji w dowolny sposób.

![eksportowane błędy stanu replikacji usługi AD w programie Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>— często zadawane pytania dotyczące stanu replikacji usługi AD
**Pyt.: Jak często są aktualizowane dane o stanie replikacji usługi AD?**
Odp.: Informacje są aktualizowane co pięć dni.

**Pyt.: Czy istnieje sposób, aby skonfigurować, jak często te dane są aktualizowane?**
O: Nie w tej chwili.

**Pyt.: Czy muszę dodać wszystkie kontrolery domeny do mojego obszaru roboczego usługi Log Analytics, aby zobaczyć stan replikacji?**
Odp.: Nie, należy dodać tylko jeden kontroler domeny. Jeśli masz wiele kontrolerów domeny w obszarze roboczym usługi Log Analytics, dane ze wszystkich z nich są wysyłane do usługi Azure Monitor.

**P: Nie chcę dodawać żadnych kontrolerów domeny do mojego obszaru roboczego usługi Log Analytics. Czy nadal mogę korzystać z rozwiązania Stan replikacji usługi AD?**

Odp. Tak. Można ustawić wartość klucza rejestru, aby go włączyć. Zobacz [Włączanie kontrolera nie-kontrolera domeny](#enable-non-domain-controller).

**Pyt.: Jaka jest nazwa procesu, który zbiera dane?**
Odp.: AdvisorAssessment.exe

**Pyt.: Jak długo trwa zbieranie danych?**
Odp.: Czas zbierania danych zależy od rozmiaru środowiska usługi Active Directory, ale zwykle trwa mniej niż 15 minut.

**P: Jakiego rodzaju dane są zbierane?**
Odp.: Informacje o replikacji są zbierane za pośrednictwem protokołu LDAP.

**Pyt.: Czy istnieje sposób konfigurowania podczas zbierania danych?**
O: Nie w tej chwili.

**Pyt.: Jakie uprawnienia są potrzebne do zbierania danych?**
Odp.: Normalne uprawnienia użytkownika do usługi Active Directory są wystarczające.

## <a name="troubleshoot-data-collection-problems"></a>Rozwiązywanie problemów z gromadzeniem danych
Aby zbierać dane, pakiet rozwiązań Stan replikacji usługi AD wymaga połączenia co najmniej jednego kontrolera domeny z obszarem roboczym usługi Log Analytics. Dopóki nie połączysz kontrolera domeny, pojawi się komunikat informujący, że **dane są nadal zbierane**.

Jeśli potrzebujesz pomocy dotyczącej łączenia jednego z kontrolerów domeny, możesz wyświetlić dokumentację w [witrynie Podłącz komputery z systemem Windows do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md). Alternatywnie, jeśli kontroler domeny jest już połączony z istniejącym środowiskiem programu System Center Operations Manager, można wyświetlić dokumentację w [programie Connect System Center Operations Manager w usłudze Azure Monitor](../../azure-monitor/platform/om-agents.md).

Jeśli nie chcesz łączyć żadnego kontrolera domeny bezpośrednio z usługą Azure Monitor lub z programem System Center Operations Manager, zobacz [Włączanie kontrolera niezwiązanego](#enable-non-domain-controller)z domeną .

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dziennika w usłudze Azure Monitor,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe dane stanu replikacji usługi Active Directory.
