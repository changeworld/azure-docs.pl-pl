---
title: Konfigurowanie diagnostyki dla usług Azure Cloud Services i maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować diagnostykę dla debugowania Azure cloude usług i maszyn wirtualnych (VM) w programie Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 34c667b0a594682e4d099e7bff64bfdb336b850b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Ustaw diagnostyki dla usług Azure Cloud Services i maszyny wirtualne
Gdy konieczne jest rozwiązywanie problemów z usługą w chmurze platformy Azure lub maszyny wirtualnej, można użyć programu Visual Studio aby łatwiej skonfigurować diagnostyki Azure. Diagnostyka przechwytuje dane systemu i rejestrowanie danych na maszynach wirtualnych i wystąpień maszyn wirtualnych, które są uruchamiane usługi w chmurze. Dane diagnostyczne jest przekazywane do wybranego konta magazynu. Aby uzyskać więcej informacji o diagnostyce rejestrowania na platformie Azure, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

W tym artykule firma Microsoft opisano, jak użyć programu Visual Studio, aby włączyć i skonfigurować diagnostyki Azure przed i po wdrożeniu. Dowiedz się, jak skonfigurować diagnostyki na maszynach wirtualnych Azure, jak wybrać typy informacji diagnostycznych do zbierania i jak wyświetlić informacje po ich zebraniu.

Jedną z następujących opcji umożliwia konfigurowanie diagnostyki Azure:

* Zmień ustawienia diagnostyki w **konfiguracji diagnostyki** okno dialogowe w programie Visual Studio. Ustawienia są zapisywane w pliku o nazwie diagnostics.wadcfgx (w Azure SDK 2.4 lub starszej, plik jest nazywane diagnostics.wadcfg). Można również bezpośrednio zmodyfikować pliku konfiguracji. Jeśli ręcznie zaktualizować plik, konfiguracja zmiany zaczęły obowiązywać przy następnym wdrożeniem chmury usługi Azure lub uruchomić usługę w emulatorze.
* Użyj Eksplorator chmury lub Eksploratora serwera w programie Visual Studio, aby zmienić ustawienia diagnostyki dla usługi w chmurze lub maszynę wirtualną, która jest uruchomiona.

## <a name="azure-sdk-26-diagnostics-changes"></a>Zmiany diagnostyki 2.6 zestawu SDK usługi Azure
Następujące zmiany dotyczą Azure SDK w wersji 2.6 i nowszych projekty w programie Visual Studio:

* Lokalne emulatora obsługuje teraz diagnostyki. Oznacza to, że można zbierać dane diagnostyczne i upewnij się, aplikacja tworzy prawo śladów podczas tworzenia i testowania w programie Visual Studio. Parametry połączenia `UseDevelopmentStorage=true` powoduje włączenie funkcji zbierania danych diagnostycznych, po uruchomieniu projektu usługi w chmurze w programie Visual Studio przy użyciu emulatora magazynu Azure. Wszystkie dane diagnostyczne są zbierane w ramach konta magazynu programowanie magazynu.
* Parametry połączenia konta magazynu diagnostyki `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` są przechowywane w pliku konfiguracji (cscfg) usługi. W systemie Azure SDK 2.5 w pliku diagnostics.wadcfgx określono konto magazynu diagnostyki.

Parametry połączenia się zmieniło w pewnym sensie klucza w wersji 2.6 zestawu SDK platformy Azure i później i 2.4 zestawu SDK platformy Azure i starszych wersji:

