---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie i wyświetlanie raportów dla Azure Backup przy użyciu Log Analytics i skoroszytów platformy Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161205"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

Typowym wymaganiem dla administratorów kopii zapasowych jest uzyskanie wglądu w kopie zapasowe na podstawie danych przez długi czas. W przypadku takich rozwiązań może istnieć wiele przypadków użycia, przydzielanie i prognozowanie zużytego magazynu w chmurze, inspekcja kopii zapasowych i przywracanie oraz identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

Obecnie usługa Azure Backup udostępnia rozwiązanie do raportowania, które korzysta z [dzienników Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) i [skoroszytów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), co ułatwia uzyskiwanie szczegółowych informacji o kopiach zapasowych w całej kopii zapasowej. W tym artykule wyjaśniono, jak konfigurować i wyświetlać raporty dotyczące kopii zapasowych.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Raporty kopii zapasowych są obsługiwane w przypadku maszyn wirtualnych platformy Azure, usług SQL na maszynach wirtualnych platformy Azure, SAP HANA/ASE na maszynach wirtualnych platformy Azure, agentach Azure Backup (MARS), Azure Backup Server (serwera usługi MAB) i programu System Center DPM.
* W przypadku obciążeń programu DPM raporty kopii zapasowych są obsługiwane przez program DPM w wersji 5.1.363.0 lub nowszej oraz agenta w wersji 2.0.9127.0 i nowszych.
* W przypadku obciążeń serwera usługi MAB raporty kopii zapasowych są obsługiwane dla serwera usługi MAB w wersji 13.0.415.0 i nowszych oraz do wersji agenta 2.0.9170.0 i nowszych.
* Raporty kopii zapasowych można wyświetlać w ramach wszystkich elementów kopii zapasowych, magazynów, subskrypcji i regionów, o ile ich dane są wysyłane do obszaru roboczego Log Analytics (LA), do którego użytkownik ma dostęp. 
* Jeśli jesteś użytkownikiem [usługi Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji klientów, możesz użyć tych raportów za pomocą usługi Azure Lighthouse do wyświetlania raportów dla wszystkich dzierżawców.
* Dane dla zadań tworzenia kopii zapasowej dzienników nie są obecnie wyświetlane w raportach.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć korzystanie z raportów, wykonaj trzy poniższe kroki:

1. **Utwórz obszar roboczy Log Analytics (LA) (lub Użyj istniejącego):**

Należy skonfigurować co najmniej jeden obszar roboczy LA do przechowywania danych raportowania kopii zapasowych. Lokalizacja i subskrypcja, w której można utworzyć ten obszar roboczy LA, jest niezależna od lokalizacji i subskrypcji, w której istnieją Twoje magazyny. 

Zapoznaj się z następującym artykułem: [Tworzenie obszaru roboczego log Analytics w Azure Portal w](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) celu skonfigurowania obszaru roboczego La.

Domyślnie dane w obszarze roboczym LA są przechowywane przez 30 dni. Aby wyświetlić dane przez dłuższy czas, należy zmienić okres przechowywania obszaru roboczego LA. Aby zmienić okres przechowywania, zapoznaj się z następującym artykułem: [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Skonfiguruj ustawienia diagnostyczne dla swoich magazynów:**

Azure Resource Manager zasobów, takich jak magazyny Recovery Services, rejestruje informacje o operacjach zaplanowanych i operacjach wyzwalanych przez użytkownika jako danych diagnostycznych. 

W sekcji monitorowanie magazynu Recovery Services wybierz pozycję **Ustawienia diagnostyczne** i określ cel dla danych diagnostycznych magazynu Recovery Services. [Dowiedz się więcej o korzystaniu z zdarzeń diagnostycznych](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Blok ustawień diagnostycznych](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup również zawiera wbudowaną Azure Policy, która automatyzuje konfigurację ustawień diagnostycznych dla wszystkich magazynów w danym zakresie. Zapoznaj się z następującym artykułem, aby dowiedzieć się, jak korzystać z tych zasad: [Konfigurowanie ustawień diagnostyki magazynu w odpowiedniej skali](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Po skonfigurowaniu diagnostyki zakończenie wypychania danych może potrwać do 24 godzin. Gdy dane zaczynają przepływać do obszaru roboczego LA, możesz nie być w stanie natychmiast zobaczyć danych w raportach, ponieważ dane dla bieżącego częściowego dnia nie są wyświetlane w raportach (więcej informacji znajdziesz [tutaj](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). W związku z tym zalecane jest rozpoczęcie wyświetlania raportów 2 dni po skonfigurowaniu magazynów w celu wysyłania danych do Log Analytics.

3. **Wyświetlanie raportów na Azure Portal:**

Po skonfigurowaniu magazynów w celu wysyłania danych do LA, Wyświetl raporty kopii zapasowej, przechodząc do bloku dowolnego magazynu i klikając element menu **raporty kopii zapasowych** . 

![Pulpit nawigacyjny magazynu](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kliknięcie tego linku spowoduje otwarcie skoroszytu raportów kopii zapasowych.

> [!NOTE]
> * Obecnie początkowe obciążenie raportu może potrwać do 1 minuty.
> * Magazyn Recovery Services jest tylko punktem wejścia dla raportów kopii zapasowych. Gdy skoroszyt raporty dotyczące tworzenia kopii zapasowych zostanie otwarty z bloku magazynu, będzie można zobaczyć dane zagregowane we wszystkich magazynach (wybierając odpowiedni zestaw LA Workspaces).

Poniżej znajduje się opis różnych kart, które zawiera raport:

* **Podsumowanie** — karta Podsumowanie zawiera ogólne omówienie funkcji tworzenia kopii zapasowych. Na karcie Podsumowanie można szybko uzyskać dostęp do łącznej liczby elementów kopii zapasowej, całkowitego zużytego magazynu w chmurze, liczby chronionych wystąpień i współczynnika powodzeń zadań dla każdego typu obciążenia. Aby uzyskać bardziej szczegółowe informacje na temat określonego typu artefaktu kopii zapasowej, przejdź do odpowiednich kart.

![Karta Podsumowanie](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementy kopii zapasowej** — karta elementy kopii zapasowej umożliwia wyświetlenie informacji i trendów w magazynie w chmurze zużywanych na poziomie elementu kopii zapasowej. Na przykład jeśli używasz programu SQL w kopii zapasowej maszyny wirtualnej platformy Azure, możesz zobaczyć magazyn w chmurze zużyty dla każdej bazy danych SQL, której kopia zapasowa ma zostać utworzona. Możesz również wyświetlić dane dotyczące elementów kopii zapasowej określonego stanu ochrony. Na przykład kliknięcie kafelka **zatrzymana ochrona** w górnej części karty filtruje wszystkie poniższe widżety, aby wyświetlić dane tylko dla elementów kopii zapasowej w stanie zatrzymania ochrony.

![Karta elementy kopii zapasowej](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Użycie** — karta Użycie umożliwia wyświetlenie kluczowych parametrów rozliczania dla kopii zapasowych. Informacje wyświetlane na tej karcie znajdują się na poziomie jednostki rozliczeń (chronionego kontenera). Na przykład w przypadku tworzenia kopii zapasowej serwera programu DPM na platformie Azure można wyświetlić trend wystąpień chronionych i magazynu w chmurze zużytych dla serwera programu DPM. Podobnie, jeśli używasz SQL w Azure Backup lub SAP HANA w Azure Backup, na tej karcie są dostępne informacje dotyczące użycia na poziomie maszyny wirtualnej, w której znajdują się te bazy danych.

![Karta Użycie](./media/backup-azure-configure-backup-reports/usage.png)

* **Zadania** — karta zadania umożliwia wyświetlanie długotrwałych trendów dotyczących zadań, takich jak liczba zadań zakończonych niepowodzeniem dziennie i Najczęstsze przyczyny niepowodzenia zadania. Te informacje można wyświetlić zarówno na poziomie zagregowanym, jak i na poziomie elementu kopii zapasowej. Kliknięcie określonego elementu kopii zapasowej w siatce umożliwia wyświetlenie szczegółowych informacji o każdym zadaniu, które zostało wyzwolone dla tego elementu kopii zapasowej w wybranym zakresie czasu.

![Karta Zadania](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zasady** — karta zasady umożliwia wyświetlanie informacji na temat wszystkich aktywnych zasad, takich jak liczba skojarzonych elementów i łączny magazyn w chmurze używany przez elementy, których kopia zapasowa jest wykonywana w ramach danej zasady. Kliknięcie określonych zasad umożliwia wyświetlenie informacji na temat wszystkich skojarzonych z nimi elementów kopii zapasowej.

![Karta zasady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Eksportowanie do programu Excel

Kliknięcie przycisku STRZAŁKA w dół w prawym górnym rogu dowolnego widżetu (tabela/wykres) eksportuje zawartość tego widżetu jako arkusz programu Excel, czyli z zastosowaniem istniejących filtrów. Aby wyeksportować więcej wierszy tabeli do programu Excel, można zwiększyć liczbę wierszy wyświetlanych na stronie przy użyciu listy rozwijanej **wiersze na stronę** w górnej części każdej siatki.

## <a name="pinning-to-dashboard"></a>Przypinanie do pulpitu nawigacyjnego

Kliknij ikonę pinezki w górnej części każdego widżetu, aby przypiąć widżet do pulpitu nawigacyjnego Azure Portal. Ułatwia to tworzenie dostosowanych pulpitów nawigacyjnych, które są dostosowane do wyświetlania najważniejszych informacji, które są potrzebne.

## <a name="cross-tenant-reports"></a>Raporty obejmujące wiele dzierżawców

Jeśli jesteś użytkownikiem [usługi Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawców, możesz użyć domyślnego filtru subskrypcji (klikając ikonę filtru w prawym górnym rogu Azure Portal), aby wybrać wszystkie subskrypcje, dla których chcesz zobaczyć dane. Dzięki temu można wybrać pozycję LA Workspaces dla dzierżawców, aby wyświetlić raporty z wieloma dzierżawcami.

## <a name="conventions-used-in-backup-reports"></a>Konwencje używane w raportach kopii zapasowych

* Filtry działają od lewej do prawej i od góry do dołu na każdej karcie, co oznacza, że dowolny filtr ma zastosowanie tylko do wszystkich tych elementów widget, które są rozmieszczone z prawej strony filtru lub poniżej tego filtru. 
* Kliknięcie kolorowego kafelka filtruje widżety poniżej kafelka dla rekordów odnoszących się do wartości tego kafelka. Na przykład kliknięcie kafelka *zatrzymana ochrona* na karcie elementy kopii zapasowej filtruje siatki i wykresy poniżej, aby wyświetlić dane dla elementów kopii zapasowej w stanie "Ochrona zatrzymana".
* Kafelki, które nie są kolorowe, nie są klikane.
* Dane bieżącego częściowego dnia nie są wyświetlane w raportach. W tym przypadku, gdy wybrana wartość **przedziału czasu** to, w ***ciągu ostatnich 7 dni***, raport przedstawia rekordy z ostatnich 7 dni ukończenia (które nie obejmują bieżącego dnia).
* Raport przedstawia szczegóły zadań (poza zadaniami dziennika), które zostały **wyzwolone** w wybranym przedziale czasu. 
* Wartości wyświetlane dla magazynu w chmurze i chronionych wystąpień znajdują się na **końcu** wybranego zakresu czasu.
* Elementy kopii zapasowej wyświetlane w raportach to elementy, które istnieją na **końcu** wybranego zakresu czasu. Elementy kopii zapasowej, które zostały usunięte w środku wybranego zakresu czasu, nie są wyświetlane. Ta sama Konwencja dotyczy również zasad tworzenia kopii zapasowych.

## <a name="query-load-times"></a>Czasy ładowania zapytania

Widżety w raporcie kopii zapasowych są obsługiwane przez zapytania Kusto, które są uruchamiane w obszarze roboczym LA użytkownika. Ponieważ te zapytania zazwyczaj obejmują przetwarzanie dużych ilości danych, a wiele sprzężeń umożliwia bogatszy wgląd w szczegółowe dane, Widżety mogą nie ładować się natychmiast, gdy użytkownik przegląda raporty w ramach dużej ilości kopii zapasowych. Poniższa tabela zawiera przybliżone oszacowanie czasu, jaki może upłynąć do obciążenia przez różne widżety, na podstawie liczby elementów kopii zapasowej i zakresu czasu, w którym raport jest wyświetlany:

| **# DataSources**                         | **Horyzont czasu** | **Czasy ładowania (w przybliżeniu)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 miesiąc          | Kafelki: 5-10 s <br> Siatki: 5-10 s <br> Wykresy: 5-10 s <br> Filtry na poziomie raportu: 5-10 s|
| ~ 5 K                       | 3 miesiące          | Kafelki: 5-10 s <br> Siatki: 5-10 s <br> Wykresy: 5-10 s <br> Filtry na poziomie raportu: 5-10 s|
| ~ 10 K                       | 3 miesiące          | Kafelki: 15-20 s <br> Siatki: 15-20 s <br> Wykresy: 1-2 minut <br> Filtry na poziomie raportu: 25-30 s|
| ~ 15 K                       | 1 miesiąc          | Kafelki: 15-20 s <br> Siatki: 15-20 s <br> Wykresy: 50-60 s <br> Filtry na poziomie raportu: 20-25 s|
| ~ 15 K                       | 3 miesiące          | Kafelki: 20-30 s <br> Siatki: 20-30 s <br> Wykresy: 2-3 minut <br> Filtry na poziomie raportu: 50-60 s |

## <a name="what-happened-to-the-power-bi-reports"></a>Co się stało z raportami Power BI?
* Nasza wcześniejsza aplikacja Power BI Template for Reporting (dane źródłowe z konta usługi Azure Storage) znajduje się na ścieżce przestarzałej. Zalecane jest, aby rozpocząć wysyłanie danych diagnostycznych magazynu do Log Analytics zgodnie z powyższym opisem, aby wyświetlić raporty.

* Ponadto schemat wersji 1 służący do wysyłania danych diagnostycznych na konto magazynu lub w obszarze roboczym LA znajduje się również na ścieżce przestarzałej. Oznacza to, że w przypadku zapisaniu niestandardowych zapytań lub automatyzacji opartych na schemacie V1 zaleca się zaktualizowanie tych zapytań, aby używały obecnie obsługiwanego schematu v2.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o monitorowaniu i raportowaniu za pomocą Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)