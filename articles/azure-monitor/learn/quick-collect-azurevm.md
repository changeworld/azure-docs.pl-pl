---
title: Zbieranie danych dotyczących infrastruktury Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak włączyć rozszerzenie agenta usługi Log Analytics dla maszyn wirtualnych i umożliwić zbieranie danych z maszyn wirtualnych platformy Azure za pomocą usługi Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: e135c7fa9907d218ed32b6bdb0fd60da0ecf1851
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540655"
---
# <a name="collect-data-about-azure-virtual-machines"></a>Zbieranie danych dotyczących infrastruktury Azure Virtual Machines
Usługa [Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) umożliwia zbieranie danych bezpośrednio z maszyn wirtualnych platformy Azure i innych zasobów w środowisku do pojedynczego repozytorium na potrzeby szczegółowej analizy i korelacji.  W tym przewodniku Szybki start opisano, jak w kilku krokach skonfigurować i rozpocząć zbieranie danych z maszyn wirtualnych platformy Azure z systemem Linux lub Windows.  
 
W tym przewodniku Szybki start przyjęto założenie, że masz już maszynę wirtualną platformy Azure. Jeśli jeszcze jej nie masz, możesz [utworzyć maszynę wirtualną z systemem Windows](../../virtual-machines/windows/quick-create-portal.md) lub [utworzyć maszynę wirtualną z systemem Linux](../../virtual-machines/linux/quick-create-cli.md), wykonując czynności opisane w przewodnikach Szybki start dotyczących maszyn wirtualnych.

