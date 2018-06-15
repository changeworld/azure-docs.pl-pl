---
title: Grupy komputerów w Azure Log Analytics dziennika wyszukiwania | Dokumentacja firmy Microsoft
description: Grupy komputerów w analizy dzienników umożliwiają zakres wyszukiwania dziennika do określonego zestawu komputerów.  W tym artykule opisano różne metody używanej do tworzenia grup komputerów i sposobu ich używania w wyszukiwaniu dziennika.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: c4a1edc8e4ff129a8b073f008e1d20bb20941ae1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33775268"
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Zaloguj się wyszukiwanie grup komputerów w analizy dzienników

Grupy komputerów w analizy dzienników umożliwiają zakresu [dziennika wyszukiwania](log-analytics-log-search-new.md) do określonego zestawu komputerów.  Każda grupa jest wypełniana z komputerami za pomocą zapytanie, które należy zdefiniować lub importując grup z różnych źródeł.  Podczas wyszukiwania dziennika zawiera grupy, wyniki są ograniczone do rekordów, które spełniają komputerów w grupie.

## <a name="creating-a-computer-group"></a>Tworzenie grupy komputerów
Można utworzyć grupę komputerów w analizy dzienników przy użyciu dowolnej z metod w poniższej tabeli.  W poniższych sekcjach znajdują się szczegółowe informacje o każdej z metod. 

| Metoda | Opis |
|:--- |:--- |
| Wyszukiwanie w dzienniku |Utwórz wyszukiwania dziennika, które zwraca listę komputerów. |
| Interfejs API wyszukiwania w dzienniku |Programowe tworzenie grupy komputerów, na podstawie wyników wyszukiwania dziennika za pomocą interfejsu API wyszukiwania dziennika. |
| Usługa Active Directory |Automatyczne skanowanie członkostwo w grupie na wszystkich komputerach agenta, które są członkami domeny usługi Active Directory i utworzyć grupę w analizy dzienników dla każdej grupy zabezpieczeń. |
| Configuration Manager | Zaimportować kolekcje w programie System Center Configuration Manager i utworzyć grupę w analizy dzienników dla każdego. |
| Windows Server Update Services |Skanowanie serwerów WSUS lub klientów do użycia z grup i automatycznie utworzyć grupę w analizy dzienników dla każdego. |

### <a name="log-search"></a>Wyszukiwanie w dzienniku
Grupy komputerów utworzone na podstawie wyszukiwania dziennika zawiera wszystkie komputery zwróconych przez zapytanie, które należy zdefiniować.  To zapytanie jest uruchamiane za każdym razem, grupa komputerów jest używany tak, aby wszelkie zmiany, ponieważ grupa została utworzona ta jest uwzględniana.  

Każde zapytanie można używać dla grupy komputerów, ale aplikacja musi zwracać różne zestaw komputerów przy użyciu `distinct Computer`.  Poniżej znajduje się wyszukiwanie typowym przykładem, które można użyć jako grupę komputerów.

    Heartbeat | where Computer contains "srv" | distinct Computer

W poniższej tabeli opisano właściwości, które definiują grupę komputerów.

| Właściwość | Opis |
|:---|:---|
| Nazwa wyświetlana   | Nazwa wyszukiwania, które mają być wyświetlane w portalu. |
| Kategoria       | Kategoria do organizowania wyszukiwania w portalu. |
| Zapytanie          | Zapytanie dla grupy komputerów. |
| Alias funkcji | Unikatowego aliasu używany do identyfikowania grupy komputerów w zapytaniu. |

Poniższa procedura umożliwia utworzenie grupy komputerów z dziennika wyszukiwania w portalu Azure.

2. Otwórz **wyszukiwania dziennika** , a następnie kliknij przycisk **zapisane wyszukiwania** w górnej części ekranu.
3. Kliknij przycisk **Dodaj** i podaj wartości dla każdej właściwości grupy komputerów.
4. Wybierz **Zapisz to zapytanie jako grupę komputerów** i kliknij przycisk **OK**.



### <a name="active-directory"></a>Usługa Active Directory
Podczas konfigurowania analizy dzienników do zaimportowania członkostwa w grupach usługi Active Directory, analizuje członkostwo w grupie komputerów przyłączonych do domeny z agentem pakietu OMS.  Grupa komputerów jest tworzony w analizy dzienników dla każdej grupy zabezpieczeń w usłudze Active Directory, a każdy komputer jest dodawany do odpowiadającego do grupy zabezpieczeń, które są oni członkami grupy komputerów.  To członkostwo jest stale aktualizowany co 4 godziny.  

