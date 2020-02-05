---
title: Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych
description: Artykuł opisujący sposób używania Eksploratora kopii zapasowych do wykonywania monitorowania kopii zapasowych w czasie rzeczywistym w ramach magazynów, subskrypcji, regionów i dzierżawców
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992185"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych

Ponieważ organizacje wykonują kopie zapasowe więcej i więcej maszyn w chmurze, ważne jest posiadanie centralnej lokalizacji do wyświetlania informacji operacyjnych dotyczących kopii zapasowych w ramach dużej ilości danych w celu wydajnego monitorowania kopii zapasowych.

Skoroszyt programu Backup Explorer to wbudowany skoroszyt Azure Monitor umożliwiający klientom kopii zapasowych posiadanie pojedynczego okienka Glass do wykonywania działań monitorowania operacyjnego związanych z wykonywaniem kopii zapasowych w całej kopii zapasowej na platformie Azure (obejmującej dzierżawy, lokalizacje, subskrypcje, grupy zasobów i magazyny) — wszystko to w centralnej lokalizacji. W szerokim zakresie zapewnia następujące możliwości:

* **W perspektywie skalowania** — Zagregowany widok elementów kopii zapasowych, zadań, alertów, zasad i zasobów nieskonfigurowanych do tworzenia kopii zapasowych w całej kopii zapasowej. 
* **Analiza przechodzenia do szczegółów** — możesz wybrać opcję uzyskania szczegółowych informacji o poszczególnych jednostkach — zadania, alerty, zasady i elementy kopii zapasowej, a wszystko to w jednym miejscu.
* **Interfejsy** obsługujące akcje — po zidentyfikowaniu problemu można wykonać działania, przechodząc od bezproblemowo do elementu kopii zapasowej lub zasobu platformy Azure.

Powyższe możliwości są udostępniane przez natywną integrację z usługą Azure Resource Graph i skoroszytami Azure Monitor.

> [!NOTE]
> * Eksplorator kopii zapasowych jest obecnie dostępny tylko dla danych maszyny wirtualnej platformy Azure.
> * Eksplorator kopii zapasowych jest przeznaczony dla działającego pulpitu nawigacyjnego do wyświetlania informacji o kopiach zapasowych w ciągu ostatnich 7 dni (maksimum).
> * Obecnie Dostosowywanie szablonu Eksploratora kopii zapasowych nie jest obsługiwane. Ponadto nie zaleca się pisania niestandardowych automatyzacji w danych grafu zasobów platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

Aby uzyskać dostęp do Eksploratora kopii zapasowych, przejdź do dowolnego magazynu Recovery Services i kliknij link **Eksplorator kopii zapasowych** na stronie **Przegląd** .

