---
title: Grupy zabezpieczeń sieci Azure visualizing przepływu dzienników przy użyciu usługi Power BI | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu wizualizacji NSG przepływu dzienników przy użyciu usługi Power BI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 19bd7ed4bab915d7918a192a046653666cfaa498
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2017
ms.locfileid: "24878585"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Dzienniki przepływu visualizing sieciową grupę zabezpieczeń z usługi Power BI

Dzienniki przepływu sieciowej grupy zabezpieczeń umożliwiają wyświetlanie informacji o przychodzące i wychodzące ruchu IP na grupy zabezpieczeń sieci. Te przepływać Pokaż dzienniki dotyczy przepływów wychodzącego i przychodzącego na podstawie reguł na przepływ karty interfejsu Sieciowego, 5-elementowej informacji o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego protokołu), i jeśli ruch został dozwolony lub niedozwolony.

Może być trudne uzyskać wgląd w dane rejestrowania przepływu ręcznie, wyszukując pliki dziennika. W tym artykule udostępniamy rozwiązania wizualizacji najnowsze dzienniki przepływu i Dowiedz się więcej o ruchu w sieci.

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu połączymy Power BI desktop na konto magazynu, które mają został skonfigurowany jako obiekt sink dla grupy NSG przepływu rejestrowania danych. Po nawiązać nasze konto magazynu usługi Power BI pobiera i analizuje dzienniki, aby zapewnić wizualną reprezentację ruchu, który jest rejestrowane przez grup zabezpieczeń sieci.

Za pomocą wizualnych podane w szablonie, który można sprawdzić:

* Górny Talkers
* Czas serii przepływu danych decyzją kierunek i reguły
* Przepływy według adresu MAC interfejsu sieciowego
* Przepływy NSG i reguły
* Przepływy przez Port docelowy

Dostarczonego szablonu jest edytowalny, więc można zmodyfikować go, aby dodać nowe dane, elementy wizualne, lub Edytuj zapytania w zależności od potrzeb.

## <a name="setup"></a>Konfiguracja

Przed rozpoczęciem, musi mieć sieci grupy przepływu rejestrowanie zabezpieczeń włączone w jednej lub wielu grup zabezpieczeń sieci na Twoim koncie. Instrukcje dotyczące włączania zabezpieczenia sieci przepływu dzienniki, zapoznaj się z następującym artykułem: [wprowadzenie do przepływu rejestrowania dla grup zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md).

Musi być zainstalowany na tym komputerze i wystarczającą ilością wolnego miejsca na tym komputerze, aby pobrać i ładowania danych dziennika, która istnieje na koncie magazynu klient programu Power BI Desktop.

![Diagram programu Visio][1]

### <a name="steps"></a>Kroki

