---
title: Grupy zabezpieczeń sieci platformy Azure visualizing dzienników przepływów przy użyciu usługi Power BI | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń z usługą Power BI.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 6df49f9cd308f4bb9b1fef6e5860872526ce8bb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860883"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Dzienniki przepływu visualizing sieciową grupę zabezpieczeń z usługą Power BI

Dzienniki przepływu sieciowej grupy zabezpieczeń umożliwiają wyświetlanie informacji o przychodzący i wychodzący ruch IP na grupy zabezpieczeń sieci. Przepływ są Pokaż dzienniki przepływy wychodzące i przychodzące na podstawie reguły w poszczególnych kart Sieciowych przepływ ma zastosowanie do 5-elementowych informacji o przepływie (źródłowy i docelowy adres IP, Port źródłowy i docelowy, Protocol), oraz czy ruch był dozwolony.

Może być trudne uzyskać wgląd w dane rejestrowania usługi flow, przeszukując ręcznie pliki dziennika. W tym artykule firma Microsoft zapewnia rozwiązanie wizualizacji najnowszych dzienników przepływu i Dowiedz się więcej o ruchu w sieci.

> [!Warning]  
> Poniższe kroki pracy z dzienników przepływu w wersji 1. Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będzie działać w wersji 2 pliki dziennika, bez żadnych modyfikacji.

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu łączymy usługi Power BI desktop do konta magazynu, który jest skonfigurowany jako obiekt sink danych rejestrowanie przepływu sieciowych grup zabezpieczeń. Po możemy połączyć się z naszym konta magazynu, usługa Power BI pobiera i analizuje dzienniki aby zapewnić wizualną reprezentację ruchu, który jest zalogowany, grup zabezpieczeń sieci.

Korzystanie z wizualizacji, podane w szablonie, który można sprawdzić:

* Najważniejsze nadajniki
* Dane szeregów czasowych przepływu decyzją regułę i kierunek
* Przepływy według adresów MAC interfejsów sieciowych
* Przepływy według sieciowych grup zabezpieczeń i reguł
* Przepływy według portów docelowych

Z szablonem jest edytowalny, dzięki czemu możesz zmodyfikować go, aby dodać nowe dane i wizualizacje, lub Edytuj zapytania do własnych potrzeb.

## <a name="setup"></a>Konfigurowanie

