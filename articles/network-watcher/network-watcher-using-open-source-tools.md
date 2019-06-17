---
title: Wizualizowanie wzorców ruchu sieciowego za pomocą usługi Azure Network Watcher i narzędzi typu open source | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu jak za pomocą usługi Network Watcher przechwytywania pakietów Capanalysis wizualizowanie wzorców ruchu do i z maszyn wirtualnych.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3a0ae782d3fe97752ca8b9e786c3c2672f554277
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64936020"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Wizualizowanie wzorców ruchu sieciowego do i z maszyn wirtualnych przy użyciu narzędzi typu open source

Przechwytywanie pakietów zawierają dane sieciowe, które umożliwiają wykonywanie sieci, analiza śledcza i inspekcję pakietów. Istnieje wiele otwiera narzędzi źródła, które umożliwia analizowanie przechwytywania pakietów w celu uzyskania szczegółowych informacji o sieci. Jednego takiego narzędzia jest CapAnalysis, narzędzie do wizualizacji przechwytywania pakietów open source. Wizualizowanie danych przechwytywania pakietów jest to wartościowy sposób szybkiego uzyskania szczegółowych informacji dotyczących wzorców i anomalii w ramach sieci. Wizualizacje zapewniają również metody udostępniania takich wgląd w sposób łatwy w użyciu.

Azure Network Watcher oferuje możliwość przechwytywania danych, co umożliwia testowanie przechwytywania pakietów w sieci. Ten artykuł zawiera omówienie sposobu wizualizacji i Uzyskaj wgląd w pakiet przechwytuje CapAnalysis przy użyciu usługi Network Watcher.

## <a name="scenario"></a>Scenariusz

Będziesz mieć prostą aplikację wdrożone na maszynie Wirtualnej na platformie Azure mają być wizualizowanie jego ruchu sieciowego, można szybko zidentyfikować wzorce przepływ i wszelkie anomalie możliwe za pomocą narzędzi typu open source. Przy użyciu usługi Network Watcher można uzyskać przechwytywania pakietów środowiska sieci i przechowuje je bezpośrednio na swoim koncie magazynu. CapAnalysis można pozyskiwać przechwytywania pakietów, bezpośrednio z obiektu blob magazynu i jego zawartość wizualizacji.

![scenariusz][1]

## <a name="steps"></a>Kroki

### <a name="install-capanalysis"></a>Zainstaluj CapAnalysis

Aby zainstalować CapAnalysis na maszynie wirtualnej, mogą odwoływać się do oficjalnego podanych tutaj instrukcji https://www.capanalysis.net/ca/how-to-install-capanalysis.
W kolejności CapAnalysis zdalnego dostępu, należy otworzyć port 9877 na maszynie Wirtualnej przez dodanie nowej reguły zabezpieczeń dla ruchu przychodzącego. Aby uzyskać więcej informacji o tworzeniu reguł sieciowych grup zabezpieczeń, zapoznaj się [tworzenie reguł w istniejącej sieciowej grupie zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-security-rule). Gdy reguła została pomyślnie dodana, powinno być możliwe do uzyskania dostępu CapAnalysis z `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Usługa Azure Network Watcher, aby rozpocząć sesję przechwytywania pakietów

Usługa Network Watcher umożliwia przechwytywanie pakietów, aby śledzić ruch do i z maszyny wirtualnej. Możesz skorzystać z instrukcjami w [Przechwytywanie pakietów zarządzania w usłudze Network Watcher](network-watcher-packet-capture-manage-portal.md) można uruchomić sesji przechwytywania pakietów. Przechwytywanie pakietów mogą być przechowywane w CapAnalysis dostępu do magazynu obiektów blob.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Przekaż przechwytywania pakietów do CapAnalysis
Możesz bezpośrednio przekazać przechwytywania pakietów podjęte przez usługi network watcher za pomocą karty "Importuj z adresu URL", a także łącze do obiektu blob magazynu, gdzie są przechowywane przechwytywania pakietów.

Gdy udostępnianie linku do CapAnalysis, upewnij się, że Dołącz token sygnatury dostępu Współdzielonego do adresu URL obiektu blob magazynu.  Aby to zrobić, przejdź do sygnatury dostępu współdzielonego z konta magazynu, wyznaczyć dozwolone uprawnienia i naciśnij przycisk Generowanie sygnatury dostępu Współdzielonego, aby utworzyć token. Token sygnatury dostępu Współdzielonego można następnie dołącz do adresu URL obiektu blob magazynu przechwytywania pakietów.

Wynikowy adres URL będzie wyglądać podobnie do następującego adresu URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Przechwytywanie pakietów analizowanie

CapAnalysis oferuje różne opcje usługi przechwycenie pakietu każdej dostarczanie analizy z punktu widzenia różnych wizualizacji. Za pomocą tych wizualne podsumowania można zrozumieć trendy ruchu sieciowego i szybko wykryć wszelkie nietypowe działania związane z. Niektóre z tych funkcji są wyświetlane na poniższej liście:

1. Tabele przepływu

    Ta tabela zawiera listę przepływów danych pakietów, sygnaturę czasową, skojarzone z przepływami i różnych protokołów skojarzonych z przepływu, a także źródłowych i docelowych adresów IP.

    ![flow — strona capanalysis][5]

1. Omówienie protokołu

    W tym okienku można szybko zobaczyć rozkład ruchu sieciowego za pośrednictwem różnych protokołów i różnych lokalizacji geograficznych.

    ![Omówienie protokołu capanalysis][6]

1. Statystyki

    W tym okienku zezwala, aby wyświetlić statystyki ruchu sieciowego — bajtów wysłanych i odebranych z źródłowe i docelowe adresy IP, przepływy dla każdego z źródłowe i docelowe adresy IP, protokół używany w przypadku różnych przepływów i czas trwania przepływów.

    ![statystyki capanalysis][7]

1. Mapę geograficzną

    W tym okienku zapewnia widoku mapy, ruchu sieciowego, kolory, skalowanie do wielkości ruchu z każdego kraju/regionu. Możesz wybrać wyróżnione krajów/regionów, aby wyświetlić przepływ dodatkowe statystyki, takie jak część danych wysyłane i odbierane z adresów IP w danym kraju/regionie.

    ![Mapę geograficzną][8]

1. Filtry

    CapAnalysis zawiera zestaw filtrów szybka analiza określonych pakietów. Na przykład można filtrować dane według protokołu, aby uzyskać szczegółowe informacje na ten część ruchu.

    ![filtry][11]

    Odwiedź stronę [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) Aby dowiedzieć się więcej o możliwościach wszystkich CapAnalysis.

## <a name="conclusion"></a>Podsumowanie

Funkcja przechwytywania pakietów usługi Network Watcher umożliwia przechwytywanie danych wymaganych do przeprowadzenia sieci, analiza śledcza i lepiej zrozumieć ruchu sieciowego. W tym scenariuszu pokazaliśmy, jak przechwytywania pakietów z usługi Network Watcher można łatwo zintegrować z narzędziami open source wizualizacji. Za pomocą narzędzi typu open source, takich jak CapAnalysis wizualizacji przechwytywania pakietów, możesz głębokiej inspekcji pakietów i szybkie Określanie trendów w ramach ruchu sieciowego.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat dzienników przepływu sieciowych grup zabezpieczeń, odwiedź stronę [dzienników przepływów sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md)

Dowiedz się, jak wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń z usługą Power BI, odwiedzając [wizualizacji przepływów sieciowych grup zabezpieczeń dzienników za pomocą usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