1. Pobierz i otwórz następujący szablon usługi Power BI w aplikacji Power BI Desktop [przepływu PowerBI obserwatora sieciowego rejestruje szablonu](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Wprowadź wymagane parametry zapytania
    1. **StorageAccountName** — Określa nazwę konta magazynu zawierające dzienniki przepływu NSG, które chcesz załadować i wizualizacji.
    1. **NumberOfLogFiles** — określa liczbę plików dziennika, które chcesz pobrać i wizualizację w usłudze Power BI. Na przykład, jeśli określono 50 50 najnowszych plików dziennika. FF mamy 2 NSG włączona i skonfigurowana w celu wysłania dzienników przepływu NSG do tego konta, a następnie ostatnich 25 godzin dzienników mogą być wyświetlane.

    ![main Power BI][2]

1. Wprowadź klucz dostępu dla konta magazynu. Klawisze dostępu prawidłowy można znaleźć przechodząc do konta magazynu na platformie Azure, portalu i wybierając **klucze dostępu** z menu ustawień. Kliknij przycisk **Connect** następnie zastosować zmiany.

    ![Klawisze dostępu][3]

    ![klucz dostępu 2][4]

4.  Dzienniki są pobrać i przeanalizować i wstępnie utworzone elementy wizualne mogą teraz wykorzystywać.

## <a name="understanding-the-visuals"></a>Opis wizualnych

Podany w szablonie to zbiór elementów wizualnych ułatwiających znaczeniu danych dziennika przepływu NSG. Na poniższych ilustracjach przedstawiono przykład pulpitu nawigacyjnego wygląd po wypełniane przy użyciu danych. Poniżej omówione każdego visual większej liczby szczegółów 

![Usługa Power BI][5]
 
Pokazuje visual Talkers góry określić adresy IP, z którego zainicjowano większość połączeń w okresie. Rozmiar pola odpowiada względną liczbę połączeń. 

![toptalkers][6]

Następujące wykresy serii czasu Pokaż liczbę przepływów w okresie. Górny wykresu jest segmentowanych przez kierunek przepływu i niższa jest segmentem decyzją (Zezwalaj lub Odmów). Z tego elementu wizualnego można przejrzeć trendy ruchu wraz z upływem czasu i dodatkowe żadnych nietypowych wzrostów lub spadek ruchu lub segmentacji ruchu.

![flowsoverperiod][7]

Następujący program wykresy przepływów dla interfejsu sieciowego, z górnym segmentowanych przez kierunek przepływu i niższe rozdzielonych decyzji. Dzięki tym informacjom można uzyskać wgląd w której maszyny wirtualne przekazywane najbardziej względem innych użytkowników, a jeśli ruch do określonej maszyny Wirtualnej są dozwolone lub nie.

![flowspernic][8]

Na poniższym wykresie kółka pierścień przedstawia podział przepływów przez Port docelowy. Dzięki tym informacjom można wyświetlić porty docelowe najczęściej używane, używane w określonym przedziale czasu.

![pierścień][9]

Na poniższym wykresie pasek przedstawia przepływ NSG i reguły. Dzięki tym informacjom można wyświetlić grup NSG odpowiedzialny za większość ruchu i z podziałem ruchu na grupy NSG przez regułę.

![barchart][10]
 
Następujące wykresy informacyjne zawierają informacje dotyczące grup NSG w dziennikach, liczbę przepływów przechwycone przez okres i Data najwcześniejszą dziennika przechwycone. Ta informacja daje wyobrażenie o jakie grupy NSG są rejestrowane i zakres dat przepływów.

![infochart1][11]

![infochart2][12]

Ten szablon obejmuje następujące fragmentatory pozwala wyświetlić dane, które największe znaczenie. Można filtrować według grup zasobów, grup NSG i zasady. Można również filtrować informacji 5-elementowej, decyzji i czas, który został zapisany w dzienniku.

![fragmentatory][13]

## <a name="conclusion"></a>Podsumowanie

Firma Microsoft pokazano, w tym scenariuszu czy przy użyciu dzienników przepływu grupy zabezpieczeń sieci zapewniane przez obserwatora sieciowego i usługi Power BI, możemy zwizualizować i zrozumieć ruchu. Za pomocą podanego szablonu usługi Power BI pobiera dzienniki bezpośrednio z magazynu i przetwarza je lokalnie. Czas potrzebny do załadowania szablonu może być różna w zależności od liczby żądane pliki i pobrać łączny rozmiar plików.

Możesz dostosować ten szablon do potrzeb. Istnieje wiele sposobów wiele, że za pomocą usługi Power BI dzienniki przepływu grupy zabezpieczeń sieci. 

## <a name="notes"></a>Uwagi

* Dzienniki domyślnie są przechowywane w`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Jeśli istnieją inne dane w innym katalogu one zapytań do ściągania i procesu dane muszą zostać zmodyfikowane.

* Podany szablon nie jest zalecane używanie z dzienników z ponad 1 GB.

* Jeśli masz dużą ilość dzienniki, firma Microsoft zaleca, aby zbadać rozwiązania za pomocą innego magazynu danych, takich jak Data Lake lub SQL server.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizacji dzienników przepływu NSG do stosu Elastick odwiedzając [dzienników przepływu wizualizacji NSG obserwatora sieci Azure przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