* W 2.4 zestawu SDK platformy Azure i starszych ciąg połączenia jest używany jako środowisko uruchomieniowe przez wtyczkę diagnostyki można pobrać informacji o koncie magazynu przesyłania dzienników diagnostycznych.
* W Azure SDK w wersji 2.6 lub nowszej Visual Studio używa ciągu połączenia diagnostyki, aby skonfigurować rozszerzenie diagnostyki Azure o informacje o koncie magazynu odpowiednie podczas publikowania. Parametry połączenia służy do definiowania różnych kont magazynu dla konfiguracji innej usługi, które korzysta z programu Visual Studio podczas publikowania. Jednak ponieważ wtyczki Diagnostyka nie jest dostępna po 2.5 zestawu SDK platformy Azure, plik .cscfg samodzielnie nie można skonfigurować rozszerzenia diagnostyki. Należy skonfigurować rozszerzenia oddzielnie za pomocą takich narzędzi jak Visual Studio lub programu PowerShell.
* Aby uprościć proces konfigurowania rozszerzenia diagnostyki za pomocą programu PowerShell, dane wyjściowe pakietu z programu Visual Studio zawiera publicznej konfiguracji XML dla rozszerzenia diagnostyki dla każdej roli. Visual Studio będzie korzystać diagnostyki parametry połączenia do wypełniania informacji o koncie magazynu w konfiguracji publicznego. Pliki konfiguracji publicznego są tworzone w folderze rozszerzenia. Pliki konfiguracji publicznego Użyj wzorca nazewnictwa PaaSDiagnostics. &lt;nazwy roli\>. PubConfig.xml. Wszystkie wdrożenia oparte na środowisku PowerShell można używać tego wzorca do mapowania każdej konfiguracji do roli.
* [Portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) używa parametrów połączenia w pliku .cscfg dostępu do danych diagnostycznych. Dane są wyświetlane na **monitorowanie** kartę. Ciąg połączenia jest wymagane do ustawienia usługi, aby wyświetlić pełne dane monitorowania w portalu.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrowanie projektów Azure SDK w wersji 2.6 lub nowszego
Podczas migracji z 2.5 zestawu SDK platformy Azure do usługi Azure SDK w wersji 2.6 lub nowszej, i jeśli masz konto magazynu diagnostyki określone w pliku .wadcfgx, konta magazynu pozostaje w tym pliku. Aby wykorzystać elastyczność przy użyciu różnych kont magazynu dla konfiguracji innego magazynu, należy ręcznie dodać parametry połączenia do projektu. W przypadku migrowania projektu z Azure SDK 2.4 lub jego starszej wersji 2.6 zestawu SDK platformy Azure, Diagnostyka parametry połączenia zostaną zachowane. Pamiętaj jednak, zmiany w sposobie parametry połączenia są traktowane w wersji 2.6 zestawu SDK platformy Azure, opisane w poprzedniej sekcji.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak Visual Studio Określa konto magazynu diagnostyki
* Jeśli parametry połączenia diagnostyki jest określone w pliku .cscfg, Visual Studio używa go skonfigurować rozszerzenia diagnostyki podczas publikowania i generuje pliki XML konfiguracji publicznego podczas pakowania.
* Jeśli nie określono diagnostyki parametry połączenia w pliku .cscfg, Visual Studio powraca do przy użyciu konta magazynu, która została określona w pliku .wadcfgx, aby skonfigurować rozszerzenia diagnostyki do publikowania oraz do generowania publicznej konfiguracji XML pliki podczas pakowania.
* Diagnostyka parametry połączenia w pliku .cscfg mają pierwszeństwo przed konta magazynu w pliku .wadcfgx. Jeśli parametry połączenia diagnostyki jest określona w pliku .cscfg programu Visual Studio używa tego ciągu połączenia i ignoruje konta magazynu w .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Do czego służy pole wyboru "Aktualizuj parametry połączenia magazynu programowanie..."?
**Zaktualizować parametry połączenia magazynu programowanie dla diagnostyki i buforowanie z poświadczeniami konta magazynu Microsoft Azure podczas publikowania do systemu Microsoft Azure** pole wyboru jest to wygodny sposób na wszystkie magazyn programowanie aktualizacji Parametry połączenia z konta z kontem magazynu platformy Azure, który jest określany podczas publikowania.

Na przykład w przypadku wybrania tego pola wyboru i parametry połączenia diagnostyki Określa `UseDevelopmentStorage=true`podczas publikowania projektu na platformie Azure, programu Visual Studio automatycznie aktualizuje diagnostyki parametry połączenia z kontem magazynu, który określono w Kreator publikowania. Jednak jeśli konto magazynu rzeczywista określono jako parametry połączenia diagnostyki, tego konta zamiast niego jest używana.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Różnice funkcji diagnostyki w 2.4 zestawu SDK platformy Azure i wcześniejszych wersji programu vs. Zestaw Azure SDK, 2.5 lub nowszej
Jeśli uaktualniasz projektu z 2.4 zestawu SDK platformy Azure i wcześniej do Azure SDK 2.5 lub nowszej, należy pamiętać następujące różnice funkcji diagnostyki:

* **Konfiguracyjne interfejsy API są przestarzałe**. Programowe konfiguracji diagnostyki jest dostępny w 2.4 zestawu SDK platformy Azure i wcześniejszych, ale jest przestarzała w wersji 2.5 zestawu SDK platformy Azure i nowszych. Jeśli konfiguracji diagnostyki jest obecnie zdefiniowana w kodzie, należy ponownie skonfigurować te ustawienia od początku migrowanych projektu dla diagnostyki, aby kontynuować pracę. Plik konfiguracji diagnostyki Azure SDK 2.4 jest diagnostics.wadcfg. Plik konfiguracji diagnostyki dla 2.5 zestawu SDK platformy Azure i nowszych jest diagnostics.wadcfgx.
* **Diagnostyki dla aplikacji usługi w chmurze można skonfigurować tylko na poziomie roli**. W wersji 2.5 zestawu SDK platformy Azure i nowszych diagnostyki dla aplikacji usługi w chmurze nie można skonfigurować na poziomie wystąpienia.
* **Za każdym razem, gdy wdrażanie aplikacji, zaktualizowano konfigurację diagnostyki**. Może to powodować problemy z parzystością, jeśli zmiana konfiguracji diagnostyki z Eksploratora serwera w usłudze Visual Studio i wdrożenie aplikacji.
* **W wersji 2.5 zestawu SDK platformy Azure i nowszych, zrzuty awaryjne są konfigurowane w pliku konfiguracyjnym Diagnostyka, a nie w kodzie**. Jeśli masz zrzuty awaryjne skonfigurowane w kodzie, musisz ręcznie przetransferować konfiguracji z kodu do pliku konfiguracji. Zrzuty awaryjne nie są przenoszone podczas migracji do 2.6 zestawu SDK platformy Azure.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Włącz diagnostykę w projekty usługi w chmurze, przed ich wdrożeniem
W programie Visual Studio może zbierać dane diagnostyczne dla ról, które działają na platformie Azure, podczas uruchamiania usługi w emulatorze przed przystąpieniem do wdrożenia. Wszelkie zmiany wprowadzone w ustawieniach diagnostyki w programie Visual Studio są zapisywane w pliku konfiguracyjnym diagnostics.wadcfgx. Te ustawienia określają konta magazynu, w której jest zapisywany dane diagnostyczne, podczas wdrażania usługi w chmurze.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Aby włączyć diagnostykę w programie Visual Studio przed wdrożeniem

