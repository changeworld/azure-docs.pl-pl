---
title: Wizualizuj wzorce ruchu w sieci z obserwatora sieciowego Azure i narzędzi typu open source | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu za pomocą przechwytywania pakietów obserwatora sieciowego Capanalysis do wizualizacji wzorce ruchu do i z maszyn wirtualnych.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 61abda6053fe743e294f309df3a6e1041052ec6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864014"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Wizualizuj wzorce ruchu sieciowego do i z maszyn wirtualnych za pomocą narzędzi typu open source

Przechwytywanie pakietów zawierają dane sieci, które umożliwiają wykonywanie sieci danych dowodowych i inspekcję pakietów. Istnieje wiele otwiera źródła narzędzia, których można użyć do analizowania przechwytywania pakietów, aby uzyskać szczegółowe informacje o sieci. Jednym z takich narzędzi jest CapAnalysis, narzędzie do wizualizacji przechwytywania pakietów typu open source. Wizualizacja danych przechwytywania pakietów jest przydatna sposobem szybkiego uzyskania szczegółowych informacji na wzorce i anomalii w sieci. Wizualizacje również oferują możliwość udostępniania tych wgląd w sposób łatwo dostępne.

Monitor sieci platformy Azure zapewnia możliwość przechwytywania tego cennych danych, umożliwiając wykonać przechwytywania pakietów w sieci. W tym artykule zawiera przewodnik wizualizacji i uzyskaj informacje z pakietu znajdują się za pomocą CapAnalysis z obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Masz prostą aplikację sieci web wdrożone na maszynie Wirtualnej na platformie Azure mają być używane do wizualizacji jego ruch sieciowy do szybkiego identyfikowania wzorców przepływu i wszystkie możliwe anomalii narzędzi typu open source. Z obserwatora sieciowego można uzyskać przechwytywania pakietów, środowiska sieci i zapisz go bezpośrednio na koncie magazynu. CapAnalysis można pozyskiwania przechwytywania pakietów, bezpośrednio z obiektu blob magazynu i wizualizacji jego zawartość.

![scenariusz][1]

## <a name="steps"></a>Kroki

### <a name="install-capanalysis"></a>Zainstaluj CapAnalysis

Aby CapAnalysis należy zainstalować na maszynie wirtualnej, można skorzystać z oficjalnego instrukcje w tym miejscu https://www.capanalysis.net/ca/how-to-install-capanalysis.
W kolejności CapAnalysis zdalnego dostępu, należy otworzyć port 9877 na maszynie Wirtualnej przez dodanie nowej reguły zabezpieczeń dla ruchu przychodzącego. Aby uzyskać więcej informacji o tworzeniu reguł grup zabezpieczeń sieci, zapoznaj się [tworzenia reguł w istniejącej grupy NSG](../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg). Po pomyślnym dodaniu reguły powinien mieć możliwość dostępu CapAnalysis z`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Użyj obserwatora sieciowego Azure, aby rozpocząć sesję przechwytywania pakietów

Obserwatora sieciowego umożliwia przechwytywanie pakietów, które śledzą ruch do i z maszyny wirtualnej. Można odwołać się do instrukcji w [Przechwytywanie pakietów zarządzania z obserwatora sieciowego](network-watcher-packet-capture-manage-portal.md) aby rozpocząć sesję przechwytywania pakietów. To Przechwytywanie pakietów mogą być przechowywane w obiektu blob magazynu, do których uzyskują dostęp CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Przekaż przechwytywania pakietów do CapAnalysis
Możesz przekazać bezpośrednio przechwytywania pakietów, wykonywaną przez obserwatora sieciowego przy użyciu karty "Z adresu URL importowania" i udostępnienie łącza do obiektu blob magazynu, w którym przechowywana jest przechwytywania pakietów.

Podczas dostarczania łącze do CapAnalysis, upewnij się dołączyć tokenu sygnatury dostępu Współdzielonego do adresu URL obiektu blob magazynu.  Aby to zrobić, przejdź do współużytkowanego podpisu dostępu z konta magazynu, wyznaczyć dozwolone uprawnienia i kliknij przycisk Generuj SAS do utworzenia tokenu. Ten token SAS może następnie dołącz do adresu URL pakietów przechwytywania magazynu obiektów blob.

Adres URL wynikowy będzie wyglądać mniej więcej tak: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Przechwytywanie analizowania pakietów

CapAnalysis oferuje różne opcje do wizualizacji z przechwytywania pakietów, każda udostępnienie analiza z różnych perspektyw. Te podsumowania visual możesz zrozumieć trendy ruchu sieciowego i szybko dodatkowe wszelkie nietypowe działania związane z. W poniższej liście przedstawiono niektóre z tych funkcji:

1. Tabele przepływu

    W tej tabeli przedstawiono listę przepływów danych pakietu, sygnatura czasowa skojarzona z przepływami i różnych protokołów skojarzonego z przepływem, a także źródłowego i docelowego adresu IP.

    ![Strona przepływu capanalysis][5]

1. Omówienie protokołu

    W tym okienku pozwala szybko sprawdzić rozkład ruchu sieciowego za pośrednictwem różnych protokołów i lokalizacji geograficznych.

    ![Omówienie protokołu capanalysis][6]

1. Statystyki

    W tym okienku pozwala, aby wyświetlić statystyki ruchu w sieci — bajtów wysłanych i odebranych z źródłowe i docelowe adresy IP, przepływów dla każdego z źródłowe i docelowe adresy IP, protokół używany dla różnych przepływów i czas trwania przepływów.

    ![statystyki capanalysis][7]

1. geomap

    W tym okienku zawiera widoku mapy ruchu sieciowego, kolorów, skalowanie do wielkości ruchu każdego z krajów. Możesz wybrać wyróżnione krajach, aby wyświetlić dodatkowe przepływu statystyk, takich jak część danych wysłanych i odebranych z adresów IP, w tym kraju.

    ![geomap][8]

1. Filtry

    CapAnalysis zawiera zestaw filtrów do szybkiego analizy określonych pakietów. Na przykład można filtrować dane protokołem Uzyskaj określone informacje w tym podzbiór ruchu.

    ![filtry][11]

    Odwiedź stronę [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) Aby dowiedzieć się więcej o możliwościach wszystkich CapAnalysis.

## <a name="conclusion"></a>Podsumowanie

Funkcja przechwytywania pakietów obserwatora sieciowego umożliwia przechwytywanie danych niezbędnych do wykonywania dowodowe sieci oraz lepsze zrozumienie ruchu sieciowego. W tym scenariuszu firma Microsoft pokazano, jak przechwytywanie pakietów z obserwatora sieciowego łatwo można zintegrować z narzędzi wizualizacji typu open source. Przy użyciu narzędzi typu open source, takich jak CapAnalysis do wizualizacji przechwytywania pakietów, można przeprowadzić inspekcję pakietów i szybkie Określanie trendów w ramach ruchu sieciowego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dziennikach przepływu NSG, odwiedź stronę [dzienniki przepływu NSG](network-watcher-nsg-flow-logging-overview.md)

Dowiedz się, jak wizualizacji NSG dzienników przepływu z usługi Power BI, odwiedzając [wizualizacji NSG przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
