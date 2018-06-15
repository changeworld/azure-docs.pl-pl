---
title: Monitoruje stan replikacji usługi Active Directory z Azure Log Analytics | Dokumentacja firmy Microsoft
description: Stan replikacji usługi Active Directory pakiet rozwiązania regularnie monitoruje środowiska usługi Active Directory dla wszelkie błędy replikacji.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7707c4a1afdc42ef44a7b6f761ceb03b7e7da2f0
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2018
ms.locfileid: "29179338"
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Monitoruje stan replikacji usługi Active Directory z analizy dzienników

![Symbol stan replikacji usługi AD](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Usługa Active Directory jest kluczowym elementem w środowisku INFORMATYCZNYM przedsiębiorstwa. Aby zapewnić wysoką dostępność i wysoką wydajność, każdy kontroler domeny ma własną kopię bazy danych usługi Active Directory. Kontrolery domeny są replikowane ze sobą w celu zmiany są propagowane na przedsiębiorstwa. W tym procesie replikacji mogą powodować wiele problemów z całym przedsiębiorstwie.

Stan replikacji AD pakiet rozwiązania regularnie monitoruje środowiska usługi Active Directory dla wszelkie błędy replikacji.

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania.

* Należy zainstalować agentów na kontrolerach domeny, które są członkami domeny, która ma zostać obliczone. Lub, musisz zainstalować agentów na serwerach członkowskich i konfigurowanie agentów na wysyłanie danych replikacji AD z analizą dzienników. Aby zrozumieć, jak połączyć się analizy dzienników z komputerów z systemem Windows, temacie [połączyć komputery do analizy dzienników](log-analytics-windows-agent.md). Jeśli kontroler domeny jest już częścią istniejącego środowiska System Center Operations Manager, który chcesz połączyć z analizą dzienników, zobacz [połączenie programu Operations Manager do analizy dzienników](log-analytics-om-agents.md).
* Dodaj rozwiązanie stan replikacji usługi Active Directory do obszaru roboczego analizy dzienników przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).  Nie są wymagane żadne dalsze czynności konfiguracyjne.

## <a name="ad-replication-status-data-collection-details"></a>Szczegóły kolekcji danych stanu replikacji usługi AD
W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak zbierane są dane, stan replikacji usługi AD.

| Platformy | Bezpośrednie agenta | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość kolekcji |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |co pięć dni |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Opcjonalnie możesz włączyć kontrolera domeny z systemem innym niż do przesyłania danych AD do analizy dzienników
Jeśli nie chcesz do poszczególnych kontrolerów domeny bezpośrednie łączenie się z analizy dzienników służy dowolnego innego komputera w domenie podłączone do analizy dzienników do zbierania danych dla pakietu rozwiązania stan replikacji usługi AD i go wysłać dane.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Aby włączyć kontrolera domeny z systemem innym niż do przesyłania danych AD do analizy dzienników
1. Sprawdź, czy komputer jest członkiem domeny, którą chcesz monitorować za pomocą rozwiązania stan replikacji usługi AD.
2. [Podłącz komputer z systemem Windows do analizy dzienników](log-analytics-windows-agent.md) lub [podłącz go przy użyciu istniejącego środowiska programu Operations Manager do analizy dzienników](log-analytics-om-agents.md), jeśli nie jest już połączony.
3. Na tym komputerze należy ustawić następujący klucz rejestru:

   * Klucz: **grup wartość HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<ManagementGroupName > \Solutions\ADReplication**
   * Wartość: **IsTarget**
   * Dane wartości: **wartość true**

   > [!NOTE]
   > Te zmiany nie będą obowiązywać do Twojej ponowne uruchomienie usługi Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Opis błędów replikacji