1. Menu skrótów dla roli, wybierz **właściwości**. W tej roli **właściwości** okno dialogowe, wybierz opcję **konfiguracji** kartę.
2. W **diagnostyki** sekcji, upewnij się, że **włączyć diagnostyki** pole wyboru jest zaznaczone.
   
    ![Dostęp do opcji Włącz diagnostyki](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Aby określić konto magazynu dla danych diagnostycznych, kliknij przycisk wielokropka (...).
   
    ![Określ konto magazynu do użycia](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. W **utworzyć parametry połączenia magazynu** oknie dialogowym Określ, czy chcesz się połączyć przy użyciu emulatora magazynu Azure subskrypcji platformy Azure, lub ręcznie wprowadzić poświadczenia.
   
    ![Okno dialogowe konta magazynu](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * W przypadku wybrania **emulatora magazynu Microsoft Azure**, ciąg połączenia jest równa `UseDevelopmentStorage=true`.
   * W przypadku wybrania **subskrypcji**, wybierz subskrypcję platformy Azure, którego chcesz używać i wprowadź nazwę konta. Aby zarządzać subskrypcjami platformy Azure, wybierz **Zarządzanie kontami**.
   * W przypadku wybrania **ręcznie wprowadzić poświadczenia**, wprowadź nazwę i klucza konta platformy Azure, którego chcesz używać.
5. Aby wyświetlić **konfiguracji diagnostyki** okno dialogowe, wybierz opcję **Konfiguruj**. Z wyjątkiem **ogólne** i **katalogów dziennika**, każda karta reprezentuje źródła danych diagnostycznych, które można zebrać. Wartość domyślna **ogólne** kartę oferuje następujące diagnostyki opcji zbierania danych: **tylko błędy**, **wszystkie informacje**, i **plan niestandardowy**. Wartość domyślna **tylko błędy** opcja używa minimalnej liczbie magazynu, ponieważ nie transferu, ostrzeżenia lub komunikaty śledzenia. **Wszystkie informacje** opcja przekazuje informacje najbardziej, używa najwięcej pamięci masowej i w związku z tym jest to najbardziej kosztownych opcja.
   
    ![Włącz diagnostyki Azure i konfiguracji](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Na przykład wybierz **plan niestandardowy** opcji, więc można dostosować zebranych danych.
7. W **przydział dysku w MB** pole, można ustawić ilości wykorzystanego miejsca można przydzielić na koncie magazynu dla danych diagnostycznych. Można zmienić lub zaakceptuj wartość domyślną.
8. Na każdej karcie danych diagnostycznych, które chcesz zebrać, wybierz **włączyć Transfer \<typ dziennika\>**  pole wyboru. Na przykład, jeśli chcesz zbierać Dzienniki aplikacji na **Dzienniki aplikacji** wybierz opcję **włączyć transferu Dzienniki aplikacji** pole wyboru. Ponadto określ inne informacje, które jest wymagane przez każdego typu danych diagnostycznych. Aby uzyskać informacje o konfiguracji dla każdej z kart, zobacz sekcję **konfigurowania źródeł danych diagnostycznych** dalszej części tego artykułu. 
9. Po włączeniu zbierania danych diagnostycznych chcesz wybrać **OK**.
10. Uruchom projekt usługi chmury Azure w programie Visual Studio w zwykły sposób. Jako za pomocą aplikacji wybraną konto magazynu Azure są zapisywane informacje dziennika, które są włączone.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Włącz diagnostykę na maszynach wirtualnych Azure
W programie Visual Studio może zbierać dane diagnostyczne maszyn wirtualnych platformy Azure.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Aby włączyć diagnostykę na maszynach wirtualnych Azure

1. W Eksploratorze serwera wybierz węzeł Azure, a następnie podłącz do subskrypcji platformy Azure, jeśli jeszcze nie zostało nawiązane.
2. Rozwiń węzeł **maszyn wirtualnych** węzła. Utwórz nową maszynę wirtualną lub wybierz istniejący węzeł.
3. W menu skrótów dla maszyny wirtualnej, którą chcesz wybrać **Konfiguruj**. Zostanie wyświetlone okno dialogowe konfiguracji maszyny wirtualnej.
   
    ![Skonfiguruj maszynę wirtualną platformy Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Jeśli to nie jest jeszcze zainstalowana, Dodaj rozszerzenie Diagnostyka agenta monitorowania firmy Microsoft. Dla tego rozszerzenia można zbierać dane diagnostyczne dla maszyny wirtualnej platformy Azure. W obszarze **zainstalowane rozszerzenia**w **Wybierz dostępne rozszerzenia** polu listy rozwijanej wybierz **Diagnostyka agenta monitorowania firmy Microsoft**.
   
    ![Zainstaluj rozszerzenie maszyny wirtualnej platformy Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Inne rozszerzenia diagnostyki są dostępne dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [rozszerzenia maszyny wirtualnej i funkcje systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Aby dodać rozszerzenie i wyświetl jego **konfiguracji diagnostyki** okno dialogowe, wybierz opcję **Dodaj**.
6. Aby określić konto magazynu, wybierz **Konfiguruj**, a następnie wybierz **OK**.
   
    Każda karta (z wyjątkiem **ogólne** i **katalogów dziennika**) reprezentuje źródła danych diagnostycznych, które można zebrać.
   
    ![Włącz diagnostyki Azure i konfiguracji](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Karta domyślne **ogólne**, udostępnia następujące opcje zbierania danych diagnostycznych: **tylko błędy**, **wszystkie informacje**, i **plan niestandardowy**. Opcja domyślna **tylko błędy**, ma co najmniej ilość miejsca w magazynie, ponieważ nie transferu, ostrzeżenia lub komunikaty śledzenia. **Wszystkie informacje** opcja przekazuje informacje najbardziej i w związku z tym jest opcją najdroższych pod względem pamięci masowej.
7. Na przykład wybierz **plan niestandardowy** opcja tak dostosować dane zbierane.
8. **Przydział dysku w MB** pole określa ilość miejsca, które mają zostać przydzielone na koncie magazynu dla danych diagnostycznych. Można zmienić wartość domyślną, jeśli chcesz.
9. Na każdej karcie mają być zbierane dane diagnostyczne, wybierz jej **włączyć Transfer \<typ dziennika\>**  pole wyboru.
   
    Na przykład, jeśli chcesz zbierać Dzienniki aplikacji, wybierz opcję **włączyć transferu Dzienniki aplikacji** pole wyboru na **Dzienniki aplikacji** kartę. Ponadto określ wszelkie inne informacje, które są wymagane dla każdego typu danych diagnostycznych. Aby uzyskać informacje o konfiguracji dla każdej z kart, zobacz sekcję **konfigurowania źródeł danych diagnostycznych** dalszej części tego artykułu.
10. Po włączeniu kolekcji wszystkich danych diagnostycznych, które mają wybierz **OK**.
11. Zapisz zaktualizowany projekt.
    
    Komunikat w **dziennik aktywności programu Microsoft Azure** okna wskazuje, że maszyna wirtualna została zaktualizowana.

## <a name="set-up-diagnostics-data-sources"></a>Konfigurowanie źródeł danych diagnostycznych
Po włączeniu funkcji zbierania danych diagnostycznych, możesz dokładnie źródeł danych, które chcesz zebrać, i jakie informacje są zbierane. Kolejne sekcje opisują kart **konfiguracji diagnostyki** okno dialogowe i oznacza każdej opcji konfiguracji.

### <a name="application-logs"></a>Dzienniki aplikacji
Dzienniki aplikacji ma informacje diagnostyczne, który jest generowany przez aplikację sieci web. Jeśli chcesz przechwycić Dzienniki aplikacji, wybierz **włączyć transferu Dzienniki aplikacji** pole wyboru. Aby zwiększyć lub zmniejszyć interwał transferu Dzienniki aplikacji do konta magazynu, należy zmienić **okres Transfer (min)** wartość. Można także zmienić ilość informacji przechwycone w dzienniku przez ustawienie **poziom dziennika** wartość. Na przykład wybierz **pełne** Aby uzyskać więcej informacji, lub wybierz **krytyczny** do przechwytywania tylko błędy krytyczne. Jeśli masz diagnostyczne zależne od dostawcy, który emituje Dzienniki aplikacji, można przechwytywać dzienniki, dodając jego identyfikator GUID w **GUID dostawcy** pole.

  ![Dzienniki aplikacji](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Aby uzyskać więcej informacji o dziennikach aplikacji, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Dzienniki zdarzeń systemu Windows
Aby przechwycić dzienniki zdarzeń systemu Windows, zaznacz **włączyć transferu dzienniki zdarzeń systemu Windows** pole wyboru. Aby zwiększyć lub zmniejszyć interwał przekazywania dzienników zdarzeń do konta magazynu, należy zmienić **okres Transfer (min)** wartość. Zaznacz pola wyboru dla typów zdarzeń, które mają być śledzone.

![Dzienniki zdarzeń](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Jeśli używasz Azure SDK Version 2.6 lub nowszej i chcesz określić niestandardowe źródło danych, wprowadź go w **\<nazwa źródła danych\>** pola tekstowego, a następnie wybierz **Dodaj**. Źródło danych jest dodawane do pliku diagnostics.cfcfg.

Jeśli używasz 2.5 zestawu SDK platformy Azure i chcesz określić niestandardowe źródło danych, należy dodać go do `WindowsEventLog` sekcji diagnostics.wadcfgx plików tak samo, jak w poniższym przykładzie:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Liczniki wydajności
Informacje o liczniku wydajności może pomóc w Znajdź wąskich gardeł systemu i poprawienia wydajności systemu i aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie i używanie liczników wydajności w aplikacji Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Aby przechwycić liczniki wydajności, wybierz **włączyć transferu liczniki wydajności** pole wyboru. Aby zwiększyć lub zmniejszyć interwał przekazywania dzienników zdarzeń do konta magazynu, należy zmienić **okres Transfer (min)** wartość. Zaznacz pola wyboru dla liczników wydajności, które mają być śledzone.

![Liczniki wydajności](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Aby śledzić licznika wydajności, które nie ma na liście, wprowadź licznika wydajności przy użyciu składni sugerowane. a następnie wybierz **Dodaj**. System operacyjny na maszynie wirtualnej Określa, które liczniki wydajności można śledzić. Aby uzyskać więcej informacji na temat składni, zobacz [Określ ścieżkę licznika](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Dzienniki infrastruktury
Dzienniki infrastruktury ma informacje dotyczące infrastruktury diagnostycznych platformy Azure, moduł RemoteAccess i RemoteForwarder modułu. Aby zbierać informacje dotyczące infrastruktury dzienników, wybierz **włączyć transferu dzienniki infrastruktury** pole wyboru. Aby zwiększyć lub zmniejszyć interwał przekazywania dzienników infrastruktury do konta magazynu, należy zmienić **okres Transfer (min)** wartość.

![Dzienniki infrastruktury diagnostyki](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Aby uzyskać więcej informacji, zobacz [zbierania danych rejestrowania przy użyciu diagnostyki Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Katalogi dziennika
Dane zbierane z katalogów dziennika dla żądań usług Internet Information Services (IIS), żądań zakończonych niepowodzeniem lub foldery, które można wybrać mają katalogów dziennika. Aby przechwycić dziennika katalogów, wybierz **włączyć transferu katalogów dziennika** pole wyboru. Aby zwiększyć lub zmniejszyć interwał przekazywania dzienników do konta magazynu, należy zmienić **okres Transfer (min)** wartość.

Zaznacz pola wyboru dzienników, które chcesz zebrać, takich jak **dzienniki programu IIS** i **nie powiodło się żądanie** dzienniki. Domyślne nazwy kontenera magazynu jest dostępny, ale można zmienić nazwy.

Można przechwycić dzienniki z dowolnego folderu. Określ ścieżkę w **dziennika z katalogu bezwzględną** , a następnie wybierz **Dodaj katalog**. Dzienniki są przechwytywane w określonych kontenerach.

![Katalogi dziennika](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Dzienniki zdarzeń systemu Windows
Jeśli używasz [śledzenia zdarzeń dla systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) i aby przechwytywać dzienniki zdarzeń systemu Windows, zaznacz **włączyć transferu dzienniki zdarzeń systemu Windows** pole wyboru. Aby zwiększyć lub zmniejszyć interwał przekazywania dzienników do konta magazynu, należy zmienić **okres Transfer (min)** wartość.

Zdarzenia są przechwytywane ze źródła zdarzeń i manifesty zdarzeń, które określisz. Aby określić źródło zdarzenia w **źródła zdarzeń** sekcji, wprowadź nazwę, a następnie wybierz **Dodaj źródło zdarzeń**. Analogicznie, można określić manifest zdarzeń w **manifesty zdarzeń** , a następnie wybierz **Dodaj Manifest zdarzeń**.

![Dzienniki zdarzeń systemu Windows](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

ETW framework jest obsługiwana w programie ASP.NET za pośrednictwem klas w [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) przestrzeni nazw. Przestrzeń nazw Microsoft.WindowsAzure.Diagnostics, która dziedziczy i rozszerza standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) klas, umożliwia korzystanie z [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) jako rejestrowania Struktura w środowisku platformy Azure. Aby uzyskać więcej informacji, zobacz [przejąć kontrolę nad rejestrowania i śledzenia w systemie Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) i [Włącz diagnostykę w usług Azure Cloud Services i maszyn wirtualnych](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Zrzuty awaryjne
Aby zebrać informacje o po awarii wystąpienia roli, wybierz **włączyć przesyłanie awarii zrzuty** pole wyboru. (Ponieważ ASP.NET obsługuje większość wyjątki, jest zazwyczaj przydatne tylko w przypadku roli proces roboczy). Aby zwiększyć lub zmniejszyć część poświęcona zrzuty awaryjne miejsca do magazynowania, należy zmienić **przydziału katalogu (%)** wartość. Można zmienić kontenera magazynu, w którym są przechowywane zrzuty awaryjne i wybierz, czy mają być przechwytywane **pełne** lub **Mini** zrzutu.

Procesy aktualnie śledzone są wyświetlane w następnym zrzut ekranu. Zaznacz pola wyboru dla procesów, które mają być przechwytywane. Aby dodać inny proces na liście, wprowadź nazwę procesu, a następnie wybierz **proces dodawania**.

![Zrzuty awaryjne](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Aby uzyskać więcej informacji, zobacz [przejąć kontrolę nad rejestrowania i śledzenia w systemie Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) i [Microsoft Azure Diagnostics część 4: składniki Rejestrowanie niestandardowe i zmiany 1.3 diagnostyki Azure](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Widok danych diagnostycznych
Po zostały zebrane dane diagnostyczne do usługi w chmurze lub maszynę wirtualną, możesz je wyświetlić.

### <a name="to-view-cloud-service-diagnostics-data"></a>Aby wyświetlić dane diagnostyczne usługi w chmurze
1. Wdrażanie usługi w chmurze w zwykły sposób, a następnie ją uruchom.
2. Mogą wyświetlać dane diagnostyczne w raporcie generuje Visual Studio lub w tabelach na koncie magazynu. Aby wyświetlić dane w raporcie, Otwórz Eksplorator chmury lub Eksploratora serwera, otwórz menu skrótów węzła dla roli, a następnie wybierz **widoku danych diagnostycznych**.
   
    ![Widok danych diagnostycznych](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Zostanie wyświetlony raport pokazujący dostępne dane.
   
    ![Microsoft Azure Diagnostics raportu w programie Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Najnowszych danych nie jest widoczne, może być konieczne poczekaj, aż okres transferu.
   
    Aby natychmiast zaktualizować danych, wybierz **Odśwież** łącza. Aby dane aktualizowane automatycznie, wybierz interwał w **automatycznego odświeżania** pole listy rozwijanej. Aby wyeksportować błąd danych, wybierz **Eksportuj do pliku CSV** przycisk, aby utworzyć plik wartości rozdzielanych przecinkami, które można otworzyć w arkuszu programu Excel.
   
    W Eksploratorze chmury lub Eksploratora serwera Otwórz konta magazynu, który został skojarzony z wdrożenia.
3. Otwórz w tabelach diagnostyki w podglądzie tabeli, a następnie przejrzyj zebranych danych. Dzienniki programu IIS i dzienników niestandardowych można otworzyć kontenera obiektów blob. W poniższej tabeli wymieniono tabele lub kontenerów obiektów blob, które zawierają dane dla różnych plikach dziennika. Oprócz danych dla tego pliku dziennika, wpisy tabeli zawierają **EventTickCount**, **DeploymentId**, **roli**, i **RoleInstance** , ułatwiające identyfikowanie, które maszyny wirtualnej i roli wygenerowanych danych i kiedy. 
   
   | Dane diagnostyczne | Opis | Lokalizacja |
   | --- | --- | --- |
   | Dzienniki aplikacji |Dzienniki generowane przez wywołanie metody kodu **System.Diagnostics.Trace** klasy. |WADLogsTable |
   | Dzienniki zdarzeń |Dane z dzienników zdarzeń systemu Windows na maszynach wirtualnych. System Windows przechowuje informacje w tych dziennikach, ale aplikacje i usługi również użyć dzienniki, aby włączyć raportowanie błędów lub rejestrowania informacji. |WADWindowsEventLogsTable |
   | Liczniki wydajności |Można zbierać dane w dowolnym licznika wydajności, które jest dostępne na maszynie wirtualnej. System operacyjny udostępnia liczników wydajności, które zawierają wiele statystyk, takich jak pamięć użycia i czasu procesora. |WADPerformanceCountersTable |
   | Dzienniki infrastruktury |Dzienniki, które są generowane na podstawie samej infrastruktury diagnostyki. |WADDiagnosticInfrastructureLogsTable |
   | Dzienniki usług IIS |Dzienniki służące do rejestrowania żądań sieci web. Usługi w chmurze pobiera znaczną ilość ruchu, te dzienniki może być długi. Należy dobrze do gromadzenia i przechowywania tych danych tylko wtedy, gdy zajdzie taka potrzeba. |Można znaleźć dzienniki żądanie nie powiodło się w kontenerze obiektów blob w obszarze wad — usługi iis-failedreqlogs w ścieżce dla tego wdrożenia, roli i wystąpienia. Można znaleźć pełną dzienniki w obszarze logfiles-wad — usługi iis. Dla każdego pliku wpisów w tabeli WADDirectories. |
   | Zrzuty awaryjne |Udostępnia binarny obraz procesu usługi chmury (zazwyczaj roli proces roboczy). |kontener obiektów blob wad zgniatania zrzutów |
   | Pliki dziennika niestandardowego |Dzienniki danych, który zostanie wstępnie zdefiniowane. |Można określić w kodzie lokalizację plików dziennika niestandardowe na koncie magazynu. Na przykład można określić kontenera niestandardowych obiektów blob. |
4. Jeśli zostały obcięte danych dowolnego typu, możesz zwiększyć bufor dla danych typu lub skrócić interwał między transferów danych z maszyny wirtualnej do konta magazynu.
5. (Opcjonalnie) Przeczyść dane z konta magazynu od czasu do czasu na obniżenie ogólnych kosztów magazynowania.
6. Po wykonaniu pełne wdrożenie plik diagnostics.cscfg (.wadcfgx dla 2.5 zestawu SDK platformy Azure) został zaktualizowany na platformie Azure i usługi w chmurze przejmuje wszystkie zmiany w konfiguracji diagnostyki. Jeśli zamiast tego zaktualizować istniejące wdrożenie, pliku nie zostały zaktualizowane na platformie Azure. Ustawienia diagnostyki, można zmienić, wykonując kroki opisane w następnej sekcji. Aby uzyskać więcej informacji na temat wykonywania pełne wdrożenie i aktualizowania istniejącego wdrożenia, zobacz [Kreator publikowania aplikacji Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Aby wyświetlić dane diagnostyczne maszyny wirtualnej
1. Menu skrótów dla maszyny wirtualnej, wybierz **dane diagnostyczne widoku**.
   
    ![Widok danych diagnostycznych w maszynie wirtualnej platformy Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    **Podsumowanie diagnostyki** zostanie wyświetlone okno dialogowe.
   
    ![Podsumowanie diagnostyki maszyny wirtualnej platformy Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Najnowszych danych nie jest widoczne, może być konieczne poczekaj, aż okres transferu.
   
    Aby natychmiast zaktualizować danych, wybierz **Odśwież** łącza. Aby dane aktualizowane automatycznie, wybierz interwał w **automatycznego odświeżania** pole listy rozwijanej. Aby wyeksportować błąd danych, wybierz **Eksportuj do pliku CSV** przycisk, aby utworzyć plik wartości rozdzielanych przecinkami, które można otworzyć w arkuszu programu Excel.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Konfigurowanie diagnostyki usługi w chmurze po wdrożeniu
Jeśli w przypadku badania problemów z usługą w chmurze, która jest już uruchomiona, można zbierać dane, których nie podano zanim pierwotnie wdrożyć rolę. W takim przypadku można uruchomić zbierania danych, zmieniając ustawienia w Eksploratorze serwera. Możesz skonfigurować diagnostykę dla jednego wystąpienia lub dla wszystkich wystąpień w roli, w zależności od tego, czy możesz otworzyć **konfiguracji diagnostyki** okno dialogowe z menu skrótów dla wystąpienia lub roli. Jeśli skonfigurujesz węzła roli wszelkie wprowadzane zmiany dotyczą wszystkich wystąpień. Jeśli skonfigurujesz węzła wystąpienia wszelkie wprowadzane zmiany dotyczą tylko tego wystąpienia.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Aby skonfigurować diagnostykę dla uruchomionej usługi w chmurze
1. W Eksploratorze serwera rozwiń **usługi w chmurze** węzeł, a następnie rozwiń listę węzłów można zlokalizować roli wystąpienia (i/lub), który chcesz zbadać.
   
    ![Skonfiguruj diagnostyki](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. Menu skrótów dla wystąpienia węzeł lub ról, wybierz **ustawień diagnostycznych aktualizacji**, a następnie wybierz ustawień diagnostycznych, które mają być zbierane.
   
    Aby uzyskać informacje o ustawieniach konfiguracji, zobacz sekcję **konfigurowania źródeł danych diagnostycznych** w tym artykule. Aby uzyskać informacje o sposobie wyświetlania danych diagnostycznych, zobacz sekcję **wyświetlić dane diagnostyczne** w tym artykule.
   
    Jeśli zmienisz zbierania danych w Eksploratorze serwera zmiany obowiązują do momentu pełnego ponownego wdrażania usługi w chmurze. Jeśli użytkownik korzysta z domyślnych ustawień publikowania, zmiany nie zostaną zastąpione. Domyślne ustawienie publikowania jest, aby zaktualizować istniejące wdrożenie, a nie do pełnego ponownego wdrażania. Aby upewnić się, że ustawienia wyczyść w czasie wdrażania, przejdź do **Zaawansowane ustawienia** karcie Kreatora publikowania, a następnie wyczyść **aktualizację wdrożenia** pole wyboru. Podczas ponownego wdrażania z tego pole wyboru jest wyczyszczone, ustawienia przywrócić w pliku .wadcfgx (lub .wadcfg) zgodnie z ustaleniami za pośrednictwem **właściwości** edytora dla roli. Po zaktualizowaniu wdrożenia usługi Azure śledzi wcześniejszych ustawień.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Rozwiązywanie problemów dotyczących usługi w chmurze Azure
Jeśli masz problemy z projektami usługi chmury, takich jak rolę, która pobiera zablokowane w stanie "zajęty" często odtwarzana, lub zgłasza wyjątek, to wewnętrzny błąd serwera, istnieją narzędzia i techniki, które można użyć, aby zdiagnozować i rozwiązać problem. Przykładem typowe problemy i rozwiązania oraz omówienie pojęć i narzędzia, których można użyć, aby zdiagnozować i rozwiązać te błędy, zobacz [Azure PaaS obliczeniowe dane diagnostyczne](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Pytania i odpowiedzi
**Co to jest rozmiar buforu, a rozmiar, jaki ma być?**

W każdym wystąpieniu maszyny wirtualnej przydziały ograniczyć ilość danych diagnostycznych może być przechowywany w lokalnym systemie plików. Ponadto można określić rozmiar buforu dla każdego typu danych diagnostycznych, która jest dostępna. Ten rozmiar buforu działa jak poszczególnych przydziału dla tego typu danych. Aby określić całkowitej ilości i ilość pamięci, która pozostaje, zobacz dolnej części okna dialogowego z typem danych diagnostycznych. Jeśli określisz buforów większy lub więcej typów danych będzie osiągają całkowitej ilości. Ogólny limit przydziału można zmienić, modyfikując diagnostics.wadcfg lub .wadcfgx w pliku konfiguracyjnym. Dane diagnostyczne są przechowywane w systemie plików jako dane aplikacji. Jeśli aplikacja korzysta z dużą ilość miejsca na dysku, nie należy zwiększyć ogólną przydział diagnostyki.

**Co to jest okres transfer i jak długo ma być?**

Okres transfer to czas, który przechwytuje upłynie między danymi. Po zaakceptowaniu transferu danych jest przenoszona z lokalnego systemu plików na maszynę wirtualną do tabel na koncie magazynu. Jeśli ilość zbieranych danych przekroczy limit przydziału przed upływem okresu transferu, starsze dane zostaną odrzucone. Są utraty danych, ponieważ danych przekracza rozmiar buforu lub całkowitej ilości, można zmniejszyć okres transferu.

**Strefy czasowej są sygnatury czasowe w?**

Sygnatury czasowe znajdują się w lokalnej strefie czasowej centrum danych, który jest hostem usługi w chmurze. Są używane następujące trzech czasu sygnatury kolumn w tabelach dziennika:

* **PreciseTimeStamp**: sygnatura czasowa zdarzenia ETW. Oznacza to, że czas zdarzenie jest rejestrowane przez klienta.
* **Sygnatura CZASOWA**: wartość **PreciseTimeStamp** zaokrąglone w dół granic częstotliwość przekazywania. Na przykład częstotliwość przekazywania jest 5 minut i zdarzenia, czas 00:17:12, sygnatura CZASOWA to 00:15:00.
* **Sygnatura czasowa**: sygnaturę czasową, jaką w tabeli platformy Azure została utworzona jednostka.

**Jak zarządzać kosztami, podczas zbierania informacji diagnostycznych?**

Ustawienia domyślne (**poziom dziennika** ustawioną **błąd**, i **okres transferu** ustawioną **1 minuta**) mają na celu ograniczenie do minimum kosztów. Koszty obliczeń zwiększyć zbierać więcej danych diagnostycznych lub Zmniejsz okres transferu. Nie należy zbierać więcej danych niż wymagane i nie zapomnij Wyłącz funkcję zbierania danych, gdy nie są już potrzebne. Należy zawsze włączyć ją ponownie, nawet w czasie wykonywania, zgodnie z opisem we wcześniejszej części tego artykułu.

**Jak zebrać dzienniki nie powiodło się żądanie za pomocą programu IIS?**

Domyślnie usługi IIS nie zbierania dzienników żądanie nie powiodło się. Usługi IIS można skonfigurować do zbierania dzienników żądanie nie powiodło się podczas edycji pliku web.config dla roli sieci web.

**Nie są zwracane informacje o śledzeniu z metody RoleEntryPoint, takie jak OnStart. Co jest nie tak?**

Metody **RoleEntryPoint** są nazywane w kontekście WAIISHost.exe nie w usługach IIS. Informacje o konfiguracji w pliku web.config, które normalnie nie mają zastosowanie umożliwia śledzenie. Aby rozwiązać ten problem, Dodaj plik .config do projektu roli sieci web i nazwa pliku do dopasowania zestawu wyjściowego, który zawiera **RoleEntryPoint** kodu. W projekcie roli sieci web domyślną nazwę pliku .config powinna być WAIISHost.exe.config. Dodaj następujące wiersze do tego pliku:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

W **właściwości** ustaw **Kopiuj do katalogu wyjściowego** właściwości **skopiuj zawsze**.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o diagnostyce rejestrowania na platformie Azure, zobacz [Włącz diagnostykę w usług Azure Cloud Services i maszyn wirtualnych](cloud-services/cloud-services-dotnet-diagnostics.md) i [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

