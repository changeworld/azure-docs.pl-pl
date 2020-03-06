---
title: Grupy komputerów w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Grupy komputerów w Azure Monitor umożliwiają określanie zakresu zapytań dziennika do określonego zestawu komputerów.  W tym artykule opisano różne metody, których można użyć do tworzenia grup komputerów i ich użycia w zapytaniu dziennika.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: a005b6cec811b8a584123dc4c8abab77766961e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373313"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupy komputerów w zapytaniach dziennika Azure Monitor
Grupy komputerów w Azure Monitor umożliwiają określanie zakresu [zapytań dziennika](../log-query/log-query-overview.md) do określonego zestawu komputerów.  Każda grupa jest wypełniana komputerami za pomocą zapytań, który zdefiniujesz lub przez importowanie grup z różnych źródeł.  Gdy grupa zostanie uwzględniona w zapytaniu dziennika, wyniki są ograniczone do rekordów, które pasują do komputerów w grupie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Grupę komputerów można utworzyć w Azure Monitor przy użyciu dowolnej metody z poniższej tabeli.  W poniższych sekcjach znajdują się szczegółowe informacje na temat poszczególnych metod. 

| Metoda | Opis |
|:--- |:--- |
| Zapytanie dziennika |Utwórz zapytanie dziennika, które zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Użyj interfejsu API przeszukiwania dzienników, aby programowo utworzyć grupę komputerów na podstawie wyników zapytania dziennika. |
| Usługa Active Directory |Automatycznie Skanuj członkostwo w grupie wszystkich komputerów agentów, które są członkami domeny Active Directory i Utwórz grupę w Azure Monitor dla każdej grupy zabezpieczeń. (Tylko komputery z systemem Windows)|
| Configuration Manager | Zaimportuj kolekcje z programu Microsoft Endpoint Configuration Manager i Utwórz grupę w Azure Monitor dla każdej z nich. |
| Windows Server Update Services |Automatycznie Skanuj serwery lub klientów programu WSUS pod kątem grup docelowych i Utwórz grupę w Azure Monitor dla każdej z nich. |

### <a name="log-query"></a>Zapytanie dziennika
Grupy komputerów utworzone na podstawie zapytania dziennika zawierają wszystkie komputery zwrócone przez zdefiniowane zapytanie.  To zapytanie jest uruchamiany za każdym razem, gdy grupa komputerów jest używana co znajduje odzwierciedlenie zmiany, ponieważ grupa została utworzona.  

Można użyć dowolnego zapytania dla grupy komputerów, ale musi on zwrócić unikatowy zestaw komputerów przy użyciu `distinct Computer`.  Poniżej przedstawiono typowe przykładowe zapytanie, którego można użyć jako grupy komputerów.

    Heartbeat | where Computer contains "srv" | distinct Computer

Poniższa procedura umożliwia utworzenie grupy komputerów z przeszukiwania dzienników w witrynie Azure portal.

1. Kliknij pozycję **dzienniki** w menu **Azure monitor** w Azure Portal.
1. Utwórz i uruchom zapytanie zwracające komputery, które chcesz umieścić w grupie.
1. Kliknij przycisk **Zapisz** w górnej części ekranu.
1. Zmień opcję **Zapisz jako** na **, a następnie** wybierz pozycję **Zapisz to zapytanie jako grupę komputerów**.
1. Podaj wartości dla każdej właściwości grupy komputerów opisanej w tabeli, a następnie kliknij przycisk **Zapisz**.

W poniższej tabeli opisano właściwości, które definiują grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Name (Nazwa)   | Nazwa zapytania do wyświetlania w portalu. |
| Alias funkcji | Unikatowego aliasu, używany do identyfikowania grupy komputerów w zapytaniu. |
| Kategoria       | Kategoria do organizowania zapytań w portalu. |


### <a name="active-directory"></a>Usługa Active Directory
W przypadku skonfigurowania Azure Monitor w celu zaimportowania członkostw w grupie Active Directory, analizuje ona członkostwo w grupie wszystkich komputerów przyłączonych do domeny systemu Windows z agentem Log Analytics.  Grupa komputerów jest tworzona w Azure Monitor dla każdej grupy zabezpieczeń w Active Directory, a każdy komputer z systemem Windows jest dodawany do grup komputerów odpowiadających grupom zabezpieczeń, do których są członkami.  To członkostwo jest stale aktualizowany co 4 godziny.  

> [!NOTE]
> Zaimportowane grupy Active Directory zawierają tylko maszyny z systemem Windows.

