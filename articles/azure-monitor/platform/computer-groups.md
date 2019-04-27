---
title: Grupy komputerów w usłudze Azure Monitor rejestrowania zapytań | Dokumentacja firmy Microsoft
description: Grupy komputerów w usłudze Azure Monitor umożliwia zakresu dziennika zapytań do konkretnego zestawu komputerów.  W tym artykule opisano różne metody używanej do tworzenia grup komputerów i sposobu ich używania w zapytaniu dziennika.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: c2babb5a86d69881b6a76c6dceae80a24a891f6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740998"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Grupy komputerów w usłudze Azure Monitor rejestrowania zapytań
Grupy komputerów w usłudze Azure Monitor pozwala do zakresu [rejestrowania zapytań](../log-query/log-query-overview.md) do konkretnego zestawu komputerów.  Każda grupa jest wypełniana komputerami za pomocą zapytań, który zdefiniujesz lub przez importowanie grup z różnych źródeł.  Gdy grupa jest dołączany do zapytania dziennika, wyniki są ograniczone do rekordy spełniające komputerów w grupie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Można utworzyć grupę komputerów w usłudze Azure Monitor przy użyciu dowolnej z metod w poniższej tabeli.  W poniższych sekcjach znajdują się szczegółowe informacje na temat poszczególnych metod. 

| Metoda | Opis |
|:--- |:--- |
| Zapytanie dziennika |Utwórz zapytanie dziennika, które zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Użyj interfejsu API wyszukiwania w dzienniku programowo utworzyć grupę komputerów, na podstawie wyników kwerendy dziennika. |
| Usługa Active Directory |Automatyczne skanowanie członkostwa w grupie komputerów agenta, które są członkami domeny usługi Active Directory i utworzyć grupę w usłudze Azure Monitor dla każdej grupy zabezpieczeń. (Tylko w przypadku maszyn Windows)|
| Configuration Manager | Importuj kolekcje w programie System Center Configuration Manager i Utwórz grupę w usłudze Azure Monitor dla każdego. |
| Windows Server Update Services |Wyszukaj przeznaczone dla grup serwerów WSUS lub klientów i automatycznie utworzyć grupę w usłudze Azure Monitor dla każdego. |

### <a name="log-query"></a>Zapytanie dziennika
Grup komputerów utworzona w wyniku zapytania dziennika zawiera wszystkich komputerów zwracanych przez zapytanie, które należy zdefiniować.  To zapytanie jest uruchamiany za każdym razem, gdy grupa komputerów jest używana co znajduje odzwierciedlenie zmiany, ponieważ grupa została utworzona.  

Można użyć dowolnego zapytania dla grupy komputerów, ale aplikacja musi zwracać różne zestaw komputerów przy użyciu `distinct Computer`.  Poniżej przedstawiono typowe przykładowe zapytanie, które można użyć jako grupę komputerów.

    Heartbeat | where Computer contains "srv" | distinct Computer

Poniższa procedura umożliwia utworzenie grupy komputerów z przeszukiwania dzienników w witrynie Azure portal.

1. Kliknij przycisk **dzienniki** w **usługi Azure Monitor** menu w witrynie Azure portal.
1. Utwórz i uruchom zapytanie zwracające komputery, które mają w grupie.
1. Kliknij przycisk **Zapisz** w górnej części ekranu.
1. Zmiana **Zapisz jako** do **funkcja** i wybierz **Zapisz to zapytanie jako grupę komputerów**.
1. Podaj wartości dla każdej właściwości grupy komputerów opisane w tabeli i kliknij przycisk **Zapisz**.

W poniższej tabeli opisano właściwości, które definiują grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Name (Nazwa)   | Nazwa zapytania do wyświetlenia w portalu. |
| Alias funkcji | Unikatowego aliasu, używany do identyfikowania grupy komputerów w zapytaniu. |
| Category       | Kategoria organizować zapytania w portalu. |


### <a name="active-directory"></a>Usługa Active Directory
Podczas konfigurowania usługi Azure Monitor, aby zaimportować członkostwa w grupach usługi Active Directory analizuje członkostwa w grupie komputerów przyłączonych do domeny Windows za pomocą agenta usługi Log Analytics.  Grupa komputerów jest tworzony w usłudze Azure Monitor dla każdej grupy zabezpieczeń w usłudze Active Directory, a każdy komputer Windows został dodany do grupy komputerów, odpowiadający grup zabezpieczeń, do których należą.  To członkostwo jest stale aktualizowany co 4 godziny.  

> [!NOTE]
> Zaimportowane grupy usługi Active Directory zawierają tylko Windows maszyny.