Po umieszczeniu danych stanu replikacji AD wysyłane do analizy dzienników, zapoznaj się kafelka podobne do następujących obrazu w wskazującą liczbę błędów replikacji aktualnie analizy dzienników.  
![Stan replikacji AD kafelka](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Błędy krytyczne replikacji** błędów, które są co najmniej 75% [okresu istnienia reliktu](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) dla lasu usługi Active Directory.

Po kliknięciu kafelka, można wyświetlić więcej informacji o błędach.
![Pulpit nawigacyjny stan replikacji usługi AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stan serwera docelowego i stan serwera źródłowego
Te kolumny zawierają stan serwerów docelowych i serwerów źródłowych, których wystąpiły błędy replikacji. Liczba po nazwie kontrolera domeny wskazuje liczbę błędów replikacji na tym kontrolerze domeny.

Błędy dla serwerów docelowych i serwerów źródłowych są wyświetlane, ponieważ niektóre problemy są łatwiejsze Rozwiązywanie problemów z perspektywy serwera źródłowego i inne osoby z perspektywy serwera docelowego.

W tym przykładzie widać, że wiele serwerów docelowych około mają taką samą liczbę błędów, ale istnieje jeden serwer źródłowy (ADDC35), który ma wiele błędów więcej niż wszystkie inne obiekty. Istnieje prawdopodobieństwo, że na ADDC35 powodujący go nie wysyłać danych do partnerów replikacji jest pewien problem. Rozwiązywanie problemów na ADDC35 może rozwiązać wiele błędów, które są wyświetlane w obszarze serwera docelowego.

### <a name="replication-error-types"></a>Typy błędów replikacji
Ten obszar zawiera informacje o typach błędy wykryte w całym przedsiębiorstwie. Każdy z błędów ma unikatowy kod numeryczne i komunikat, który może pomóc w określeniu przyczyny błędu.

Pierścień u góry daje pomysł, które błędy występują więcej i rzadziej w danym środowisku.

Przedstawia on po wielu kontrolerów domeny występuje ten sam błąd replikacji. W takim przypadku użytkownik może mieć możliwość odnajdowania lub identyfikowania rozwiązania na jeden kontroler domeny, a następnie powtórz go na innych kontrolerach domeny, które wpływa ten sam błąd.

### <a name="tombstone-lifetime"></a>Okresu istnienia reliktu
Okresu istnienia reliktu Określa, jak długo usunięty obiekt określany jako reliktów, są przechowywane w bazie danych usługi Active Directory. Gdy usuniętego obiektu przekazuje okresu istnienia reliktu, proces zbierania odzyskiwanie automatycznie spowoduje usunięcie jej z bazy danych usługi Active Directory.

Domyślnego okresu istnienia reliktu jest 180 dni do najnowszej wersji systemu Windows, ale było 60 dni w starszych wersjach i można go zmienić jawnie przez administratora usługi Active Directory.

Należy znać, jeśli występują błędy replikacji, które zbliża się lub upłynął okres istnienia reliktu. Jeśli dwa kontrolery domeny wystąpi błąd replikacji, która będzie się powtarzać, poza okresu istnienia reliktu, wyłączyć replikacji między te dwa kontrolery domeny, nawet wtedy, gdy podstawowy błąd replikacji jest stały.

Obszar okresu istnienia reliktu pomaga zidentyfikować miejsca, w którym wyłączone replikacji jest w toku. Każdy z błędów w **ponad 100% TSL** kategorii reprezentuje partycji, która nie została zreplikowana między swojego serwera źródłowego i docelowego dla co najmniej okresu istnienia reliktu lasu.

W takim przypadku po prostu naprawienie błędu replikacji nie będzie wystarczającej ilości. Co najmniej należy zbadać ręcznie do identyfikowania i wyczyścić pokutujące obiekty przed ponownym uruchomieniem replikacji. Może być konieczne nawet likwidowania kontrolera domeny.

Oprócz identyfikujące wszelkie błędy replikacji, które trwają poza okresu istnienia reliktu, należy zwrócić uwagę na błędów należących do **TSL 50 75%** lub **TSL 75 100%** kategorii.

Są to błędy, które są wyraźnie pokutujących, nie jest przejściowe, więc prawdopodobnie potrzebują rozpoznać żadnej interwencji użytkownika. Dobre wieści jest, że ich nie osiągnęły jeszcze okresu istnienia reliktu. Jeśli szybko rozwiązać te problemy i *przed* osiągną okresu istnienia reliktu, replikacji można ponownie uruchomić z minimalnym ręcznej interwencji.

Jak wspomniano wcześniej, kafelka pulpitu nawigacyjnego dla rozwiązania stan replikacji AD pokazuje liczbę *krytyczne* błędy replikacji w danym środowisku, która jest zdefiniowana jako błędy, które są ponad 75% okresu istnienia reliktu (w tym błędy które są ponad 100% TSL). Dokładamy wszelkich starań zachować ten numer od 0.

> [!NOTE]
> Wszystkie reliktu okres istnienia procent jest obliczany na podstawie okresu istnienia reliktu rzeczywiste lasu usługi Active Directory, więc ufasz, że te wartości procentowe są poprawne, nawet jeśli ma wartość okresu istnienia reliktu niestandardowych, ustaw.
>
>

### <a name="ad-replication-status-details"></a>Szczegóły stanu replikacji usługi AD
Kliknij dowolny element w wykazie, pojawić się dodatkowe szczegóły dotyczące za pomocą wyszukiwania dziennika. Wyniki są filtrowane w celu wyświetlania tylko błędy związane z tym elementem. Na przykład kliknięcie pierwszego kontrolera domeny są wyświetlane w obszarze **stan serwera docelowego (ADDC02)**, zobacz wyniki wyszukiwania odfiltrowana Pokaż błędy przy użyciu tego kontrolera domeny jest wymieniony jako serwer docelowy:

![Błędy stanu replikacji usługi AD w wynikach wyszukiwania](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

W tym miejscu można filtrować dalsze, zmodyfikuj zapytanie wyszukiwania i tak dalej. Aby uzyskać więcej informacji o używaniu wyszukiwania dziennika, zobacz [dziennika wyszukiwania](log-analytics-log-searches.md).

**HelpLink** pole zawiera adres URL strony TechNet, dodatkowe informacje dotyczące tego błędu. Można skopiować i wkleić to łącze w swojej przeglądarce, aby wyświetlić informacje dotyczące rozwiązywania problemów i usunięciu błędu.

Możesz również kliknąć **wyeksportować** do wyeksportowania wyników do programu Excel. Eksportowanie danych może pomóc wizualizacji danych błąd replikacji w żaden sposób, który chcesz.

![błędy stanu replikacji AD wyeksportowanego w programie Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Stan replikacji AD — często zadawane pytania
**Pytanie: jak często jest zaktualizowane dane o stanie replikacji AD?**
Odpowiedź: informacje są aktualizowane co pięć dni.

**Pytanie: czy istnieje sposób konfigurowania, jak często są aktualizowane dane?**
Odpowiedź: nie w tej chwili.

**Pytanie: należy dodać wszystkie moje kontrolerów domeny do mojego obszaru roboczego analizy dzienników, aby wyświetlić stan replikacji?**
Odpowiedź: nie można dodać tylko jednego kontrolera domeny. Jeśli masz wiele kontrolerów domeny w obszarze roboczym analizy dzienników, dane ze wszystkich z nich jest wysyłane do analizy dzienników.

**Pytanie: nie chcę dodać wszystkie kontrolery domeny do mojego obszaru roboczego analizy dzienników. Można nadal korzystać z rozwiązania stan replikacji usługi AD?**
Odpowiedź: tak. Można ustawić wartość klucza rejestru, aby ją włączyć. Zobacz [umożliwiające kontrolera domeny z systemem innym niż do przesyłania danych AD do analizy dzienników](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**Pytanie: co to jest nazwa procesu, który wykonuje zbierania danych?**
A: AdvisorAssessment.exe

**Pytanie: jak długo trwa do zebrania danych?**
A: czas zbierania danych zależy od rozmiaru środowiska usługi Active Directory, ale zazwyczaj trwa mniej niż 15 minut.

**Pytanie: typ danych zbieranych?**
A: informacje o replikacji zbieranych za pośrednictwem protokołu LDAP.

**Pytanie: czy istnieje sposób konfigurowania, gdy dane są zbierane?**
Odpowiedź: nie w tej chwili.

**Pytanie: jakie uprawnienia należy zbierać dane?**
Odpowiedź: normalnymi uprawnieniami użytkownika do usługi Active Directory są wystarczające.

## <a name="troubleshoot-data-collection-problems"></a>Rozwiązywanie problemów zbierania danych
Aby zbierać dane, stan replikacji AD pakiet rozwiązania wymaga co najmniej jeden kontroler domeny do podłączenia do obszaru roboczego analizy dzienników. Dopiero po nawiązaniu połączenia kontrolera domeny, pojawi się komunikat wskazujący, że **nadal są zbierane dane**.

Jeśli potrzebujesz pomocy przy podłączaniu jeden z kontrolerów domeny, można wyświetlić dokumentację w [połączyć komputery do analizy dzienników](log-analytics-windows-agent.md). Alternatywnie, jeśli kontroler domeny jest już połączony z istniejącym środowiskiem programu System Center Operations Manager, można wyświetlić dokumentację w [połączyć System Center Operations Manager do analizy dzienników](log-analytics-om-agents.md).

Jeśli nie chcesz połączyć poszczególnych kontrolerów domeny, bezpośrednio do analizy dzienników lub System Center Operations Manager, zobacz [umożliwiające kontrolera domeny z systemem innym niż do przesyłania danych AD do analizy dzienników](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane stanu replikacja usługi Active Directory.