![Szybki link do magazynu](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Kliknięcie linku spowoduje otwarcie Eksploratora kopii zapasowych, który zapewnia Zagregowany widok dla wszystkich magazynów i subskrypcji, do których masz dostęp. Jeśli używasz konta usługi Azure Lighthouse, możesz wyświetlać dane między wszystkimi dzierżawcami, do których masz dostęp (Zobacz więcej w poniższej sekcji w widokach międzydzierżawców).

![Strona docelowa Eksploratora](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Przypadki użycia Eksploratora kopii zapasowych

Eksplorator kopii zapasowych składa się z wielu kart, każda karta zawiera szczegółowe informacje dotyczące określonego rodzaju artefaktu kopii zapasowej, na przykład elementy kopii zapasowej, zadania, zasady itp. Ta sekcja zawiera krótkie omówienie poszczególnych kart. Filmy wideo zapewniają przykładowe przypadki użycia dla każdej z kart wraz z opisem różnych kontrolek dostępnych dla użytkownika.

### <a name="summary"></a>Podsumowanie

Karta Podsumowanie pozwala szybko skrócić się do ogólnego stanu kopii zapasowej. Można wyświetlić informacje, takie jak liczba chronionych elementów, liczba elementów, dla których ochrona nie została włączona, ile zadań zakończyło się pomyślnie w ciągu ostatnich 24 godzin i tak dalej. 

Każda z pozostałych kart reprezentuje odrębną jednostkę — na przykład: elementy kopii zapasowej, zadania tworzenia kopii zapasowej, alerty kopii zapasowych i zasady tworzenia kopii zapasowych. Można również wyświetlić informacje o maszynach, dla których nie skonfigurowano kopii zapasowej. Kliknięcie dowolnej z tych kart spowoduje wyświetlenie informacji specyficznych dla tej jednostki.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Elementy kopii zapasowej

Można wyświetlić wszystkie elementy kopii zapasowej odfiltrowane według subskrypcji, magazynu i innych parametrów. Kliknięcie nazwy elementu kopii zapasowej umożliwia otwarcie bloku Azure dla tego elementu kopii zapasowej. Na przykład, z tabeli, można zauważyć, że Ostatnia kopia zapasowa nie powiodła się dla elementu "X". Kliknięcie przycisku "X" spowoduje otwarcie bloku kopii zapasowej dla tego elementu, w którym można wyzwolić operację tworzenia kopii zapasowej na żądanie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Zadania

Możesz wyświetlić szczegóły wszystkich zadań wyzwalanych w ciągu ostatnich siedmiu dni, przechodząc do karty zadania. Tutaj można filtrować według operacji zadania, stanu zadania i kodu błędu (w przypadku zadań zakończonych niepowodzeniem).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Alerty

Aby wyświetlić szczegóły wszystkich alertów, które zostały wyzwolone w Twoich magazynach w ciągu ostatnich siedmiu dni, kliknij kartę alerty. W tym miejscu można filtrować rekordy według typu alertu (na przykład błędu kopii zapasowej, niepowodzenia przywracania), bieżącego stanu alertu (na przykład aktywnego lub rozwiązanego) i ważności alertu (na przykład krytycznego, ostrzegawczego, informacji). Możesz również przejść do maszyny wirtualnej platformy Azure, klikając link do maszyny wirtualnej i podejmując wymagane działanie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Zasady

Kliknięcie karty zasady umożliwia wyświetlenie najważniejszych informacji na temat wszystkich zasad tworzenia kopii zapasowych, które zostały utworzone w ramach danej kopii zapasowej. Można sprawdzić, ile elementów jest skojarzonych z każdą zasadą, wraz z zakresem przechowywania i częstotliwością wykonywania kopii zapasowych określoną przez poszczególne zasady.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Nie włączono kopii zapasowej

Ważne jest, aby administrator kopii zapasowej organizacji mógł szybko określić, które maszyny w organizacji nie mają jeszcze włączonej kopii zapasowej, aby można było włączyć funkcję tworzenia kopii zapasowych dla wszystkich maszyn, które wymagają ochrony. Kliknięcie tej opcji **nie włączono tworzenia kopii zapasowej** kart pomaga w tym zadaniu.

Na tej karcie zobaczysz tabelę zawierającą listę elementów, które nie są chronione. Jeśli Twoja organizacja korzysta z zasad przypisywania różnych tagów do maszyn produkcyjnych i maszyn testowych lub maszyn obsługujących różne funkcje, z których każdy może potrzebować oddzielnych zasad tworzenia kopii zapasowych, filtrowanie według tagów ułatwia przeglądanie informacji specyficznych dla Niektóre klasy maszyn. Kliknięcie nazwy dowolnego elementu przekierowuje użytkownika do bloku **Konfiguruj kopię zapasową** tego elementu, w którym można wybrać Tworzenie kopii zapasowej tego elementu przy użyciu odpowiednich zasad tworzenia kopii zapasowych.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Eksportowanie do programu Excel

Kliknięcie przycisku STRZAŁKA w dół w prawym górnym rogu dowolnego widżetu (tabela/wykres) eksportuje zawartość tego widżetu jako arkusz programu Excel, czyli z zastosowaniem istniejących filtrów. Aby wyeksportować więcej wierszy tabeli do programu Excel, można zwiększyć liczbę wierszy wyświetlanych na stronie przy użyciu listy rozwijanej **wiersze na stronę** w górnej części każdej karty.

## <a name="pinning-to-dashboard"></a>Przypinanie do pulpitu nawigacyjnego

Możesz kliknąć ikonę pinezki w górnej części każdego widżetu, aby przypiąć ten element widget do pulpitu nawigacyjnego Azure Portal. Ułatwia to tworzenie dostosowanych pulpitów nawigacyjnych, które są dostosowane do wyświetlania najważniejszych informacji, które są potrzebne.

## <a name="cross-tenant-views"></a>Widoki między dzierżawcami

Jeśli jesteś użytkownikiem usługi Azure Lighthouse z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawców, możesz użyć domyślnego filtru subskrypcji (klikając ikonę filtru w prawym górnym rogu Azure Portal), aby wybrać wszystkie subskrypcje, które chcesz Zobacz dane dla. Dzięki temu Eksplorator kopii zapasowych będzie umożliwiał agregowanie informacji na temat wszystkich magazynów w ramach tych subskrypcji. Dowiedz się więcej o usłudze Azure Lighthouse [tutaj](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak używać Azure Monitor do uzyskiwania wglądu w dane kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)