---
title: Grupy komputerów w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Grupy komputerów w usłudze Azure Monitor umożliwiają zakres zapytań dziennika do określonego zestawu komputerów.  W tym artykule opisano różne metody tworzenia grup komputerów i sposób ich używania w kwerendzie dziennika.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274777"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupy komputerów w kwerendach dziennika usługi Azure Monitor
Grupy komputerów w usłudze Azure Monitor umożliwiają zakres [zapytań dziennika](../log-query/log-query-overview.md) do określonego zestawu komputerów.  Każda grupa jest wypełniona komputerami przy użyciu zdefiniowanej kwerendy lub przez importowanie grup z różnych źródeł.  Gdy grupa jest uwzględniona w kwerendzie dziennika, wyniki są ograniczone do rekordów, które pasują do komputerów w grupie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Grupę komputerów można utworzyć w usłudze Azure Monitor przy użyciu dowolnej z metod w poniższej tabeli.  Szczegółowe informacje na temat każdej metody znajdują się w poniższych sekcjach. 

| Metoda | Opis |
|:--- |:--- |
| Kwerenda dziennika |Utwórz kwerendę dziennika, która zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Użyj interfejsu API wyszukiwania dzienników, aby programowo utworzyć grupę komputerów na podstawie wyników kwerendy dziennika. |
| Usługa Active Directory |Automatyczne skanowanie członkostwa w grupie wszystkich komputerów agenta, które są członkami domeny usługi Active Directory i utworzyć grupę w usłudze Azure Monitor dla każdej grupy zabezpieczeń. (tylko komputery z systemem Windows)|
| Configuration Manager | Importuj kolekcje z programu Microsoft Endpoint Configuration Manager i utwórz grupę w usłudze Azure Monitor dla każdego z nich. |
| Program Windows Server Update Services |Automatyczne skanowanie serwerów lub klientów usługi WSUS w poszukiwaniu grup kierowania i tworzenie grupy w usłudze Azure Monitor dla każdego z nich. |

### <a name="log-query"></a>Kwerenda dziennika
Grupy komputerów utworzone na podstawie kwerendy dziennika zawierają wszystkie komputery zwrócone przez zdefiniowaną kwerendę.  Ta kwerenda jest uruchamiana za każdym razem, gdy grupa komputerów jest używana, dzięki czemu wszelkie zmiany od momentu utworzenia grupy są odzwierciedlane.  

Można użyć dowolnej kwerendy dla grupy komputerów, ale musi `distinct Computer`ona zwrócić odrębny zestaw komputerów za pomocą programu .  Poniżej przedstawiono typową przykładową kwerendę, której można użyć jako grupy komputerów.

    Heartbeat | where Computer contains "srv" | distinct Computer

Poniższa procedura służy do tworzenia grupy komputerów z wyszukiwania dziennika w witrynie Azure portal.

1. Kliknij **pozycję Dzienniki** w menu **Azure Monitor** w witrynie Azure portal.
1. Utwórz i uruchom kwerendę zwracaą komputery, które mają być w grupie.
1. Kliknij **pozycję Zapisz** u góry ekranu.
1. Zmień **zapisz jako** **funkcję** i wybierz pozycję Zapisz **tę kwerendę jako grupę komputerów**.
1. Podaj wartości dla każdej właściwości dla grupy komputerów opisanej w tabeli i kliknij przycisk **Zapisz**.

W poniższej tabeli opisano właściwości definiujące grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Nazwa   | Nazwa kwerendy do wyświetlenia w portalu. |
| Alias funkcji | Unikatowy alias używany do identyfikowania grupy komputerów w kwerendzie. |
| Kategoria       | Kategoria do organizowania zapytań w portalu. |


### <a name="active-directory"></a>Usługa Active Directory
Podczas konfigurowania usługi Azure Monitor do importowania członkostwa w grupach usługi Active Directory analizuje członkostwo w grupie dowolnej domeny systemu Windows przyłączonych do różnych komputerów za pomocą agenta usługi Log Analytics.  Grupa komputerów jest tworzona w usłudze Azure Monitor dla każdej grupy zabezpieczeń w usłudze Active Directory, a każdy komputer z systemem Windows jest dodawany do grup komputerów odpowiadających grupom zabezpieczeń, do których są członkami.  To członkostwo jest stale aktualizowane co 4 godziny.  

> [!NOTE]
> Zaimportowane grupy usługi Active Directory zawierają tylko komputery z systemem Windows.

Usługę Azure Monitor można skonfigurować do importowania grup zabezpieczeń usługi Active Directory z **ustawień zaawansowanych** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **pozycję Grupy komputerów**, **Usługa Active Directory**, a następnie **importowanie członkostw grup usługi Active Directory z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usługi Active Directory](media/computer-groups/configure-activedirectory.png)