Przed przystąpieniem do wykonywania, konieczne jest posiadanie sieci grupy przepływu rejestrowanie zabezpieczeń włączone w jednej lub wielu grup zabezpieczeń sieci w ramach Twojego konta. Aby uzyskać instrukcje na temat włączania dzienników przepływu zabezpieczeń sieci zapoznaj się z następującym artykułem: [Wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

Musi również mieć zainstalowanego na maszynie oraz wystarczającą ilością wolnego miejsca na tym komputerze, aby pobrać i ładowania danych dziennika, która istnieje na koncie magazynu klienta programu Power BI Desktop.

![Diagram programu Visio][1]

### <a name="steps"></a>Kroki

1. Pobierz i otwórz następujący szablon usługi Power BI w aplikacja Power BI Desktop [szablonu dzienników przepływów sieciowych obserwatora usługi Power BI](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Podaj wymagane parametry zapytania
   1. **StorageAccountName** — Określa nazwę konta magazynu zawierające dzienniki przepływu sieciowych grup zabezpieczeń, które chcesz załadować i wizualizację.
   1. **NumberOfLogFiles** — określa liczbę plików dziennika, które chcesz pobrać i wizualizacji w usłudze Power BI. Na przykład, jeśli określono 50 50 najnowszych plików dziennika. Jeśli będziemy mieć 2 sieciowych grup zabezpieczeń włączone i skonfigurowane do wysyłania dzienników przepływu sieciowych grup zabezpieczeń do tego konta, mogą być wyświetlane w ciągu ostatnich 25 godzin dzienników.

      ![główne usługi Power BI][2]

1. Wprowadź klucz dostępu konta magazynu. Klucze dostępu nie jest ważna można znaleźć, przechodząc do konta magazynu w witrynie Azure portal i wybierając opcję **klucze dostępu** z poziomu menu ustawień. Kliknij przycisk **Connect** następnie zastosować zmiany.

    ![Klucze dostępu][3]

    ![klucz dostępu 2][4]

4. Dzienniki są, pobrać i przeanalizować i można teraz korzystać ze wstępnie utworzonych wizualizacji.

## <a name="understanding-the-visuals"></a>Opis elementów wizualnych

Udostępnionego w szablonie są zestawem wizualizacje, które ułatwiają sensu danych dzienników przepływu sieciowej grupy zabezpieczeń. Na poniższych ilustracjach przedstawiono przykładowe jak pulpit nawigacyjny wygląda po wypełniany danymi. Poniżej omówiony bardziej szczegółowo każdy element wizualny 

![powerbi][5]
 
Pokazuje visual nadajniki górnej określone adresy IP, które rozpoczęły większość połączeń w okresie. Rozmiar pola odpowiada względną liczbę połączeń. 

![toptalkers][6]

Poniższe wykresy serii czasu pokazują liczbę przepływów w okresie. Górny wykres jest posegmentowana według kierunku przepływu, a dolna składa się z decyzją wprowadzone (Zezwalaj lub Odmów). Przy użyciu tego elementu wizualnego możesz sprawdzić trendy ruchu, wraz z upływem czasu i wykryć wszelkie nietypowe skoki lub odrzucić w ruchu lub segmentacji ruchu.

![flowsoverperiod][7]

W poniższych wykresów pokazano przepływów dla interfejsu sieciowego, z górnym posegmentowana według kierunku przepływu i niższym posegmentowana według decyzji. Dzięki tym informacjom możesz uzyskać wgląd w której maszyny wirtualne przekazywane najbardziej względem innych użytkowników, a jeśli ruch do określonej maszyny Wirtualnej jest dozwolony lub blokowany.

![flowspernic][8]

Poniższy wykres pierścieniowy w kółko pokazuje podział przepływy według portów docelowych. Dzięki tym informacjom można wyświetlić porty docelowe najczęściej używane, używane w określonym przedziale czasu.

![Wykres pierścieniowy][9]

Poniższy wykres słupkowy przedstawia przepływ przez sieciową grupę zabezpieczeń i reguł. Dzięki tym informacjom można wyświetlić odpowiedzialnych za większość ruchu i podział ruchu sieciowe grupy zabezpieczeń na sieciową grupę zabezpieczeń, zgodnie z regułą.

![barchart][10]
 
Następujące wykresy informacyjne zawierają informacje o obecnych w dziennikach, liczba przechwycone przez okres i daty najwcześniejszej dziennika przechwycone przepływów sieciowych grup zabezpieczeń. Ta informacja daje wyobrażenie o jakie sieciowe grupy zabezpieczeń są rejestrowane i zakres dat przepływów.

![infochart1][11]

![infochart2][12]

Ten szablon zawiera następujące fragmentatory, aby możliwe było wyświetlić tylko dane, których interesuje Cię najbardziej. Można filtrować według grupy zasobów, sieciowe grupy zabezpieczeń i reguł. Można również filtrować w 5-elementowe spójne kolekcje informacji, decyzji i czas, który został zapisany w dzienniku.

![fragmentatory][13]

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu pokazaliśmy, czy przy użyciu dzienników przepływu grupy zabezpieczeń w sieci, udostępnianych przez usługi Network Watcher i usługi Power BI, możemy Wizualizacja i rozumienie ruchu. Za pomocą podanego szablonu usługi Power BI pobiera dzienniki bezpośrednio z usługi storage i przetwarza je lokalnie. Czas ładowania szablonu różni się w zależności od liczby żądane pliki, a całkowity rozmiar plików pobrane.

Możesz dostosować ten szablon do własnych potrzeb. Istnieje wiele sposobów, za pomocą usługi Power BI dzienniki sieciowych grup zabezpieczeń przepływu. 

## <a name="notes"></a>Uwagi

* Dzienniki, które domyślnie są przechowywane w `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Jeśli istnieją inne dane w innym katalogu są zapytania do ściągania i przetwarzać dane muszą zostać zmodyfikowane.

* Podany szablon nie jest zalecane do użytku z dzienników z ponad 1 GB.

* Jeśli masz dużą ilość dzienników, firma Microsoft zaleca badania rozwiązania za pomocą innego magazynu danych, takich jak usługa Data Lake lub SQL server.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu programu Elastic Stack, odwiedzając [wizualizacji Azure Network Watcher NSG dzienników przepływów przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
