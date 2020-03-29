---
title: Konfigurowanie raportów usługi Azure Backup
description: Konfigurowanie i wyświetlanie raportów dla usługi Azure Backup przy użyciu usługi Log Analytics i skoroszytów platformy Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161205"
---
# <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

Typowym wymaganiem dla administratorów kopii zapasowych jest uzyskanie szczegółowych informacji na temat kopii zapasowych na podstawie danych obejmujących długi okres czasu. Może istnieć wiele przypadków użycia dla takiego rozwiązania - przydzielanie i prognozowanie używanego magazynu w chmurze, inspekcja kopii zapasowych i przywracania oraz identyfikowanie kluczowych trendów na różnych poziomach szczegółowości.

Obecnie usługa Azure Backup udostępnia rozwiązanie do raportowania, które wykorzystuje [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) i [skoroszyty platformy Azure,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)pomagając uzyskać szczegółowe informacje na temat kopii zapasowych w całej nieruchomości kopii zapasowych. W tym artykule wyjaśniono, jak skonfigurować i wyświetlić raporty kopii zapasowej.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Raporty kopii zapasowych są obsługiwane dla maszyn wirtualnych platformy Azure, SQL w maszynach wirtualnych platformy Azure, SAP HANA/ASE na maszynach wirtualnych platformy Azure, agenta azure backup agent (MARS), serwera kopii zapasowych Azure (MABS) i programu DPM centrum systemu.
* W przypadku obciążeń programu DPM raporty kopii zapasowych są obsługiwane dla programu DPM w wersji 5.1.363.0 i wyższej oraz agenta w wersji 2.0.9127.0 i wyższej.
* W przypadku obciążeń mabs raporty kopii zapasowych są obsługiwane dla systemu MABS w wersji 13.0.415.0 i wyższej oraz agenta w wersji 2.0.9170.0 i wyższej.
* Raporty kopii zapasowej można wyświetlać we wszystkich elementach kopii zapasowej, magazynach, subskrypcjach i regionach, o ile ich dane są wysyłane do obszaru roboczego usługi Log Analytics (LA), do których użytkownik ma dostęp. 
* Jeśli jesteś użytkownikiem [latarni morskiej platformy Azure](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji klientów, możesz użyć tych raportów za pomocą latarni morskiej platformy Azure, aby wyświetlić raporty we wszystkich dzierżawach.
* Dane dla zadań tworzenia kopii zapasowych dziennika nie są obecnie wyświetlane w raportach.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć korzystanie z raportów, wykonaj trzy kroki opisane poniżej:

1. **Utwórz obszar roboczy usługi Log Analytics (LA) (lub użyj istniejącego):**

Musisz skonfigurować jeden lub więcej obszarów roboczych LA do przechowywania danych raportowania kopii zapasowych. Lokalizacja i subskrypcja, w której można utworzyć ten obszar roboczy LA, jest niezależna od lokalizacji i subskrypcji, w której istnieją magazyny. 

Zapoznaj się z [następującym artykułem: Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal,](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) aby skonfigurować obszar roboczy LA.

Domyślnie dane w la workspace są przechowywane przez 30 dni. Aby wyświetlić dane przez dłuższy horyzont czasowy, zmień okres przechowywania obszaru roboczego LA Workspace. Aby zmienić okres przechowywania, zapoznaj się z następującym [artykułem: Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Skonfiguruj ustawienia diagnostyczne dla magazynów:**

Zasoby usługi Azure Resource Manager, takie jak magazyny usług odzyskiwania, rejestrują informacje o zaplanowanych operacjach i operacjach wyzwalanych przez użytkownika jako dane diagnostyczne. 

W sekcji monitorowania magazynu usług odzyskiwania wybierz **pozycję Ustawienia diagnostyczne** i określ miejsce docelowe dla danych diagnostycznych magazynu usług recovery services. [Dowiedz się więcej o używaniu zdarzeń diagnostycznych](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Blok ustawień diagnostyki](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Usługa Azure Backup udostępnia również wbudowaną usługę Azure Policy, która automatyzuje konfigurację ustawień diagnostycznych dla wszystkich magazynów w danym zakresie. Zapoznaj się z następującym artykułem, aby dowiedzieć się, jak korzystać z tych zasad: [Konfigurowanie ustawień diagnostyki przechowalni na dużą skalę](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Po skonfigurowaniu diagnostyki może upłynąć do 24 godzin, aby początkowe wypychanie danych było zakończone. Gdy dane zaczną płynąć do obszaru roboczego LA, możesz nie być w stanie natychmiast zobaczyć danych w raportach, ponieważ dane dla bieżącego dnia częściowego nie są wyświetlane w raportach (więcej szczegółów [tutaj](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). W związku z tym zaleca się, aby rozpocząć wyświetlanie raportów 2 dni po skonfigurowaniu magazynów do wysyłania danych do usługi Log Analytics.

3. **Wyświetlanie raportów w witrynie Azure portal:**

Po skonfigurowaniu magazynów do wysyłania danych do la, wyświetlanie raportów kopii zapasowej, przechodząc do bloku dowolnego magazynu i klikając element menu **Raporty kopii zapasowych.** 

![Pulpit nawigacyjny przechowalni](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Kliknięcie tego łącza powoduje otwarcie skoroszytu raportu kopii zapasowej.

> [!NOTE]
> * Obecnie początkowe obciążenie raportu może potrwać do 1 minuty.
> * Magazyn usług odzyskiwania jest tylko punktem wejścia dla raportów kopii zapasowych. Po otwarciu skoroszytu raportów kopii zapasowej z bloku magazynu będą można zobaczyć dane zagregowane we wszystkich magazynach (wybierając odpowiedni zestaw obszarów roboczych LA Workspace).

Poniżej znajduje się opis różnych kart, które zawiera raport:

* **Podsumowanie** — karta Podsumowanie zawiera ogólny przegląd nieruchomości kopii zapasowej. Na karcie Podsumowanie można szybko zapoznać się z całkowitą liczbą elementów kopii zapasowej, całkowitą zużywaną przestrzenią dyskową w chmurze, liczbą chronionych wystąpień i wskaźnikiem powodzenia zadania dla typu obciążenia. Aby uzyskać bardziej szczegółowe informacje dotyczące określonego typu artefaktu kopii zapasowej, przejdź do odpowiednich kart.

![Karta Podsumowanie](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementy kopii zapasowej** — karta Elementy kopii zapasowej umożliwia wyświetlanie informacji i trendów dotyczących magazynu w chmurze używanego na poziomie elementu kopii zapasowej. Na przykład jeśli używasz SQL w kopii zapasowej maszyny Wirtualnej platformy Azure, można zobaczyć magazynu w chmurze używane dla każdej bazy danych SQL, której kopię zapasową. Można również wybrać wyświetlanie danych dla elementów kopii zapasowej o określonym stanie ochrony. Na przykład klikając kafelek **Ochrona zatrzymana** u góry karty, filtruje wszystkie poniższe widżety, aby wyświetlić dane tylko dla elementów kopii zapasowej w stanie Wstrzymane zabezpieczenia.

![Karta Elementy kopii zapasowej](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Użycie** — karta Użycie ułatwia wyświetlanie kluczowych parametrów rozliczeń dla kopii zapasowych. Informacje wyświetlane na tej karcie znajdują się na poziomie encji rozliczeniowej (kontener chroniony). Na przykład w przypadku serwera programu DPM, którego kopię zapasową jest na platformie Azure, można wyświetlić trend chronionych wystąpień i magazynu w chmurze używanego dla serwera programu DPM. Podobnie jeśli używasz SQL w usłudze Azure Backup lub SAP HANA w usłudze Azure Backup, ta karta zawiera informacje związane z użyciem na poziomie maszyny wirtualnej, w której znajdują się te bazy danych.

![Karta Użycie](./media/backup-azure-configure-backup-reports/usage.png)

* **Zadania** — karta Zadania umożliwia wyświetlanie długo działających trendów w zadaniach, takich jak liczba zadań, które nie powiodło się dziennie i najważniejsze przyczyny niepowodzenia zadania. Można wyświetlić te informacje zarówno na poziomie zagregowanym, jak i na poziomie elementu kopii zapasowej. Kliknięcie określonego elementu kopii zapasowej w siatce umożliwia wyświetlenie szczegółowych informacji o każdym zadaniu, które zostało wyzwolone dla tego elementu kopii zapasowej w wybranym zakresie czasu.

![Karta Zadania](./media/backup-azure-configure-backup-reports/jobs.png)

* **Zasady** — karta Zasady umożliwia wyświetlanie informacji o wszystkich aktywnych zasadach, takich jak liczba skojarzonych elementów i całkowita ilość miejsca w chmurze zużywanej przez elementy kopii zapasowej w ramach danej zasady. Kliknięcie określonej zasady umożliwia wyświetlanie informacji o każdym z powiązanych elementów kopii zapasowej.

![Karta Zasady](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Eksportowanie do programu Excel

Kliknięcie przycisku strzałki w dół w prawym górnym rogu dowolnego widżetu (tabeli/wykresu) powoduje wyekslenie zawartości tego widżetu jako arkusza programu Excel, zgodnie z istniejącymi filtrami. Aby wyeksportować więcej wierszy tabeli do programu Excel, można zwiększyć liczbę wierszy wyświetlanych na stronie za pomocą listy rozwijanej **Wiersze na stronę** u góry każdej siatki.

## <a name="pinning-to-dashboard"></a>Przypinanie do pulpitu nawigacyjnego

Kliknij ikonę Przypnij u góry każdego widżetu, aby przypiąć widżet do pulpitu nawigacyjnego portalu Azure. Pomaga to tworzyć dostosowane pulpity nawigacyjne dostosowane do wyświetlania najważniejszych potrzebnych informacji.

## <a name="cross-tenant-reports"></a>Raporty między dzierżawcami

Jeśli jesteś użytkownikiem [usługi Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawy, możesz użyć domyślnego filtru subskrypcji (klikając ikonę filtru w prawym górnym rogu witryny Azure portal), aby wybrać wszystkie subskrypcje, dla których chcesz zobaczyć dane. W ten sposób można wybrać la obszarów roboczych w dzierżawach, aby wyświetlić raporty z wieloma dzierżawami.

## <a name="conventions-used-in-backup-reports"></a>Konwencje używane w raportach kopii zapasowych

* Filtry działają od lewej do prawej i od góry do dołu na każdej karcie, to znaczy każdy filtr ma zastosowanie tylko do wszystkich tych widżetów, które są umieszczone po prawej stronie tego filtru lub poniżej tego filtru. 
* Kliknięcie kolorowego kafelka filtruje widżety pod kafelkiem dla rekordów odnoszących się do wartości tego kafelka. Na przykład kliknięcie kafelka *Ochrona zatrzymana* na karcie Elementy kopii zapasowej filtruje siatki i wykresy poniżej, aby wyświetlić dane dla elementów kopii zapasowej w stanie "Ochrona zatrzymana".
* Kafelki, które nie są kolorowe, nie można klikalnymi.
* Dane bieżącego częściowego dnia nie są wyświetlane w raportach. Tak więc, gdy wybrana wartość **zakresu czasu** jest, ***Ostatnie 7 dni,*** raport pokazuje rekordy z ostatnich 7 ukończonych dni (który nie obejmuje bieżącego dnia).
* Raport zawiera szczegóły zadań (oprócz zadań dziennika), które zostały **wyzwolone** w wybranym zakresie czasu. 
* Wartości wyświetlane dla magazynu w chmurze i wystąpienia chronione znajdują się na **końcu** wybranego zakresu czasu.
* Elementy kopii zapasowej wyświetlane w raportach są te elementy, które istnieją na **końcu** wybranego zakresu czasu. Elementy kopii zapasowej, które zostały usunięte w środku wybranego zakresu czasu, nie są wyświetlane. Ta sama konwencja ma zastosowanie do zasad tworzenia kopii zapasowych, jak również.

## <a name="query-load-times"></a>Czasy ładowania kwerendy

Widżety w raporcie kopii zapasowej są obsługiwane przez zapytania Kusto, które działają na obszarach roboczych LA użytkownika. Ponieważ te zapytania zazwyczaj obejmują przetwarzanie dużych ilości danych, z wieloma sprzężeniami, aby włączyć bogatsze spostrzeżenia, widżety mogą nie ładować się natychmiast, gdy użytkownik wyświetla raporty w dużej nieruchomości kopii zapasowej. Poniższa tabela zawiera przybliżone oszacowanie czasu ładowania różnych widżetów na podstawie liczby elementów kopii zapasowej i zakresu czasu, dla którego raport jest oglądany:

| **# Źródła danych**                         | **Horyzont czasowy** | **Czasy ładowania (ok.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 tys.                       | 1 miesiąc          | Płytki: 5-10 sek. <br> Siatki: 5-10 sekund <br> Wykresy: 5-10 sek. <br> Filtry na poziomie raportu: 5-10 sek.|
| ~5 tys.                       | 3 miesiące          | Płytki: 5-10 sek. <br> Siatki: 5-10 sekund <br> Wykresy: 5-10 sek. <br> Filtry na poziomie raportu: 5-10 sek.|
| ~10 tys.                       | 3 miesiące          | Płytki: 15-20 sek. <br> Siatki: 15-20 sek. <br> Wykresy: 1-2 min <br> Filtry na poziomie raportu: 25-30 sek.|
| ~15 tys.                       | 1 miesiąc          | Płytki: 15-20 sek. <br> Siatki: 15-20 sek. <br> Wykresy: 50-60 sek. <br> Filtry na poziomie raportu: 20-25 sek.|
| ~15 tys.                       | 3 miesiące          | Płytki: 20-30 sek. <br> Siatki: 20-30 sek. <br> Wykresy: 2-3 min <br> Filtry na poziomie raportu: 50-60 sek. |

## <a name="what-happened-to-the-power-bi-reports"></a>Co się stało z raportami usługi Power BI?
* Nasza wcześniejsza aplikacja szablonu usługi Power BI do raportowania (które pochodzą z konta usługi Azure Storage) znajduje się na ścieżce deprecation. Zaleca się, aby rozpocząć wysyłanie danych diagnostycznych magazynu do usługi Log Analytics, jak opisano powyżej, aby wyświetlić raporty.

* Ponadto schemat V1 wysyłania danych diagnostycznych do konta magazynu lub la workspace jest również na ścieżce deprecation. Oznacza to, że jeśli zostały napisane wszelkie niestandardowe zapytania lub automatyzacje oparte na schemacie V1, zaleca się zaktualizować te zapytania, aby użyć aktualnie obsługiwanego schematu V2.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o monitorowaniu i raportowaniu za pomocą usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)