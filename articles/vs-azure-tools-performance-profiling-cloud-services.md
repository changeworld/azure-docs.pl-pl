---
title: Testowanie wydajności usługi w chmurze | Dokumentacja firmy Microsoft
description: Testowanie wydajności usługi w chmurze przy użyciu profilera Visual Studio
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 74ffd70bdb204cee75b86a1e922ac67873a88758
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testowanie wydajności usługi w chmurze
## <a name="overview"></a>Przegląd
Należy przetestować wydajność usługi w chmurze, w następujący sposób:

* Użyj diagnostyki Azure zbierać informacje dotyczące żądania i połączenia i przejrzyj statystyki witryny, które pokazują, jak usługa wykonuje z punktu widzenia klienta. Aby rozpocząć, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i maszyn wirtualnych](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Użyj profilera Visual Studio, aby pobrać dokładnych analiz obliczeniową aspektów działania usługi. Zgodnie z opisem w tym temacie, można użyć profilera do mierzenia wydajności, ponieważ usługa działa na platformie Azure. Aby uzyskać informacje o sposobie używania profilera do pomiaru wydajności, ponieważ usługa działa lokalnie w emulatorze obliczeń, zobacz [testowanie wydajności usługi Azure Cloud Service lokalnie w obliczeniowe emulatora przy użyciu profilera Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Wybieranie testowania — metoda
### <a name="use-azure-diagnostics-to-collect"></a>Umożliwia zbieranie diagnostyki Azure:
* Statystyka stron sieci web lub usługi, takie jak żądania i połączenia.
* Statystyka role, takie jak częstotliwość roli zostanie ponownie uruchomiony.
* Ogólne informacje na temat użycia pamięci, takie jak procent czasu pobierającej przez moduł garbage collector lub pamięć zestaw uruchomionej roli.

### <a name="use-the-visual-studio-profiler-to"></a>Użyj profilera Visual Studio, aby:
* Określ, które funkcje zajmuje najwięcej czasu.
* Zmierz czas, jaki zajmuje każdej części programu w praktyce znacznym.
* Umożliwiają porównanie wydajności szczegółowe raporty dotyczące dwóch wersji usługi.
* Przeanalizuj więcej szczegółów niż poziom alokacji pamięci poszczególnych alokacji pamięci.
* Analizy współbieżności problemów w kodzie wielowątkowych.

Gdy używasz profiler może zbierać dane po uruchomieniu usługi w chmurze lokalnie lub na platformie Azure.

### <a name="collect-profiling-data-locally-to"></a>Zbierz dane profilowania lokalnie do:
* Przetestuj wydajność część usługi w chmurze, takich jak wykonanie określonego procesu roboczego roli, która nie wymaga realistyczne symulowanym obciążeniem.
* Testowanie wydajności usługi w chmurze w izolacji w warunkach kontrolowanych.
* Przed wdrożeniem na platformie Azure, należy przetestować wydajność usługi w chmurze.
* Testowanie wydajności usługi w chmurze prywatnie, bez zakłócania istniejące wdrożenia.
* Testowanie wydajności usługi bez są naliczane opłaty za działające na platformie Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Zbieranie danych profilowania w Azure, aby:
* Testowanie wydajności usługi w chmurze pod obciążeniem symulowane lub rzeczywistej.
* Metoda Instrumentacji zbierania danych profilowania, ponieważ w tym temacie opisano później.
* W tym samym środowisku jako gdy usługa jest uruchamiana w środowisku produkcyjnym, należy przetestować wydajność usługi.

Zwykle symulować obciążenia usługi w chmurze testu w normalnych lub warunki obciążenia.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilowanie usługi w chmurze na platformie Azure
Po opublikowaniu usługi w chmurze w programie Visual Studio można profilu usługi i określ ustawienia profilowania, które zapewniają informacje, które mają. Sesja profilowania jest uruchomiony dla każdego wystąpienia roli. Aby uzyskać więcej informacji o sposobie publikowania usługi z programu Visual Studio, zobacz [publikowania dla usługi w chmurze platformy Azure w programie Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Aby dowiedzieć się więcej na temat profilowanie wydajności w programie Visual Studio, zobacz [profilowanie wydajności — przewodnik dla początkujących](https://msdn.microsoft.com/library/azure/ms182372.aspx) i [analizowanie wydajności aplikacji przy użyciu narzędzi profilowania](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Można włączyć funkcji IntelliTrace lub profilowania po opublikowaniu usługi w chmurze. Nie można włączyć jednocześnie.
> 
> 

### <a name="profiler-collection-methods"></a>Metoda zbierania danych profilera
Do profilowania, oparte na problemy z wydajnością, można użyć metody innej kolekcji:

* **Próbkowanie Procesora** — ta metoda służy do zbierania statystyk aplikacji, które są przydatne podczas początkowej analizy problemy dotyczące użycia procesora CPU. Próbkowanie Procesora to sugerowane metody uruchamiania większości dochodzenia wydajności. Brak niski wpływ na aplikacji, w której są profilowania podczas zbierania danych próbkowania procesora CPU.
* **Instrumentacja** — ta metoda służy do zbierania danych o chronometrażu, który jest przydatne do ukierunkowanej analizy i analizowania problemów z wydajnością wejścia/wyjścia. Każdy wpis, zakończenia i wywołanie funkcji funkcji w module metody Instrumentacji rejestruje podczas przebiegu profilowania. Ta metoda jest przydatne do zbierania czasu szczegółowe informacje o sekcji kodu i zrozumienie wpływu wejściowymi i wyjściowymi operacje na wydajność aplikacji. Ta metoda jest wyłączona na komputerze 32-bitowym systemie operacyjnym. Ta opcja jest dostępna tylko podczas uruchamiania usługi w chmurze na platformie Azure, nie lokalnie w emulatorze obliczeń.
* **Alokacja pamięci .NET** — ta metoda służy do zbierania danych alokacji pamięci .NET Framework za pomocą metoda profilowania próbkowania. Zebrane dane obejmuje liczby i rozmiaru przydzielonych obiektów.
* **Współbieżność** — ta metoda służy do zbierania danych kontencji zasobów i procesów i wątków danych wykonawczych, który jest przydatny podczas analizowania aplikacji wielowątkowych i wieloprocesowa. Metoda współbieżności zbiera dane dla każdego zdarzenia, że wykonanie bloki kodu, na przykład jeśli wątek oczekuje na zablokowany dostęp do zasobów aplikacji ma zostać zwolniony. Ta metoda jest przydatna do analizowania aplikacji wielowątkowych.
* Można również włączyć **profilowanie interakcji między warstwami**, który udostępnia dodatkowe informacje o czas wykonywania programu ADO.NET synchroniczne odwołuje się funkcje aplikacji wielowarstwowych, które komunikują się z co najmniej jedna baza danych. Możliwość zbierania danych o interakcji między warstwy z dowolnej z metod profilowania. Aby uzyskać więcej informacji na temat profilowanie interakcji między warstwami, zobacz [widok interakcji warstwowych](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Konfigurowanie ustawień profilowania
Na poniższej ilustracji przedstawiono sposób konfigurowania ustawień profilowania w oknie dialogowym Publikowanie aplikacji platformy Azure.

![Skonfiguruj ustawienia profilowania](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Aby włączyć **włączyć profilowanie** pole wyboru, musisz mieć profilera zainstalowane na komputerze lokalnym, którego używasz do publikowania usługi w chmurze. Domyślnie profilera jest instalowana po zainstalowaniu programu Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Aby skonfigurować ustawienia profilowania
1. W Eksploratorze rozwiązań Otwórz menu skrótów projektu platformy Azure, a następnie wybierz **publikowania**. Aby uzyskać szczegółowe instrukcje dotyczące publikowania usługi w chmurze, zobacz [publikowania usługi w chmurze przy użyciu narzędzia Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. W **publikowanie aplikacji platformy Azure** okno dialogowe, wybierz **Zaawansowane ustawienia** kartę.
3. Aby włączyć profilowanie, zaznacz **włączyć profilowanie** pole wyboru.
4. Aby skonfigurować ustawienia profilowania, wybierz **ustawienia** hiperłącza. Zostanie wyświetlone okno dialogowe Ustawienia profilowania.
5. Z **której metody profilowania chcesz użyć** przyciski opcji, należy wybrać typ profilowania, że należy.
6. Aby zbierać dane profilowania interakcji warstwy, wybierz **Włącz profilowanie interakcji między warstwami** pole wyboru.
7. Aby zapisać ustawienia, wybierz **OK** przycisku.
   
    Podczas publikowania tej aplikacji, te ustawienia są używane do utworzenia sesji profilowania dla każdej roli.

## <a name="viewing-profiling-reports"></a>Wyświetlanie raportów profilowania
Sesja profilowania jest tworzony dla każdego wystąpienia roli w usłudze w chmurze. Aby wyświetlanie raportów profilowania każdej sesji z programu Visual Studio, można wyświetlić okno Eksploratora serwera, a następnie wybierz węzeł rozwiązań usługi obliczenia Azure, aby zaznaczyć wystąpienia roli. Następnie można wyświetlić raportu profilowania, jak pokazano na poniższej ilustracji.

![Wyświetlanie raportu profilowania z platformy Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Wyświetlanie raportów profilowania
1. Aby wyświetlić okno Eksploratora serwera w programie Visual Studio, na pasku menu wybierz widok Eksploratora serwera.
2. Wybierz węzeł rozwiązań usługi obliczenia Azure, a następnie wybierz węzeł wdrożenia usługi Azure dla wybranego profilu po opublikowaniu w programie Visual Studio usługi w chmurze.
3. Aby wyświetlić raporty profilowania dla wystąpienia, wybierz rolę usługi, otwórz menu skrótów dla określonego wystąpienia, a następnie wybierz **Wyświetl raport profilowania**.
   
    Raport o nazwie pliku Vsp jest teraz pobierane z usługi Azure, a stan pobierania zostanie wyświetlony w dzienniku aktywności platformy Azure. Po zakończeniu pobierania raportu profilowania jest wyświetlany na karcie w edytorze programu Visual Studio o nazwie <Role name> *<Instance Number>* <identifier>pliku Vsp. Zostanie wyświetlone podsumowanie danych dla raportu.
4. Aby wyświetlić różne widoki raportu, na liście bieżący widok, wybierz typ widoku, który ma. Aby uzyskać więcej informacji, zobacz [widoku raportu narzędzi profilowania](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie usług w chmurze](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publikowanie do usługi w chmurze Azure w programie Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

