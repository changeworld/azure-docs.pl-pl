---
title: Przeszukiwanie dzienników grup komputerów w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Grupy komputerów w usłudze Log Analytics umożliwia zakres wyszukiwań w dziennikach do konkretnego zestawu komputerów.  W tym artykule opisano różne metody używanej do tworzenia grup komputerów i jak ich używać podczas wyszukiwania dziennika.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 81eacf464c90d56c4ebeae1d1cefbd6f2f0fdab8
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408544"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Przeszukiwanie dzienników grup komputerów w usłudze Log Analytics

Grupy komputerów w usłudze Log Analytics umożliwia zakresu [dziennikach](log-analytics-log-search-new.md) do konkretnego zestawu komputerów.  Każda grupa jest wypełniana komputerami za pomocą zapytań, który zdefiniujesz lub przez importowanie grup z różnych źródeł.  Gdy grupy jest włączona do przeszukiwania dzienników, wyniki są ograniczone do rekordy spełniające komputerów w grupie.

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Można utworzyć grupę komputerów w usłudze Log Analytics przy użyciu dowolnej z metod w poniższej tabeli.  W poniższych sekcjach znajdują się szczegółowe informacje na temat poszczególnych metod. 

| Metoda | Opis |
|:--- |:--- |
| Wyszukiwanie w dzienniku |Utwórz wyszukiwanie w dzienniku, które zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Użyj interfejsu API wyszukiwania w dzienniku programowo utworzyć grupę komputerów, na podstawie wyników przeszukiwania dzienników. |
| Usługa Active Directory |Automatyczne skanowanie członkostwa w grupie komputerów agenta, które są członkami domeny usługi Active Directory i utworzyć grupę w usłudze Log Analytics dla każdej grupy zabezpieczeń. |
| Configuration Manager | Importuj kolekcje w programie System Center Configuration Manager i Utwórz grupę w usłudze Log Analytics dla każdego. |
| Windows Server Update Services |Wyszukaj przeznaczone dla grup serwerów WSUS lub klientów i automatycznie utworzyć grupę w usłudze Log Analytics dla każdego. |

### <a name="log-search"></a>Wyszukiwanie w dzienniku
Grupy komputerów, utworzone na podstawie wyszukiwanie w dzienniku zawiera wszystkich komputerów zwracanych przez zapytanie, które należy zdefiniować.  To zapytanie jest uruchamiany za każdym razem, gdy grupa komputerów jest używana co znajduje odzwierciedlenie zmiany, ponieważ grupa została utworzona.  

Można użyć dowolnego zapytania dla grupy komputerów, ale aplikacja musi zwracać różne zestaw komputerów przy użyciu `distinct Computer`.  Poniżej przedstawiono wyszukiwanie typowym przykładem, które można użyć jako grupę komputerów.

    Heartbeat | where Computer contains "srv" | distinct Computer

W poniższej tabeli opisano właściwości, które definiują grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Nazwa wyświetlana   | Nazwa wyszukiwania, aby wyświetlić w portalu. |
| Kategoria       | Kategoria do organizowania operacji wyszukiwania w portalu. |
| Zapytanie          | Zapytanie dla grupy komputerów. |
| Alias funkcji | Unikatowego aliasu, używany do identyfikowania grupy komputerów w zapytaniu. |

Poniższa procedura umożliwia utworzenie grupy komputerów z przeszukiwania dzienników w witrynie Azure portal.

2. Otwórz **wyszukiwanie w dzienniku** a następnie kliknij przycisk **zapisane wyszukiwania** w górnej części ekranu.
3. Kliknij przycisk **Dodaj** i podaj wartości dla każdej właściwości grupy komputerów.
4. Wybierz **Zapisz to zapytanie jako grupę komputerów** i kliknij przycisk **OK**.



### <a name="active-directory"></a>Usługa Active Directory
Podczas konfigurowania usługi Log Analytics, aby zaimportować członkostwa w grupach usługi Active Directory analizuje członkostwa w grupie komputerów przyłączonych do domeny za pomocą agenta usługi Log Analytics.  Grupa komputerów jest tworzony w usłudze Log Analytics dla każdej grupy zabezpieczeń w usłudze Active Directory, a każdy komputer został dodany do grupy komputerów, odpowiadający grup zabezpieczeń, do których należą.  To członkostwo jest stale aktualizowany co 4 godziny.  

