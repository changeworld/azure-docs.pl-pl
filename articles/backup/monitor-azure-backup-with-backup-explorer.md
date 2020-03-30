---
title: Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych
description: W tym artykule opisano, jak używać Eksploratora kopii zapasowych do monitorowania w czasie rzeczywistym kopii zapasowych w magazynach, subskrypcjach, regionach i dzierżawach.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131809"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorowanie kopii zapasowych za pomocą Eksploratora kopii zapasowych

W miarę jak organizacje tworzą kopię zapasową coraz większej liczby maszyn w chmurze, coraz ważniejsze staje się efektywne monitorowanie tych kopii zapasowych. Najlepszym sposobem, aby rozpocząć jest wykorzystanie centralnej lokalizacji do przeglądania informacji operacyjnych w całej dużej nieruchomości.

Eksplorator kopii zapasowych to wbudowany skoroszyt usługi Azure Monitor, który zapewnia klientom usługi Azure Backup tę pojedynczą, centralną lokalizację. Eksplorator kopii zapasowych ułatwia monitorowanie działań operacyjnych w całej nieruchomości kopii zapasowej na platformie Azure, obejmujące dzierżawy, lokalizacje, subskrypcje, grupy zasobów i magazyny. Zasadniczo Eksplorator kopii zapasowych oferuje następujące możliwości:

* **Perspektywa na skalę:** Uzyskaj zagregowany widok elementów kopii zapasowej, zadań, alertów, zasad i zasobów, które nie są jeszcze skonfigurowane do tworzenia kopii zapasowych w całej nieruchomości. 
* **Analiza szczegółowa:** Wyświetl szczegółowe informacje o każdym z zadań, alertów, zasad i elementów kopii zapasowej w jednym miejscu.
* **Interfejsy z zasysaniem:** Po zidentyfikowaniu problemu można go rozwiązać, przechodząc bezproblemowo do odpowiedniego elementu kopii zapasowej lub zasobu platformy Azure.

Te możliwości są dostarczane gotowe do użycia przez natywną integrację z programem Azure Resource Graph i skoroszytami usługi Azure Monitor.

> [!NOTE]
> * Eksplorator kopii zapasowych jest obecnie dostępny tylko dla danych maszyn wirtualnych platformy Azure.Backup Explorer is currently available only for Azure virtual machines (VMs) data.
> * Eksplorator kopii zapasowych ma być operacyjnym pulpitem nawigacyjnym do wyświetlania informacji o kopiach zapasowych w ciągu ostatnich 7 dni (maksymalnie).
> * Eksplorator kopii zapasowych nie jest obecnie obsługiwany w chmurach krajowych.
> * Obecnie dostosowywanie szablonu Eksploratora kopii zapasowych nie jest obsługiwane. 
> * Nie zaleca się pisania automatyzacji niestandardowych na danych usługi Azure Resource Graph.

## <a name="get-started"></a>Wprowadzenie

Do Eksploratora kopii zapasowych można uzyskać, przechodząc do dowolnego magazynu usług odzyskiwania i wybierając łącze **Eksploratora kopii zapasowych** w okienku **Przegląd.**