Konfigurowanie Azure Monitor do importowania Active Directory grup zabezpieczeń z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz pozycję **grupy komputerów**, **Active Directory**, a następnie **zaimportuj członkostwa w grupach Active Directory z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usługą Active Directory](media/computer-groups/configure-activedirectory.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

### <a name="windows-server-update-service"></a>Usługa Windows Update Server
W przypadku skonfigurowania Azure Monitor w celu zaimportowania członkostwa w grupach usług WSUS program analizuje członkostwo grupy docelowej na wszystkich komputerach z agentem Log Analytics.  Jeśli używasz funkcji określania wartości docelowej po stronie klienta, każdy komputer, który jest połączony z Azure Monitor i jest częścią dowolnych grup docelowych usług WSUS, ma członkostwo w grupie zaimportowane do Azure Monitor. Jeśli używasz określania wartości docelowej po stronie serwera, Agent Log Analytics powinien być zainstalowany na serwerze programu WSUS, aby informacje o członkostwie w grupie zostały zaimportowane do Azure Monitor.  To członkostwo jest stale aktualizowany co 4 godziny. 

Skonfiguruj Azure Monitor do importowania grup usług WSUS z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz pozycje **grupy komputerów**, **WSUS**, a następnie **Importuj członkostwa w grupach usług WSUS**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów programu WSUS](media/computer-groups/configure-wsus.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

### <a name="configuration-manager"></a>Configuration Manager
Podczas konfigurowania Azure Monitor do importowania członkostw w kolekcji Configuration Manager tworzy grupę komputerów dla każdej kolekcji.  Informacje o członkostwo w kolekcji jest pobierana co 3 godziny, aby zapewnić aktualność grup komputerów. 

Przed zaimportowaniem kolekcji Configuration Manager należy [połączyć Configuration Manager z Azure monitor](collect-sccm.md).  

![Grupy komputerów z programu SCCM](media/computer-groups/configure-sccm.png)

Po kolekcji zostały zaimportowane, menu wyświetla liczbę komputerów z członkostwem grupy wykryte i liczbę grup zaimportowanych.  Możesz kliknąć jedno z tych linków, aby zwrócić rekordy Grupa **komputerów** z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Można wyświetlić grupy komputerów, które zostały utworzone na podstawie zapytania dziennika lub interfejsu API wyszukiwania dzienników z **ustawień zaawansowanych** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz **grupy komputerów** , a następnie **zapisane grupy**.  

Kliknij **znak x** w kolumnie **Usuń** , aby usunąć grupę komputerów.  Kliknij ikonę **Wyświetl członków** w grupie, aby uruchomić przeszukiwanie dzienników grupy zwracające jego elementy członkowskie.  Nie można zmodyfikować grupy komputerów, ale zamiast tego należy usunąć i utworzyć ją ponownie przy użyciu zmodyfikowanych ustawień.

![Zapisane grupy komputerów](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Używanie grupy komputerów w zapytaniu dziennika
Należy użyć grupy komputerów utworzonej na podstawie zapytania dziennika w zapytaniu, traktując swój alias jako funkcję, zazwyczaj z następującą składnią:

  `Table | where Computer in (ComputerGroup)`

Na przykład można użyć następujących do zwrócenia rekordy UpdateSummary tylko komputery należące do grupy komputerów o nazwie mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Zaimportowane grupy komputerów i ich dołączone komputery są przechowywane w tabeli **komputerów** .  Na przykład następujące zapytanie zwróci listę komputerów w grupie komputerów w domenie z usługi Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Następujące zapytanie zwróci rekordy UpdateSummary tylko komputery, na komputerach z domeny.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Rekordów grupy komputerów
Rekord zostanie utworzony w obszarze roboczym usługi Log Analytics dla każdego członkostwo w grupie komputerów, utworzone na podstawie usługi Active Directory lub programu WSUS.  Te rekordy mają typ Grupa **komputerów** i mają właściwości w poniższej tabeli.  Rekordy nie są tworzone dla grup komputerów opartych na zapytaniach dziennika.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*Grupa komputerów* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nazwa komputera członkowskiego. |
| `Group` |Nazwa grupy. |
| `GroupFullName` |Pełna ścieżka do grupy, w tym źródłowego i nazwę źródła. |
| `GroupSource` |Źródłowej, tej grupy, zebranych z. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nazwa źródła, które zostały zebrane grupy.  Usługi Active Directory jest to nazwa domeny. |
| `ManagementGroupName` |Nazwa grupy zarządzania agentów SCOM.  W przypadku innych agentów jest to AOI\<identyfikator obszaru roboczego\> |
| `TimeGenerated` |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  

