---
title: Wizualizacja dzienników przepływu sieciowej sieciowej sieciowej platformy Azure — usługa Power BI
titleSuffix: Azure Network Watcher
description: Na tej stronie opisano sposób wizualizacji dzienników przepływu sieciowej sieciowej w usłudze Power BI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840608"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Wizualizacja dzienników przepływu sieciowej grupy zabezpieczeń za pomocą usługi Power BI

Dzienniki przepływu sieciowej grupy zabezpieczeń umożliwiają wyświetlanie informacji o ruchu ip przychodzących i wychodzących w sieciowych grupach zabezpieczeń. Te dzienniki przepływu pokazują przepływy wychodzące i przychodzące na podstawie reguły, karta sieciowa, którą przepływ dotyczy, 5-krotka informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub odrzucany.

Może być trudne do uzyskania wglądu w dane rejestrowania przepływu ręcznie przeszukując pliki dziennika. W tym artykule udostępniamy rozwiązanie do wizualizacji najnowszych dzienników przepływu i dowiedzenia się o ruchu w sieci.

> [!Warning]  
> Poniższe kroki działają z dziennikami przepływu w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieci .](network-watcher-nsg-flow-logging-overview.md) Poniższe instrukcje nie będą działać z wersją 2 plików dziennika, bez modyfikacji.

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu łączymy pulpit usługi Power BI z kontem magazynu, które skonfigurowaliśmy jako ujście dla naszych danych rejestrowania przepływu sieciowej. Po nawiązaniu połączenia z naszym kontem magazynu usługa Power BI pobiera i analizuje dzienniki, aby zapewnić wizualną reprezentację ruchu rejestrowanego przez grupy zabezpieczeń sieciowych.

Za pomocą wizualizacji dostarczonych w szablonie można sprawdzić:

* Top Talkers
* Dane przepływu szeregów czasowych według kierunku i decyzji reguły
* Przepływy według adresu MAC interfejsu sieciowego
* Przepływy przez nsg i reguły
* Przepływy według portu docelowego

Dostarczony szablon można edytować, dzięki czemu można go zmodyfikować, aby dodać nowe dane, wizualizacje lub edytować zapytania zgodnie z potrzebami.

## <a name="setup"></a>Konfiguracja

Przed rozpoczęciem należy włączyć rejestrowanie przepływu sieciowej grupy zabezpieczeń na jednej lub wielu grupach zabezpieczeń sieciowych na koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieciowych można znaleźć w następującym artykule: [Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieciowych](network-watcher-nsg-flow-logging-overview.md).

Na komputerze musi być zainstalowany klient programu Power BI Desktop oraz wystarczająca ilość wolnego miejsca na komputerze, aby pobrać i załadować dane dziennika, które istnieją na koncie magazynu.

![Diagram programu Visio][1]

### <a name="steps"></a>Kroki