![Szybkie łącze vault](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Wybranie łącza powoduje otwarcie Eksploratora kopii zapasowych, który zapewnia widok zagregowany we wszystkich magazynach i subskrypcjach, do których masz dostęp. Jeśli używasz konta latarnika platformy Azure, możesz wyświetlać dane we wszystkich dzierżawach, do których masz dostęp. Aby uzyskać więcej informacji, zobacz sekcję "Widoki między dzierżawami" na końcu tego artykułu.

![Strona docelowa Eksploratora kopii zapasowych](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Przypadki użycia Eksploratora kopii zapasowych

Eksplorator kopii zapasowych wyświetla wiele kart, z których każda zawiera szczegółowe informacje o określonym artefaktzie kopii zapasowej (na przykład elemencie kopii zapasowej, zadaniu lub zasadach). Ta sekcja zawiera krótki przegląd każdej z kart. Filmy wideo zawierają przykładowe przypadki użycia dla każdego artefaktu kopii zapasowej, wraz z opisami dostępnych formantów.

### <a name="the-summary-tab"></a>Karta Podsumowanie

Karta **Podsumowanie** umożliwia szybkie spojrzenie na ogólny stan nieruchomości kopii zapasowej. Na przykład można wyświetlić liczbę chronionych elementów, liczbę elementów, dla których ochrona nie została włączona, lub liczbę zadań, które zakończyły się powodzeniem w ciągu ostatnich 24 godzin.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Karta Elementy kopii zapasowej

Każdy z elementów kopii zapasowej można filtrować i wyświetlać według subskrypcji, przechowalni i innych cech. Wybierając nazwę elementu kopii zapasowej, można otworzyć okienko platformy Azure dla tego elementu. Na przykład z tabeli można zaobserwować, że ostatnia kopia zapasowa nie powiodła się dla elementu *X*. Wybierając X *,* można otworzyć okienko **kopii zapasowej** elementu, w którym można wyzwolić operację tworzenia kopii zapasowej na żądanie.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Karta Zadania

Wybierz kartę **Zadania,** aby wyświetlić szczegóły wszystkich zadań, które zostały wyzwolone w ciągu ostatnich 7 dni. W tym miejscu można filtrować według *operacji zlecenia,* *stanu zadania*i *kodu błędu* (dla zadań po awarii).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Karta Alerty

Wybierz kartę **Alerty,** aby wyświetlić szczegóły wszystkich alertów, które zostały wygenerowane w magazynach w ciągu ostatnich 7 dni. Alerty można filtrować według typu *(Błąd kopii zapasowej* lub *Niepowodzenie przywracania),* bieżący stan *(Aktywny* lub Rozwiązany) i *ważność**(Krytyczne*, *Ostrzeżenie*lub *Informacje*). Można również wybrać łącze, aby przejść do maszyny Wirtualnej platformy Azure i podjąć wszelkie niezbędne działania.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Karta Zasady

Można wybrać **kartę Zasady,** aby wyświetlić kluczowe informacje o wszystkich zasadach tworzenia kopii zapasowych utworzonych w całym zestawieniu kopii zapasowych. Można wyświetlić liczbę elementów skojarzonych z poszczególnych zasad, wraz z zakresem przechowywania i częstotliwością tworzenia kopii zapasowych określonymi przez zasady.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Karta Kopia zapasowa nie została włączona

Kopia zapasowa powinna być włączona dla wszystkich maszyn, które wymagają ochrony. Za pomocą Eksploratora kopii zapasowych administratorzy kopii zapasowych mogą szybko określić, które komputery w organizacji nie są jeszcze chronione przez kopię zapasową. Aby wyświetlić te informacje, wybierz kartę **Kopia zapasowa nie została włączona.**

W okienku **Kopia zapasowa nie została włączona** tabela z listą niechronionych komputerów. Organizacja może przypisać różne tagi do maszyn produkcyjnych i testowych lub do maszyn, które obsługują różne funkcje. Ponieważ każda klasa komputerów wymaga osobnej zasady tworzenia kopii zapasowych, filtrowanie według tagów ułatwia wyświetlanie informacji specyficznych dla każdego z nich. Wybranie nazwy dowolnego komputera powoduje przekierowanie użytkownika do okienka **Konfiguruj kopię zapasową** tego komputera, w którym można zastosować odpowiednie zasady tworzenia kopii zapasowych.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Eksportuj do programu Excel

Zawartość dowolnej tabeli lub wykresu można wyeksportować jako arkusz kalkulacyjny programu Excel. Zawartość jest eksportowana w stanie, w jakim są stosowane istniejące filtry. Aby wyeksportować dodatkowe wiersze tabeli, można zwiększyć liczbę wierszy wyświetlanych na stronie za pomocą listy rozwijanej **Wiersze na stronę** u góry każdej karty.

## <a name="pin-to-the-dashboard"></a>Przypinanie do pulpitu nawigacyjnego

Możesz wybrać ikonę "pin" u góry każdej tabeli lub wykresu, aby przypiąć ją do pulpitu nawigacyjnego witryny Azure portal. Przypinanie tych informacji ułatwia utworzenie dostosowanego pulpitu nawigacyjnego dostosowanego do wyświetlania informacji, które są dla Ciebie najważniejsze.

## <a name="cross-tenant-views"></a>Widoki między dzierżawcami

Jeśli jesteś użytkownikiem usługi Azure Lighthouse z delegowanym dostępem do subskrypcji w wielu środowiskach dzierżawy, możesz użyć domyślnego filtru subskrypcji. Wyświetlasz subskrypcje, dla których chcesz wyświetlić dane, wybierając ikonę "filtruj" w prawym górnym rogu witryny Azure portal. Korzystając z tej funkcji, Eksplorator kopii zapasowych agreguje informacje o wszystkich magazynach w wybranych subskrypcjach. Aby dowiedzieć się więcej, zobacz [Co to jest latarnia morska platformy Azure?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak korzystać z usługi Azure Monitor w celu uzyskiwania szczegółowych informacji na temat danych kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
