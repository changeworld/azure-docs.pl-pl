---
title: Wizualizuj wzorce ruchu sieciowego za pomocą narzędzi open source
titleSuffix: Azure Network Watcher
description: Na tej stronie opisano sposób używania przechwytywania pakietów obserwatora sieci za pomocą capanalizy do wizualizacji wzorców ruchu do i z maszyn wirtualnych.
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
ms.openlocfilehash: f36db28b58cd57b6407019b378a82632aa6c6228
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840659"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Wizualizuj wzorce ruchu sieciowego do i z maszyn wirtualnych za pomocą narzędzi typu open source

Przechwytywanie pakietów zawiera dane sieciowe, które umożliwiają przeprowadzanie kryminalistyki sieciowej i głęboką inspekcję pakietów. Istnieje wiele narzędzi źródłowych otwieranych, za pomocą których można analizować przechwytywanie pakietów w celu uzyskania szczegółowych informacji o sieci. Jednym z takich narzędzi jest CapAnalysis, narzędzie do wizualizacji przechwytywania pakietów typu open source. Wizualizacja danych przechwytywania pakietów jest cennym sposobem szybkiego uzyskiwania szczegółowych informacji na temat wzorców i anomalii w sieci. Wizualizacje umożliwiają również dzielenie się takimi spostrzeżeniami w łatwy w stanie eksploatacyjnym.

Kontrola sieci platformy Azure zapewnia możliwość przechwytywania danych, umożliwiając wykonywanie przechwytywania pakietów w sieci. W tym artykule przedstawiono sposób wizualizacji i uzyskiwania szczegółowych informacji z przechwytywania pakietów przy użyciu capanalizy za pomocą funkcji Kontrola sieci.

## <a name="scenario"></a>Scenariusz

Masz prostą aplikację sieci web wdrożoną na maszynie Wirtualnej na platformie Azure, aby użyć narzędzi open source do wizualizacji ruchu sieciowego w celu szybkiego identyfikowania wzorców przepływu i wszelkich możliwych anomalii. Za pomocą funkcji Kontrola sieci można uzyskać przechwytywanie pakietów środowiska sieciowego i przechowywać je bezpośrednio na koncie magazynu. CapAnalysis można następnie pozyskiwania przechwytywania pakietów bezpośrednio z obiektu blob magazynu i wizualizować jego zawartość.

![scenariusz][1]

## <a name="steps"></a>Kroki

### <a name="install-capanalysis"></a>Instalowanie capanalysis