## <a name="log-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Kliknij pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę dla nowego **obszaru roboczego usługi Log Analytics**, na przykład *DefaultLAWorkspace*. Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.  
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
   * W polu **Lokalizacja** wybierz lokalizację, w której wdrożono maszyny wirtualne.  Aby uzyskać dodatkowe informacje, sprawdź, w których [regionach jest dostępna usługa Log Analytics](https://azure.microsoft.com/regions/services/).
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje dotyczące konkretnej warstwy, zobacz [Log Analytics — cennik](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Tworzenie bloku zasobów usługi Log Analytics](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Po podaniu wymaganych informacji w okienku **Obszar roboczy usługi Log Analytics** kliknij przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Włączanie rozszerzenia Log Analytics VM Extension

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

W przypadku wdrożonych już na platformie Azure maszyn wirtualnych z systemem Windows i Linux agent usługi Log Analytics jest instalowany za pomocą rozszerzenia Log Analytics VM Extension. Użycie tego rozszerzenia upraszcza proces instalacji i automatycznie konfiguruje agenta do przesyłania danych do określonego obszaru roboczego usługi Log Analytics. Agent jest również automatycznie uaktualniany, co zapewnia, że posiadane funkcje i poprawki są zawsze najnowsze. Przed kontynuowaniem sprawdź, czy maszyna wirtualna działa. W przeciwnym razie proces zakończy się niepowodzeniem.  

>[!NOTE]
>Agenta usługi Log Analytics dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics. 

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz utworzony wcześniej obszar *DefaultLAWorkspace*.
3. W menu po lewej stronie w obszarze Źródła danych obszaru roboczego kliknij pozycję **Maszyny wirtualne**.  
4. Z listy **Maszyny wirtualne** wybierz maszynę wirtualną, na której chcesz zainstalować agenta. Zwróć uwagę, że **Stan połączenia usługi Log Analytics** dla maszyny wirtualnej wskazuje, że jest ona **Niepołączona**.
5. W szczegółach dotyczących maszyny wirtualnej wybierz pozycję **Połącz**. Agent jest automatycznie instalowany i konfigurowany na potrzeby obszaru roboczego usługi Log Analytics. Ten proces trwa kilka minut. W tym czasie w obszarze **Stan** jest wyświetlana wartość **Łączenie**.
6. Po zainstalowaniu i połączeniu agenta **Stan połączenia usługi Log Analytics** zostanie zaktualizowany do wartości **Ten obszar roboczy**.

## <a name="collect-event-and-performance-data"></a>Zbieranie danych zdarzeń i wydajności
Usługa Log Analytics może zbierać zdarzenia z dzienników zdarzeń systemu Windows lub dzienników Syslog systemu Linux oraz z liczników wydajności określonych na potrzeby analizy i raportowania w dłuższym okresie po wykryciu określonego warunku.  Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika zdarzeń systemu Windows i dziennika Syslog systemu Linux oraz na początek z kilku typowych liczników wydajności.  

### <a name="data-collection-from-windows-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Windows
1. Wybierz pozycję **Ustawienia zaawansowane**.

    ![Ustawienia zaawansowane usługi Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png)

3. Wybierz pozycję **Dane**, a następnie pozycję **Dzienniki zdarzeń systemu Windows**.  
4. Dziennik zdarzeń dodaje się przez wpisanie nazwy dziennika.  Wpisz **System**, a następnie kliknij znak plus **+**.  
5. W tabeli zaznacz ważności **Błąd** i **Ostrzeżenie**.   
6. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.
7. Wybierz pozycję **Dane wydajności systemu Windows**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Windows. 
8. Podczas pierwszej konfiguracji liczników wydajności systemu Windows dla nowego obszaru roboczego usługi Log Analytics można szybko utworzyć kilka typowych liczników. Na liście obok każdego z nich znajduje się pole wyboru.

    ![Zaznaczone domyślne liczniki wydajności systemu Windows](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Kliknij pozycję **Dodaj wybrane liczniki wydajności**.  Są one dodawane i ustawiane wstępnie z dziesięciosekundowym interwałem próbkowania kolekcji.
  
9. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.

### <a name="data-collection-from-linux-vm"></a>Zbieranie danych z maszyny wirtualnej z systemem Linux

1. Wybierz dziennik **Syslog**.  
2. Dziennik zdarzeń dodaje się przez wpisanie nazwy dziennika.  Wpisz **Syslog**, a następnie kliknij znak plus **+**.  
3. W tabeli usuń zaznaczenie ważności **Informacja**, **Powiadomienie** i **Debugowanie**. 
4. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.
5. Wybierz pozycję **Dane wydajności systemu Linux**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Linux. 
6. Podczas pierwszej konfiguracji liczników wydajności systemu Linux dla nowego obszaru roboczego usługi Log Analytics można szybko utworzyć kilka typowych liczników. Na liście obok każdego z nich znajduje się pole wyboru.

    ![Zaznaczone domyślne liczniki wydajności systemu Windows](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Kliknij pozycję **Dodaj wybrane liczniki wydajności**.  Są one dodawane i ustawiane wstępnie z dziesięciosekundowym interwałem próbkowania kolekcji.  

7. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych
Teraz, po włączeniu zbierania danych, uruchomimy proste przykładowe wyszukiwanie w dziennikach, aby wyświetlić dane z docelowych maszyn wirtualnych.  

1. W witrynie Azure Portal przejdź do usługi Log Analytics i wybierz utworzony wcześniej obszar roboczy.
2. Kliknij kafelek **Przeszukiwanie dzienników** i w okienku Przeszukiwanie dzienników w polu zapytania wpisz `Perf`, a następnie naciśnij klawisz Enter lub kliknij przycisk wyszukiwania z prawej strony pola zapytania.

    ![Przykład zapytania dotyczącego przeszukiwania dzienników w usłudze Log Analytics](./media/quick-collect-azurevm/log-analytics-portal-perf-query.png) 

Na przykład zapytanie na poniższej ilustracji zwróciło 735 rekordów wydajności.  W Twoim przypadku wyników będzie znacznie mniej. 

![Wynik przeszukiwania dzienników w usłudze Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Obszar roboczy usługi Log Analytics można usunąć, gdy nie jest już potrzebny. W tym celu wybierz utworzony wcześniej obszar roboczy usługi Log Analytics i na stronie zasobów kliknij przycisk **Usuń**.


![Usuwanie zasobu usługi Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy są zbierane dane operacyjne i dane wydajności z maszyn wirtualnych z systemem Windows lub Linux, możesz w prosty sposób rozpocząć eksplorowanie i analizowanie danych oraz wykonywanie działań na *bezpłatnie* zebranych danych.  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.   

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