Po zaimportowaniu grup menu zawiera listę wykrytych komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych łączy, aby zwrócić rekordy **ComputerGroup** z tymi informacjami.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Podczas konfigurowania usługi Azure Monitor do importowania członkostwa w grupach programu WSUS analizuje członkostwo w grupie docelowej wszystkich komputerów za pomocą agenta usługi Log Analytics.  Jeśli korzystasz z kierowania po stronie klienta, każdy komputer, który jest połączony z usługą Azure Monitor i jest częścią wszystkich grup targetowania programu WSUS ma swoje członkostwo w grupie importowane do usługi Azure Monitor. Jeśli używasz kierowania po stronie serwera, agent usługi Log Analytics powinny być zainstalowane na serwerze WSUS w celu zaimportowania informacji o członkostwie grupy do usługi Azure Monitor.  To członkostwo jest stale aktualizowane co 4 godziny. 

Usługę Azure Monitor można skonfigurować do importowania grup programu WSUS z **ustawień zaawansowanych** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **pozycję Grupy komputerów**, **WSUS**, a następnie **Zaimportuj członkostwo w grupach WSUS**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerowe z usługi WSUS](media/computer-groups/configure-wsus.png)

Po zaimportowaniu grup menu zawiera listę wykrytych komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych łączy, aby zwrócić rekordy **ComputerGroup** z tymi informacjami.

### <a name="configuration-manager"></a>Configuration Manager
Po skonfigurowaniu usługi Azure Monitor do importowania członkostwa kolekcji programu Configuration Manager tworzy grupę komputerów dla każdej kolekcji.  Informacje o członkostwie kolekcji są pobierane co 3 godziny, aby utrzymać aktualną liczbę grup komputerów. 

Przed zaimportowanie kolekcji programu Configuration Manager należy [połączyć program Menedżer konfiguracji z usługą Azure Monitor](collect-sccm.md).  

![Grupy komputerowe z SCCM](media/computer-groups/configure-sccm.png)

Po zaimportowaniu kolekcji menu zawiera listę wykrytych komputerów z wykrytym członkostwem w grupie oraz liczbę zaimportowanych grup.  Możesz kliknąć jedno z tych łączy, aby zwrócić rekordy **ComputerGroup** z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Można wyświetlić grupy komputerów, które zostały utworzone na podstawie kwerendy dziennika lub interfejsu API wyszukiwania dzienników z **ustawień zaawansowanych** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **pozycję Grupy komputerów,** a następnie **pozycję Zapisane grupy**.  

Kliknij **x** w kolumnie **Usuń,** aby usunąć grupę komputerów.  Kliknij ikonę **Wyświetl członków** grupy, aby uruchomić wyszukiwanie dzienników grupy, które zwraca jej członków.  Nie można zmodyfikować grupy komputerów, ale zamiast tego należy ją usunąć, a następnie ponownie utworzyć za pomocą zmodyfikowanych ustawień.

![Zapisane grupy komputerów](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Używanie grupy komputerów w kwerendzie dziennika
Grupa Komputer utworzona na podstawie kwerendy dziennika w kwerendzie jest używana przez traktowanie jej aliasu jako funkcji, zazwyczaj z następującą składnią:

  `Table | where Computer in (ComputerGroup)`

Na przykład można użyć następujących do zwrócenia updatesummary rekordy tylko dla komputerów w grupie komputerów o nazwie mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Importowane grupy komputerów i ich uwzględnione komputery są przechowywane w tabeli **ComputerGroup.**  Na przykład następująca kwerenda zwraca listę komputerów w grupie Komputery domeny z usługi Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Następująca kwerenda zwraca rekordy UpdateSummary tylko dla komputerów w komputerach domeny.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Rekordy grupy komputerowej
Rekord jest tworzony w obszarze roboczym usługi Log Analytics dla każdego członkostwa w grupie komputerów utworzonego z usługi Active Directory lub programu WSUS.  Te rekordy mają typ **ComputerGroup** i mają właściwości w poniższej tabeli.  Rekordy nie są tworzone dla grup komputerów na podstawie zapytań dziennika.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nazwa komputera członkowskiego. |
| `Group` |Nazwa grupy. |
| `GroupFullName` |Pełna ścieżka do grupy, w tym nazwa źródła i źródła. |
| `GroupSource` |Źródło, z których pobrano tę grupę. <br><br>ActiveDirectory<br>Program WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nazwa źródła, z których grupa została pobrana.  W przypadku usługi Active Directory jest to nazwa domeny. |
| `ManagementGroupName` |Nazwa grupy zarządzania agentów SCOM.  W przypadku innych agentów jest\<to identyfikator obszaru roboczego AOI\> |
| `TimeGenerated` |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.  