Aby zainstalować CapAnalysis na maszynie wirtualnej, można https://www.capanalysis.net/ca/how-to-install-capanalysiszapoznać się z oficjalnymi instrukcjami tutaj .
Aby uzyskać zdalny dostęp do capanalizy, należy otworzyć port 9877 na maszynie wirtualnej, dodając nową regułę zabezpieczeń przychodzących. Aby uzyskać więcej informacji na temat tworzenia reguł w sieciowych grupach zabezpieczeń, zobacz [Tworzenie reguł w istniejącej sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-security-rule). Po pomyślnym dodaniu reguły powinieneś mieć dostęp do CapAnalysis z`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Uruchamianie sesji przechwytywania pakietów za pomocą funkcji Azure Network Watcher

Network Watcher umożliwia przechwytywanie pakietów w celu śledzenia ruchu do i z maszyny wirtualnej. Aby rozpocząć sesję przechwytywania pakietów, można zapoznać się z instrukcjami zawartymi w instrukcji [w zakładce Zarządzaj przechwytywaniem pakietów za pomocą funkcji Kontrola sieci.](network-watcher-packet-capture-manage-portal.md) Przechwytywanie pakietów mogą być przechowywane w obiekcie blob magazynu, aby uzyskać dostęp do CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Przekazywanie przechwytywania pakietów do capanalysis
Przechwytywanie pakietów zrobione przez obserwatora sieciowego można bezpośrednio przekazać za pomocą karty "Importuj z adresu URL" i udostępnić łącze do obiektu blob magazynu, w którym jest przechowywane przechwytywanie pakietów.

Podczas podawania łącza do CapAnalysis, upewnij się, że dołączyć token sygnatury dostępu Współdzielonego do adresu URL obiektu blob magazynu.  Aby to zrobić, przejdź do podpisu dostępu udostępnionego z konta magazynu, wyznacz dozwolone uprawnienia i naciśnij przycisk Generuj sygnaturę dostępu Współdzielonego, aby utworzyć token. Następnie można dołączyć token sygnatury dostępu Współdzielonego do adresu URL obiektu blob magazynu przechwytywania pakietów.

Wynikowy adres URL będzie wyglądał mniej więcej tak, jak następujący adres URL:http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analizowanie przechwytywania pakietów

CapAnalysis oferuje różne opcje wizualizacji przechwytywania pakietów, z których każda zapewnia analizę z innej perspektywy. Dzięki tym wizualnym podsumowaniom możesz zrozumieć trendy ruchu sieciowego i szybko dostrzec wszelkie nietypowe działania. Kilka z tych funkcji znajduje się na poniższej liście:

1. Tabele przepływu

    Ta tabela zawiera listę przepływów w danych pakietu, sygnaturę czasową skojarzoną z przepływami i różne protokoły skojarzone z przepływem, a także źródłowy i docelowy adres IP.

    ![strona przepływu kapanalizy][5]

1. Omówienie protokołu

    To okienko umożliwia szybkie wyświetlanie dystrybucji ruchu sieciowego za pośrednictwem różnych protokołów i regionów geograficznych.

    ![Omówienie protokołu capanalysis][6]

1. Statystyki

    To okienko umożliwia wyświetlanie statystyk ruchu sieciowego — bajtów wysyłanych i odbieranych z adresów IP źródłowych i docelowych, przepływów dla każdego źródłowych i docelowych adresów IP, protokołu używanego dla różnych przepływów oraz czasu trwania przepływów.

    ![statystyki kapanalizy][7]

1. Mapa geograficzna

    To okienko zapewnia widok mapy ruchu sieciowego z kolorami skalowania do natężenia ruchu z każdego kraju/regionu. Możesz wybrać wyróżnione kraje/regiony, aby wyświetlić dodatkowe statystyki przepływu, takie jak odsetek danych wysyłanych i odbieranych od ip w tym kraju/regionie.

    ![mapa geograficzna][8]

1. Filtry

    CapAnalysis udostępnia zestaw filtrów do szybkiej analizy określonych pakietów. Na przykład można filtrować dane według protokołu, aby uzyskać szczegółowe informacje na temat tego podzbioru ruchu.

    ![filtry][11]

    Odwiedź, [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) aby dowiedzieć się więcej o wszystkich możliwościach CapAnalysis.

## <a name="conclusion"></a>Podsumowanie

Funkcja przechwytywania pakietów przez Obserwatora Sieci umożliwia przechwytywanie danych niezbędnych do przeprowadzenia kryminalistyki sieciowej i lepszego zrozumienia ruchu sieciowego. W tym scenariuszu pokazaliśmy, jak przechwytywanie pakietów z funkcji Kontrola sieci może być łatwo zintegrowane z narzędziami wizualizacji typu open source. Korzystając z narzędzi typu open source, takich jak CapAnalysis, do wizualizacji przechwytuje pakiety, można przeprowadzić głęboką inspekcję pakietów i szybko zidentyfikować trendy w ruchu sieciowym.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dziennikach przepływu nsg, odwiedź [dzienniki przepływu nsg](network-watcher-nsg-flow-logging-overview.md)

Dowiedz się, jak wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi Power BI, odwiedzając [wizualizuj dzienniki przepływów sieciowych sieciowych za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