Konfigurowanie usługi Azure Monitor do zaimportowania grup zabezpieczeń usługi Active Directory z **Zaawansowane ustawienia** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **grup komputerów**, **usługi Active Directory**, a następnie **członkostwa w grupach usługi Active Directory importu z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usługą Active Directory](media/computer-groups/configure-activedirectory.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Po skonfigurowaniu usługi Azure Monitor, aby zaimportować członkostwa w grupach usług WSUS, analizuje określania wartości docelowej członkostwa w grupie komputerów za pomocą agenta usługi Log Analytics.  Jeśli używasz klienta przeznaczone dla dowolnego komputera, który jest podłączony do usługi Azure Monitor i jest częścią dowolnego programu WSUS przeznaczone dla grup ma jego członkostwo w grupie zaimportowany do usługi Azure Monitor. Jeśli używasz po stronie serwera dla usługi Log Analytics agent powinien być zainstalowany na serwerze programu WSUS w taki sposób, w kolejności, aby uzyskać informacje o członkostwie grupy do zaimportowania do usługi Azure Monitor.  To członkostwo jest stale aktualizowany co 4 godziny. 

Konfigurowanie usługi Azure Monitor do zaimportowania grup usług WSUS z **Zaawansowane ustawienia** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **grup komputerów**, **WSUS**, a następnie **członkostwa w grupach importu programu WSUS**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów programu WSUS](media/computer-groups/configure-wsus.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Podczas konfigurowania usługi Azure Monitor, aby zaimportować członkostwa w kolekcjach programu Configuration Manager tworzy grupy komputerów, dla każdej kolekcji.  Informacje o członkostwo w kolekcji jest pobierana co 3 godziny, aby zapewnić aktualność grup komputerów. 

Przed zaimportowaniem kolekcji programu Configuration Manager, należy najpierw [łączenia programu Configuration Manager do usługi Azure Monitor](collect-sccm.md).  Następnie można skonfigurować importowania danych z **Zaawansowane ustawienia** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **grup komputerów**, **SCCM**, a następnie **członkostwa w kolekcjach programu Configuration Manager importu**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z programu SCCM](media/computer-groups/configure-sccm.png)

Po kolekcji zostały zaimportowane, menu wyświetla liczbę komputerów z członkostwem grupy wykryte i liczbę grup zaimportowanych.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Możesz wyświetlić grupy komputerów, które zostały utworzone na podstawie zapytania dziennika lub interfejsu API wyszukiwania w dzienniku z **Zaawansowane ustawienia** w obszarze roboczym usługi Log Analytics w witrynie Azure portal.  Wybierz **grup komputerów** i następnie **zapisane grupy**.  

Kliknij przycisk **x** w **Usuń** kolumny, aby usunąć grupę komputerów.  Kliknij przycisk **Wyświetl członków** ikony grupy uruchomić przeszukiwanie dziennika grupy, które zwraca jej członków.  Nie można zmodyfikować grupy komputerów, ale zamiast tego należy usunąć i utworzyć ją ponownie przy użyciu zmodyfikowanych ustawień.

![Zapisane grupy komputerów](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Używanie grupy komputerów w zapytaniu dziennika
Możesz użyć grupy komputerów utworzone na podstawie kwerendy dziennika w zapytaniu traktując jego aliasu jako funkcja, zazwyczaj przy użyciu następującej składni:

  `Table | where Computer in (ComputerGroup)`

Na przykład można użyć następujących do zwrócenia rekordy UpdateSummary tylko komputery należące do grupy komputerów o nazwie mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Zaimportowany komputer grupy i ich uwzględnionych komputerach, które są przechowywane w **Grupa_komputerów** tabeli.  Na przykład następujące zapytanie zwróci listę komputerów w grupie komputerów w domenie z usługi Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Następujące zapytanie zwróci rekordy UpdateSummary tylko komputery, na komputerach z domeny.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Rekordów grupy komputerów
Rekord zostanie utworzony w obszarze roboczym usługi Log Analytics dla każdego członkostwo w grupie komputerów, utworzone na podstawie usługi Active Directory lub programu WSUS.  Te rekordy mają typ **Grupa_komputerów** i mają właściwości podane w poniższej tabeli.  Dla grup komputerów opartych na kwerendach dziennika nie zostały utworzone rekordy.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Nazwa komputera członkowskiego. |
| `Group` |Nazwa grupy. |
| `GroupFullName` |Pełna ścieżka do grupy, w tym źródłowego i nazwę źródła. |
| `GroupSource` |Źródłowej, tej grupy, zebranych z. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Nazwa źródła, które zostały zebrane grupy.  Usługi Active Directory jest to nazwa domeny. |
| `ManagementGroupName` |Nazwa grupy zarządzania agentów SCOM.  Dla innych agentów jest to AOI -\<identyfikator obszaru roboczego\> |
| `TimeGenerated` |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  

