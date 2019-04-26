---
title: Monitoruje stan replikacji usługi Active Directory z usługą Azure Monitor | Dokumentacja firmy Microsoft
description: Pakiet rozwiązań stan replikacji usługi Active Directory regularnie monitoruje środowiska usługi Active Directory pod kątem awarii replikacji.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: f7bbde98c6ef35021cc03b2646193d3601ca1cff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60495175"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitoruje stan replikacji usługi Active Directory z usługą Azure Monitor

![Symbol stanu replikacji usługi AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Usługa Active Directory jest kluczowym elementem środowisko IT przedsiębiorstwa. Aby zapewnić wysoką dostępność i wysoką wydajność, każdy kontroler domeny ma własną kopię bazy danych usługi Active Directory. Kontrolery domeny nie zreplikują ze sobą w celu propagujące zmiany w całym przedsiębiorstwie. W tym procesie replikacji mogą powodować różne problemy w całym przedsiębiorstwie.

Pakiet rozwiązań stan replikacji usługi AD regularnie monitoruje środowiska usługi Active Directory pod kątem awarii replikacji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

### <a name="install-agents-on-domain-controllers"></a>Instalowanie agentów na kontrolerach domeny
Konieczne jest zainstalowanie agentów na kontrolerach domeny, które są członkami domeny, która ma zostać obliczone. Alternatywnie należy zainstalować agentów na serwerach członkowskich i konfigurować agentów do przesyłania danych replikacji usługi AD do usługi Azure Monitor. Aby dowiedzieć się, jak połączyć komputery Windows do usługi Azure Monitor, zobacz [Windows łączenie komputerów do usługi Azure Monitor](../../azure-monitor/platform/agent-windows.md). Jeśli kontroler domeny jest już częścią istniejącego środowiska programu System Center Operations Manager, którą chcesz nawiązać połączenie z usługi Azure Monitor, zobacz [łączenie programu Operations Manager do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Włącz niebędącym kontrolerem domeny
Nie chcesz połączyć dowolny z kontrolerami domeny bezpośrednio do usługi Azure Monitor, można użyć dowolnego innego komputera w domenie podłączone do usługi Azure Monitor, zbieranie danych dla pakietu rozwiązań stan replikacji usługi AD i Wyślij dane.

1. Sprawdź, czy komputer jest członkiem domeny, który chcesz monitorować za pomocą rozwiązania stan replikacji usługi AD.
2. [Podłącz komputer Windows do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md) lub [połączyć za pomocą istniejącego środowiska programu Operations Manager do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md), jeśli nie jest już połączony.
3. Na tym komputerze należy ustawić następujący klucz rejestru:<br>Klucz: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Wartość: **IsTarget**<br>Dane wartości: **true**

   > [!NOTE]
   > Te zmiany nie obowiązywać dopiero po ponownym uruchomieniu usługi Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Zainstalować rozwiązanie
   > Wykonaj czynności opisane w [zainstalować rozwiązanie do monitorowania](solutions.md#install-a-monitoring-solution) dodać **stan replikacji usługi Active Directory** rozwiązania do obszaru roboczego usługi Log Analytics. Nie są wymagane żadne dalsze czynności konfiguracyjne.


## <a name="ad-replication-status-data-collection-details"></a>Szczegóły kolekcji danych stanu replikacji usługi AD
W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące jak zbierane są dane dotyczące stanu replikacji usługi AD.

| Platforma | Agent bezpośredni | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |co pięć dni |



## <a name="understanding-replication-errors"></a>Informacje o błędach replikacji

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Kafelek stanu replikacji usługi AD Wyświetla ile błędy replikacji aktualnie zainstalowana. **Błędy krytyczne replikacji** błędów, które są co najmniej 75% [okres istnienia reliktu](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) dla lasu usługi Active Directory.

![Kafelek stanu replikacji usługi AD](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknięciu kafelka, możesz wyświetlić więcej informacji na temat błędów.
![Pulpit nawigacyjny stan replikacji usługi AD](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stan serwera docelowego i stan serwera źródłowego
Te kolumny zawierają stan serwerów docelowych i serwerów źródłowych, w których występują błędy replikacji. Liczba po każdej nazwy kontrolera domeny wskazuje liczbę błędów replikacji na tym kontrolerze domeny.

Błędy Aby uzyskać zarówno serwerów docelowych, jak i serwerów źródłowych są wyświetlane, ponieważ niektóre problemy mogą pozwalać na łatwiejsze Rozwiązywanie problemów z perspektywy serwera źródłowego i innych z perspektywy serwera docelowego.

W tym przykładzie widać, że wiele serwerów docelowych około mają taką samą liczbę błędów, ale ma jednego serwera źródłowego (ADDC35), który ma wiele więcej błędów niż wszystkie inne. Jest prawdopodobne, że na ADDC35 powodujące się zakończyć się niepowodzeniem z wysyłania danych do swoich partnerów replikacji jest problem. Rozwiązywanie problemów na ADDC35 może rozwiązać wiele błędów, które są wyświetlane w obszarze serwer docelowy.

### <a name="replication-error-types"></a>Typy błędów replikacji
Ten obszar zawiera informacje o typach błędy wykryte w całym przedsiębiorstwie. Każdy z błędów ma unikatowy kod wartości liczbowych i komunikat, który może pomóc w określeniu przyczyny błędu.

Wykres pierścieniowy u góry daje wyobrażenie które błędy występują więcej i rzadziej w danym środowisku.

Przedstawia on po wielu kontrolerów domeny wystąpi ten sam błąd replikacji. W tym przypadku możesz może mieć możliwość odnajdowania lub Zidentyfikuj rozwiązania na jeden kontroler domeny, a następnie powtórzyć go na pozostałych kontrolerach domeny, które wpływa ten sam błąd.

### <a name="tombstone-lifetime"></a>Okres istnienia reliktu
Okres istnienia reliktu Określa, jak długo usuniętego obiektu, nazywany reliktów, są przechowywane w bazie danych usługi Active Directory. Po pomyślnej okres istnienia reliktu usuniętego obiektu procesu kolekcji wyrzucania elementów automatycznie usuwane z bazy danych usługi Active Directory.

Domyślny okres istnienia reliktu jest 180 dni, aby uzyskać najnowsze wersje systemu Windows, ale była 60 dni w starszych wersjach i może zostać zmieniona w jawnie przez administratora usługi Active Directory.

Należy znać, jeśli występują błędy replikacji, które są zbliża się lub dla których minął okres istnienia reliktu. Jeśli dwa kontrolery domeny, wystąpi błąd replikacji, która utrwala upłynął okres istnienia reliktu, wyłączyć replikacji między te dwa kontrolery domeny, nawet wtedy, gdy podstawowy błąd replikacji zostanie usunięty.

W obszarze okres istnienia reliktu zawarto informacje ułatwiające zidentyfikowanie miejsc, w którym wyłączone replikacji jest występuje. Każdy błąd **ponad 100% TSL** kategorii reprezentuje partycji, które nie zostały zreplikowane między jej serwerze źródłowym i docelowym dla co najmniej okres istnienia reliktu lasu.

W takiej sytuacji wystarczy naprawia błąd replikacji nie będzie wystarczająca. Jako minimum musisz ręcznie zbadać, aby zidentyfikować i wyczyścić lingering obiektów przed ponownym uruchomieniem replikacji. Może być konieczne nawet likwidowanie kontrolera domeny.

Oprócz identyfikowania wszelkie błędy replikacji, które zostały utrwalone upłynął okres istnienia reliktu, należy zwrócić uwagę na wszelkie błędy należących do **TSL 50-75%** lub **TSL 75 100%** kategorii.

Są to błędy, które są wyraźnie pokutujących, nie jest przejściowy, dlatego prawdopodobnie należy rozwiązać żadnej interwencji użytkownika. Dobra wiadomość jest, że ich jeszcze nie osiągnęły okres istnienia reliktu. Jeśli niezwłocznie rozwiązać te problemy i *przed* znajdą okres istnienia reliktu, replikację można uruchomić ponownie przy minimalnej interwencji ręcznej.

Jak wspomniano wcześniej, Kafelek pulpitu nawigacyjnego dla rozwiązania stan replikacji usługi AD pokazuje liczbę *krytyczne* błędy replikacji w danym środowisku, która jest zdefiniowana jako błędy, które są ponad 75% okres istnienia reliktu (łącznie z błędami które są ponad 100% TSL). Dokładamy wszelkich starań zachować tę liczbę od 0.

> [!NOTE]
> Wszystkie reliktu okres istnienia procent obliczenia są oparte na okres istnienia reliktu rzeczywiste dla lasu usługi Active Directory, dzięki czemu można ufać, że te wartości procentowe są dokładne, nawet jeśli użytkownik ma wartość okresu istnienia reliktu niestandardowego, ustaw.
>
>

### <a name="ad-replication-status-details"></a>Szczegóły stanu replikacji usługi AD
Po kliknięciu dowolnego elementu w wykazie, zobaczysz dodatkowe szczegóły dotyczące za pomocą zapytań log. Wyniki są filtrowane w celu wyświetlenia tylko błędy związane z tym elementem. Na przykład kliknięcie pierwszego kontrolera domeny na liście **stan serwera docelowego (ADDC02)**, zostaną wyświetlone wyniki zapytania filtrowana w celu Pokaż błędy przy użyciu tego kontrolera domeny jest wymieniony jako serwer docelowy:

![Błędy stanu replikacji usługi AD w wynikach kwerendy](./media/ad-replication-status/oms-ad-replication-search-details.png)

W tym miejscu można dalej filtrować, zmodyfikuj zapytanie dziennika i tak dalej. Aby uzyskać więcej informacji o używaniu zapytań dzienników w usłudze Azure Monitor, zobacz [Analizuj dane dzienników w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

**HelpLink** polu wyświetlany adres URL strony TechNet, z dodatkowymi szczegółami dotyczącymi tego wystąpienia określonego błędu. Można skopiować i wkleić ten link do okna przeglądarki w celu wyświetlenia informacji na temat rozwiązywania problemów i usunięciu błędu.

Możesz również kliknąć **wyeksportować** wyeksportować wyniki do programu Excel. Eksportowanie danych może pomóc w wizualizacji danych błąd replikacji w jakikolwiek sposób, który chcesz.

![wyeksportowane błędy stanu replikacji usługi AD w programie Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Stan replikacji usługi AD — często zadawane pytania
**Pyt.: Jak często jest aktualizacja danych stanu replikacji usługi AD?**
Odp.: Informacje są aktualizowane co pięć dni.

**Pyt.: Czy istnieje sposób, aby skonfigurować, jak często dane są aktualizowane?**
Odp.: Nie w tej chwili.

**Pyt.: Należy dodać wszystkie moje kontrolerów domeny do mojego obszaru roboczego usługi Log Analytics, aby można było wyświetlić stan replikacji?**
Odp.: Nie można dodać tylko jednego kontrolera domeny. Jeśli masz wiele kontrolerów domeny w obszarze roboczym usługi Log Analytics, dane ze wszystkich z nich są wysyłane do usługi Azure Monitor.

**Pyt.: Nie chcę dodać kontrolerów domeny do mojego obszaru roboczego usługi Log Analytics. Można nadal korzystać z rozwiązania stan replikacji usługi AD?**

Odp.: Tak. Można ustawić wartość klucza rejestru, aby go włączyć. Zobacz [Włącz niebędącym kontrolerem domeny](#enable-non-domain-controller).

**Pyt.: Co to jest nazwa procesu, który wykonuje zbierania danych?**
Odp.: AdvisorAssessment.exe

**Pyt.: Jak długo trwa dla danych, które mają być zbierane?**
Odp.: Czas zbierania danych zależy od wielkości środowiska usługi Active Directory, ale zazwyczaj zajmuje mniej niż 15 minut.

**Pyt.: Jakiego typu dane są zbierane?**
Odp.: Informacje o replikacji są zbierane za pośrednictwem protokołu LDAP.

**Pyt.: Czy istnieje sposób, aby skonfigurować po zebraniu danych?**
Odp.: Nie w tej chwili.

**Pyt.: Jakie uprawnienia należy zbierać dane?**
Odp.: Uprawnieniami zwykłego użytkownika w usłudze Active Directory są wystarczające.

## <a name="troubleshoot-data-collection-problems"></a>Rozwiązywanie problemów kolekcji danych
Aby zbierać dane, pakiet rozwiązań stan replikacji usługi AD wymaga co najmniej jeden kontroler domeny do podłączenia do obszaru roboczego usługi Log Analytics. Dopiero po nawiązaniu połączenia z kontrolerem domeny, zostanie wyświetlony komunikat, który **jest wciąż są zbierane dane**.

Jeśli potrzebujesz pomocy przy podłączaniu jeden z kontrolerów domeny, można wyświetlić dokumentację w [Windows łączenie komputerów do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md). Alternatywnie, jeśli kontroler domeny jest podłączony do istniejącego środowiska programu System Center Operations Manager, można wyświetlić dokumentację na [połączyć System Center Operations Manager do usługi Azure Monitor](../../azure-monitor/platform/om-agents.md).

Jeśli nie chcesz połączyć dowolny z kontrolerami domeny bezpośrednio do usługi Azure Monitor lub System Center Operations Manager, zobacz [Włącz niebędącym kontrolerem domeny](#enable-non-domain-controller).

## <a name="next-steps"></a>Kolejne kroki
* Użyj [rejestrowania zapytań w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe dane stanu replikacji usługi Active Directory.