Konfigurowanie usługi Log Analytics można zaimportować grupy zabezpieczeń usługi Active Directory z usługą Log Analytics **Zaawansowane ustawienia** w witrynie Azure portal.  Wybierz **grup komputerów**, **usługi Active Directory**, a następnie **członkostwa w grupach usługi Active Directory importu z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usługą Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Podczas konfigurowania usługi Log Analytics, aby zaimportować członkostwa w grupach usług WSUS, analizuje określania wartości docelowej członkostwa w grupie komputerów za pomocą agenta usługi Log Analytics.  Jeśli używasz klienta przeznaczone dla dowolnego komputera, który jest podłączony do usługi Log Analytics i jest częścią dowolnego programu WSUS przeznaczone dla grup ma jego członkostwo w grupie zaimportowany do usługi Log Analytics. Jeśli używasz po stronie serwera dla usługi Log Analytics agent powinien być zainstalowany na serwerze programu WSUS w taki sposób, w kolejności, aby uzyskać informacje o członkostwie grupy do zaimportowania do usługi Log Analytics.  To członkostwo jest stale aktualizowany co 4 godziny. 

Konfigurowanie usługi Log Analytics do zaimportowania grup usług WSUS z usługą Log Analytics **Zaawansowane ustawienia** w witrynie Azure portal.  Wybierz **grup komputerów**, **WSUS**, a następnie **członkostwa w grupach importu programu WSUS**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów programu WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Grupy zostały zaimportowane, menu znajdują się liczby komputerów za pomocą członkostwa w grupie wykryte i liczba grup zaimportowane.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Podczas konfigurowania usługi Log Analytics, aby zaimportować członkostwa w kolekcjach programu Configuration Manager tworzy grupy komputerów, dla każdej kolekcji.  Informacje o członkostwo w kolekcji jest pobierana co 3 godziny, aby zapewnić aktualność grup komputerów. 

Przed zaimportowaniem kolekcji programu Configuration Manager, należy najpierw [łączenia programu Configuration Manager do usługi Log Analytics](log-analytics-sccm.md).  Następnie można skonfigurować importowania z usługi Log Analytics **Zaawansowane ustawienia** w witrynie Azure portal.  Wybierz **grup komputerów**, **SCCM**, a następnie **członkostwa w kolekcjach programu Configuration Manager importu**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z programu SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Po kolekcji zostały zaimportowane, menu wyświetla liczbę komputerów z członkostwem grupy wykryte i liczbę grup zaimportowanych.  Możesz kliknąć jedno z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Grupy komputerów, które zostały utworzone przy użyciu przeszukiwania dzienników lub interfejsu API wyszukiwania w dzienniku z usługą Log Analytics można wyświetlić **Zaawansowane ustawienia** w witrynie Azure portal.  Wybierz **grup komputerów** i następnie **zapisane grupy**.  

Kliknij przycisk **x** w **Usuń** kolumny, aby usunąć grupę komputerów.  Kliknij przycisk **Wyświetl członków** ikony grupy uruchomić przeszukiwanie dziennika grupy, które zwraca jej członków.  Nie można zmodyfikować grupy komputerów, ale zamiast tego należy usunąć i utworzyć ją ponownie przy użyciu zmodyfikowanych ustawień.

![Zapisane grupy komputerów](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Używanie grupy komputerów podczas wyszukiwania dziennika
Możesz użyć grupy komputerów utworzone na podstawie przeszukiwania dzienników w zapytaniu traktując jego aliasu jako funkcja, zazwyczaj przy użyciu następującej składni:

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
Rekord zostanie utworzony w obszarze roboczym usługi Log Analytics dla każdego członkostwo w grupie komputerów, utworzone na podstawie usługi Active Directory lub programu WSUS.  Te rekordy mają typ **Grupa_komputerów** i mają właściwości podane w poniższej tabeli.  Rekordy nie zostaną utworzone dla grup komputerów, w oparciu o wyszukiwań w dziennikach.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer (Komputer) |Nazwa komputera członkowskiego. |
| Grupa |Nazwa grupy. |
| GroupFullName |Pełna ścieżka do grupy, w tym źródłowego i nazwę źródła. |
| GroupSource |Źródłowej, tej grupy, zebranych z. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nazwa źródła, które zostały zebrane grupy.  Usługi Active Directory jest to nazwa domeny. |
| ManagementGroupName |Nazwa grupy zarządzania agentów SCOM.  Dla innych agentów jest to AOI -\<identyfikator obszaru roboczego\> |
| TimeGenerated |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennikach](log-analytics-log-searches.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  

