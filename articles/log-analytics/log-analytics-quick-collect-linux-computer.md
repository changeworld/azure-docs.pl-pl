---
title: Zbieranie danych z lokalnych komputerów z systemem Linux przy użyciu usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak wdrożyć agenta Log Analytics dla systemu Linux i włączyć zbieranie danych z tego systemu operacyjnego przy użyciu usługi Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: af385a7cbf97ff15156cc78c77f8b54e2f79badb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637637"
---
# <a name="collect-data-from-linux-computer-hosted-in-your-environment"></a>Zbieranie danych z komputerów z systemem Linux hostowanych w danym środowisku
Usługa [Azure Log Analytics](log-analytics-overview.md) umożliwia zbieranie danych bezpośrednio z fizycznego lub wirtualnego komputera z systemem Linux i innych zasobów w środowisku do pojedynczego repozytorium na potrzeby szczegółowej analizy i korelacji.  W tym przewodniku Szybki start opisano, jak w kilku prostych krokach skonfigurować i rozpocząć zbieranie danych z komputera z systemem Linux.  Aby uzyskać informacje dotyczące maszyn wirtualnych platformy Azure z systemem Linux, zobacz następujący temat [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Linux](log-analytics-concept-hybrid.md#supported-linux-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-concept-hybrid.md#network-firewall-requirements).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Kliknij pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

  * Podaj nazwę dla nowego **obszaru roboczego OMS**, na przykład *DefaultLAWorkspace*. 
  * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
  * W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
  * W polu **Lokalizacja** wybierz lokalizację, w której wdrożono maszyny wirtualne.  Aby uzyskać dodatkowe informacje, sprawdź, w których [regionach jest dostępna usługa Log Analytics](https://azure.microsoft.com/regions/services/).  
  * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje dotyczące konkretnej warstwy, zobacz [Log Analytics — cennik](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. Po podaniu wymaganych informacji w okienku **Obszar roboczy OMS** kliknij przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem agenta pakietu OMS dla systemu Linux potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics.  Te informacje są wymagane przez skrypt otoki agenta w celu poprawnego skonfigurowania agenta i upewnienia się, że może się skutecznie komunikować z usługą Log Analytics.  

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi** w lewym górnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. Na liście obszarów roboczych usługi Log Analytics wybierz utworzony wcześniej obszar *DefaultLAWorkspace*.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Linux**.   
5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej obie wartości do ulubionego edytora.   

## <a name="install-the-agent-for-linux"></a>Instalacja agenta dla systemu Linux
Następujące kroki umożliwiają konfigurację agenta usługi Log Analytics na platformie Azure i w chmurze Azure Government.  

>[!NOTE]
>Agenta pakietu OMS dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics.  

Jeśli komputer z systemem Linux musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, konfigurację serwera proxy można określić w wierszu polecenia przez dołączenie elementu `-p [protocol://][user:password@]proxyhost[:port]`.  Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z usługą Log Analytics, uruchom następujące polecenie, podając skopiowane wcześniej identyfikator obszaru roboczego i klucz podstawowy.  To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Poniższe polecenie uwzględnia parametr serwera proxy `-p` i przykładową składnię.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z usługą Log Analytics w chmurze Azure Government, uruchom następujące polecenie, podając skopiowane wcześniej identyfikator obszaru roboczego i klucz podstawowy.  To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Poniższe polecenie uwzględnia parametr serwera proxy `-p` i przykładową składnię.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Zbieranie danych zdarzeń i wydajności
Usługa Log Analytics może zbierać zdarzenia z dzienników Syslog systemu Linux oraz z liczników wydajności określonych na potrzeby analizy i raportowania w dłuższym okresie po wykryciu określonego warunku.  Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika Syslog systemu Linux oraz na początek z kilku typowych liczników wydajności.  

1. Wybierz dziennik **Syslog**.  
2. Dziennik zdarzeń dodaje się przez wpisanie nazwy dziennika.  Wpisz **Syslog**, a następnie kliknij znak plus **+**.  
3. W tabeli usuń zaznaczenie ważności **Informacja**, **Powiadomienie** i **Debugowanie**. 
4. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.
5. Wybierz pozycję **Dane wydajności systemu Linux**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Windows. 
6. Podczas pierwszej konfiguracji liczników wydajności systemu Linux dla nowego obszaru roboczego usługi Log Analytics można szybko utworzyć kilka typowych liczników. Na liście obok każdego z nich znajduje się pole wyboru.<br><br> ![Zaznaczone domyślne liczniki wydajności systemu Windows](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)<br> Kliknij pozycję **Dodaj wybrane liczniki wydajności**.  Są one dodawane i ustawiane wstępnie z dziesięciosekundowym interwałem próbkowania kolekcji.  
7. Kliknij przycisk **Zapisz** znajdujący się u góry strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych
Teraz, po włączeniu zbierania danych, uruchomimy proste przykładowe wyszukiwanie w dziennikach, aby wyświetlić dane z docelowego komputera.  

1. W witrynie Azure Portal przejdź do usługi Log Analytics i wybierz utworzony wcześniej obszar roboczy.
2. Kliknij kafelek **Przeszukiwanie dzienników** i w okienku Przeszukiwanie dzienników w polu zapytania wpisz `Perf`, a następnie naciśnij klawisz Enter lub kliknij przycisk wyszukiwania z prawej strony pola zapytania.<br><br> ![Przykład zapytania dotyczącego przeszukiwania dzienników w usłudze Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Na przykład zapytanie na poniższej ilustracji zwróciło 735 rekordów wydajności.<br><br> ![Wynik przeszukiwania dzienników w usłudze Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebny, można usunąć agenta z komputera z systemem Linux i usunąć obszar roboczy usługi Log Analytics.  

Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux.  Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Aby usunąć obszar roboczy, wybierz utworzony wcześniej obszar roboczy usługi Log Analytics i na stronie zasobów kliknij przycisk **Usuń**.<br><br> ![Usuwanie zasobu usługi Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy są zbierane dane operacyjne i dane wydajności z lokalnego komputera z systemem Linux, możesz w prosty sposób rozpocząć eksplorowanie i analizowanie danych oraz wykonywanie działań na *bezpłatnie* zebranych danych.  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.   

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](log-analytics-tutorial-viewdata.md)