1. Pobieranie i otwieranie następującego szablonu usługi Power BI w [szablonie dzienników przepływu programu Power BI Network Watcher PowerBI](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Wprowadź wymagane parametry kwerendy
   1. **StorageAccountName** — określa nazwę konta magazynu zawierającego dzienniki przepływu nsg, które chcesz załadować i wizualizować.
   1. **NumberOfLogFiles** — określa liczbę plików dziennika, które chcesz pobrać i wizualizować w usłudze Power BI. Na przykład jeśli określono 50, 50 najnowszych plików dziennika. Jeśli mamy 2 nsgs włączone i skonfigurowane do wysyłania dzienników przepływu NSG do tego konta, a następnie ostatnich 25 godzin dzienników można przeglądać.

      ![zasilanie GŁÓWNE BI][2]

1. Wprowadź klucz dostępu dla swojego konta magazynu. Prawidłowe klucze dostępu można znaleźć, przechodząc do konta magazynu w witrynie Azure portal i wybierając **klawisze dostępu** z menu Ustawienia. Kliknij **przycisk Połącz,** a następnie zastosuj zmiany.

    ![klawisze dostępu][3]

    ![klucz dostępu 2][4]

4. Dzienniki są pobierane i analizowane i można teraz korzystać z wstępnie utworzonych wizualizacji.

## <a name="understanding-the-visuals"></a>Opis wizualizacji

W szablonie przedstawiono zestaw wizualizacji, które pomagają zrozumieć dane dziennika przepływu nsg. Na poniższych obrazach przedstawiono przykład, jak wygląda pulpit nawigacyjny po wypełnieniu danymi. Poniżej badamy każdy obraz w sposób bardziej szczegółowy 

![powerbi][5]
 
Wizualizacja Top Talkers pokazuje usługi IP, które zainicjowały najwięcej połączeń w określonym okresie. Rozmiar pól odpowiada względnej liczbie połączeń. 

![toptalkers][6]

Poniższe wykresy szeregów czasowych pokazują liczbę przepływów w tym okresie. Górny wykres jest segmentowany według kierunku przepływu, a dolny jest segmentowany przez podjętą decyzję (zezwalaj lub odrzucaj). Dzięki tej wizualizacji możesz z czasem sprawdzać trendy ruchu i wykrywać wszelkie nietypowe skoki lub spadek natężenia ruchu lub segmentacji ruchu.

![okres przepływów][7]

Poniższe wykresy przedstawiają przepływy na interfejs sieciowy, z górnym segmentem według kierunku przepływu i niższym segmentem według podjętej decyzji. Dzięki tym informacjom można uzyskać wgląd w to, które maszyny wirtualne komunikowały się najbardziej względem innych osób, a ruch do określonej maszyny Wirtualnej jest dozwolony lub odrzucany.

![flowspernic][8]

Na poniższym wykresie kółka pierścieniowego przedstawiono podział przepływów według portu docelowego. Dzięki tym informacjom można wyświetlić najczęściej używane porty docelowe używane w określonym czasie.

![pierścień][9]

Na poniższym wykresie słupkowym przedstawiono przepływ przez nsg i regułę. Dzięki tym informacjom można zobaczyć sieciowe sieciowe odpowiedzialne za największy ruch i awarię ruchu na sieciowej sieciowej grupowej według reguły.

![barchart ( barchart )][10]
 
Na poniższych wykresach informacyjnych są wyświetlane informacje o sieciowych sieciowych sieciowych znajdujących się w dziennikach, liczbie przepływów przechwyconych w okresie i dacie przechwycenia najwcześniejszego dziennika. Te informacje dają wyobrażenie o tym, jakie cele nsg są rejestrowane i zakres dat przepływów.

![wykres informacyjny1][11]

![wykres informacyjny2][12]

Ten szablon zawiera następujące fragmentatory, aby umożliwić wyświetlanie tylko danych, które są najbardziej interesujące. Można filtrować grupy zasobów, sieciowe grupy zabezpieczeń i reguły. Można również filtrować informacje o 5 krotek, decyzji i czasie dziennika został napisany.

![Fragmentatory][13]

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu pokazaliśmy, że za pomocą dzienników przepływów sieciowej grupy zabezpieczeń dostarczonych przez Network Watcher i Power BI jesteśmy w stanie wizualizować i rozumieć ruch. Korzystając z dostarczonego szablonu, usługa Power BI pobiera dzienniki bezpośrednio z magazynu i przetwarza je lokalnie. Czas ładowania szablonu różni się w zależności od liczby żądanych plików i całkowitego rozmiaru pobranych plików.

Możesz dostosować ten szablon do swoich potrzeb. Istnieje wiele sposobów korzystania z usługi Power BI z dziennikami przepływu grupy zabezpieczeń sieciowych. 

## <a name="notes"></a>Uwagi

* Dzienniki domyślnie są przechowywane w`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Jeśli w innym katalogu istnieją inne dane, należy zmodyfikować zapytania do ściągania i przetwarzania danych.

* Podany szablon nie jest zalecany do użycia z więcej niż 1 GB dzienników.

* Jeśli masz dużą ilość dzienników, zaleca się zbadanie rozwiązania przy użyciu innego magazynu danych, takich jak Usługa Data Lake lub serwer SQL.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu sieciowej sieciowej sieciowej za pomocą stosu elastycznego, odwiedzając [wizualizuj dzienniki przepływu sieciowej sieci obserwatorzy sieci Azure przy użyciu narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