Konfigurowanie analizy dzienników do zaimportowania grup zabezpieczeń usługi Active Directory z analizy dzienników **Zaawansowane ustawienia** w portalu Azure.  Wybierz **grup komputerów**, **usługi Active Directory**, a następnie **członkostwa w grupach usługi Active Directory importu z komputerów**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z usługi Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Grupy zostały zaimportowane, menu znajdują się liczba komputerów z członkostwem grupy wykryte i liczby grup zaimportowane.  Możesz kliknąć jeden z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Podczas konfigurowania analizy dzienników do zaimportowania członkostwa w grupach usług WSUS, analizuje docelowych członkostwo w grupie komputerów z agentem pakietu OMS.  Jeśli używasz klienta przeznaczonych dla dowolnego komputera, który jest podłączony do analizy dzienników i jest częścią żadnych WSUS przypisywania grup ma jego przynależność zaimportowany do analizy dzienników. Jeśli używasz po stronie serwera celem OMS agenta należy zainstalować na serwerze programu WSUS w taki sposób, aby informacje o członkostwie grupy do zaimportowania do analizy dzienników.  To członkostwo jest stale aktualizowany co 4 godziny. 

Konfigurowanie analizy dzienników do zaimportowania grup usług WSUS z analizy dzienników **Zaawansowane ustawienia** w portalu Azure.  Wybierz **grup komputerów**, **WSUS**, a następnie **importu programu WSUS członkostw**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów programu WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Grupy zostały zaimportowane, menu znajdują się liczba komputerów z członkostwem grupy wykryte i liczby grup zaimportowane.  Możesz kliknąć jeden z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Podczas konfigurowania analizy dzienników do Importuj członkostwa w kolekcji programu Configuration Manager tworzy grupy komputerów dla każdej kolekcji.  Informacje dotyczące członkostwa kolekcji jest pobierana co 3 godziny, aby zapewnić aktualność grupy komputerów. 

Przed zaimportowaniem kolekcji programu Configuration Manager, należy najpierw [łączenia programu Configuration Manager do analizy dzienników](log-analytics-sccm.md).  Następnie można skonfigurować Importuj z analizy dzienników **Zaawansowane ustawienia** w portalu Azure.  Wybierz **grup komputerów**, **SCCM**, a następnie **członkostwa w kolekcji programu Configuration Manager importu**.  Nie są wymagane żadne dalsze czynności konfiguracyjne.

![Grupy komputerów z programu SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Kolekcje zostały zaimportowane, menu znajdują się liczba komputerów z członkostwem grupy wykryte i liczby grup zaimportowane.  Możesz kliknąć jeden z poniższych linków, aby zwrócić **Grupa_komputerów** rekordy z tymi informacjami.

## <a name="managing-computer-groups"></a>Zarządzanie grupami komputerów
Możesz wyświetlić grupy komputerów, które zostały utworzone na podstawie wyszukiwania dziennika lub interfejsu API dziennika wyszukiwania z analizy dzienników **Zaawansowane ustawienia** w portalu Azure.  Wybierz **grup komputerów** , a następnie **zapisane grupy**.  

Kliknij przycisk **x** w **Usuń** kolumna usunąć grupę komputerów.  Kliknij przycisk **Wyświetlanie członków** ikona grupy do uruchamiania grupy dziennik wyszukiwania, które zwraca jej elementów członkowskich.  Nie można zmodyfikować grupy komputerów, ale zamiast tego należy usunąć i ponownie utworzyć przy użyciu zmodyfikowanych ustawień.

![Grupy komputerów zapisane](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>W wyszukiwaniu dziennika przy użyciu grupy komputerów
Możesz użyć grupy komputerów utworzone na podstawie wyszukiwania dziennika w zapytaniu traktując jej aliasem jako funkcja, zwykle przy użyciu następującej składni:

  `Table | where Computer in (ComputerGroup)`

Na przykład można użyć następujących do zwrócenia UpdateSummary rekordów tylko komputery należące do grupy komputerów o nazwie mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Zaimportowany komputer grup i komputerów uwzględnione są przechowywane w **Grupa_komputerów** tabeli.  Na przykład poniższe zapytanie zwróci listę komputerów w grupie komputerów w domenie z usługi Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

Następujące zapytanie zwróci UpdateSummary rekordów tylko komputery w domenie komputerach.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>Rekordów grupy komputerów
Zostaje utworzony rekord w obszarze roboczym analizy dzienników dla każdego członkostwa grup komputerów utworzone na podstawie usługi Active Directory lub usług WSUS.  Te rekordy mieć typu **Grupa_komputerów** i mieć właściwości w poniższej tabeli.  Rekordy nie są tworzone dla grup komputerów opartych na dziennik wyszukiwania.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computer (Komputer) |Nazwa komputera członkowskiego. |
| Grupa |Nazwa grupy. |
| GroupFullName |Pełna ścieżka do grupy, takie jak źródło i nazwa źródła. |
| GroupSource |Źródło tej grupy zostały zebrane z. <br><br>Polecenie cmdlet<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nazwa źródła, które zostały zebrane grupy.  Dla usługi Active Directory jest to nazwa domeny. |
| ManagementGroupName |Nazwa grupy zarządzania agentów SCOM.  W przypadku innych agentów jest AOI -\<identyfikator obszaru roboczego\> |
| TimeGenerated |Data i godzina utworzenia lub aktualizacji grupy komputerów. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.  

