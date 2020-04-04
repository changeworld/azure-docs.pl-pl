---
title: 'Szybki start: zbieranie danych z hybrydowego komputera z systemem Linux za pomocą usługi Azure Monitor'
description: W tym przewodniku Szybki start dowiesz się, jak wdrożyć agenta usługi Log Analytics dla komputerów z systemem Linux działających poza platformą Azure i włączyć zbieranie danych za pomocą dzienników usługi Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1da862a7994d7db5ee8c9f090119e14f19c6b13a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637892"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Szybki start: zbieranie danych z komputera z systemem Linux w środowisku hybrydowym za pomocą usługi Azure Monitor

[Usługa Azure Monitor](../overview.md) może zbierać dane bezpośrednio z fizycznych lub wirtualnych komputerów z systemem Linux w danym środowisku do obszaru roboczego usługi Log Analytics w celu szczegółowej analizy i korelacji. Instalowanie [agenta usługi Log Analytics](../platform/log-analytics-agent.md) umożliwia platformie Azure Monitor zbieranie danych z centrum danych lub innego środowiska w chmurze. Ten przewodnik Szybki start pokazuje, jak skonfigurować i zbierać dane z serwera Linux za pomocą kilku prostych kroków. Aby uzyskać informacje na temat maszyn wirtualnych z systemem Azure Linux, zobacz [Zbieranie danych o maszynach wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Aby zapoznać się z obsługiwana konfiguracją, zobacz [Obsługiwane systemy operacyjne Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) i [konfiguracja zapory sieciowej](../../azure-monitor/platform/log-analytics-agent.md#network-requirements).
 
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.

    ![Znajdowanie obszaru roboczego usługi Log Analytics w witrynie Azure portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Wybierz **pozycję Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego usługi Log Analytics,** takiego jak *DefaultLAWorkspace*.  
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
   * W polu **Lokalizacja** wybierz lokalizację, w której wdrożono maszyny wirtualne.  Aby uzyskać dodatkowe informacje, sprawdź, w których [regionach jest dostępna usługa Log Analytics](https://azure.microsoft.com/regions/services/).
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje dotyczące konkretnej warstwy, zobacz [Log Analytics — cennik](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po podaniu wymaganych informacji w okienku **obszaru roboczego usługi Log Analytics** wybierz przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego

Przed zainstalowaniem agenta usługi Log Analytics dla systemu Linux potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics. Te informacje są wymagane przez skrypt otoki agenta, aby poprawnie skonfigurować agenta i upewnić się, że może pomyślnie komunikować się z usługą Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź **usługa Log Analytics**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz **obszary robocze usługi Log Analytics**.

2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy utworzony wcześniej. (Być może nazwałeś ją **DefaultLAWorkspace).)**

3. Wybierz **ustawienia zaawansowane:**

    ![Menu Ustawienia zaawansowane dla usługi Log Analytics w witrynie Azure portal](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Wybierz **Połączone źródła**, a następnie **Serwery Linux**.

5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej obie wartości do ulubionego edytora.

## <a name="install-the-agent-for-linux"></a>Instalacja agenta dla systemu Linux

Następujące kroki umożliwiają konfigurację agenta usługi Log Analytics na platformie Azure i w chmurze Azure Government.  

>[!NOTE]
>Agenta usługi Log Analytics dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics.  

Jeśli komputer z systemem Linux musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, konfigurację serwera proxy można określić w wierszu polecenia przez dołączenie elementu `-p [protocol://][user:password@]proxyhost[:port]`.  Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym usługi Log Analytics, uruchom następujące polecenie, podając identyfikator obszaru roboczego i klucz podstawowy skopiowany wcześniej. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera `-p` parametr serwera proxy i przykład składnię, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym usługi Log Analytics w chmurze platformy Azure dla instytucji rządowych, uruchom następujące polecenie podając identyfikator obszaru roboczego i klucz podstawowy skopiowany wcześniej. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera `-p` parametr serwera proxy i przykład składnię, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Zbieranie danych zdarzeń i wydajności

Usługa Azure Monitor może zbierać zdarzenia z liczników syslogu systemu Linux i wydajności, które można określić dla długoterminowej analizy i raportowania. Może również podjąć działania, gdy wykryje określony warunek. Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika Syslog systemu Linux oraz na początek z kilku typowych liczników wydajności.  

1. W lewym dolnym rogu witryny Azure portal wybierz pozycję **Więcej usług**. W polu wyszukiwania wprowadź **usługa Log Analytics**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz **obszary robocze usługi Log Analytics**.

2. Wybierz **pozycję Dane**, a następnie wybierz pozycję **Syslog**.  

3. Możesz dodać syslog wpisując nazwę dziennika. Wprowadź **Syslog,** a następnie **+** wybierz znak plus .  

4. W tabeli usuń zaznaczenie ważności **Informacja**, **Powiadomienie** i **Debugowanie**. 

5. Wybierz **pozycję Zapisz** u góry strony, aby zapisać konfigurację.

6. Wybierz pozycję **Dane wydajności systemu Linux**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Linux. 

7. Podczas pierwszej konfiguracji liczników wydajności systemu Linux dla nowego obszaru roboczego usługi Log Analytics można szybko utworzyć kilka typowych liczników. Na liście obok każdego z nich znajduje się pole wyboru.

    ![Domyślne liczniki wydajności systemu Linux wybrane w usłudze Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Wybierz **opcję Zastosuj poniżej konfigurację do moich komputerów,** a następnie wybierz pozycję **Dodaj wybrane liczniki wydajności**. Są one dodawane i ustawiane wstępnie z dziesięciosekundowym interwałem próbkowania kolekcji.  

8. Wybierz **pozycję Zapisz** u góry strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

Teraz, po włączeniu zbierania danych, uruchomimy proste przykładowe wyszukiwanie w dziennikach, aby wyświetlić dane z docelowego komputera.  

1. W wybranym obszarze roboczym z lewego okienka wybierz pozycję **Dzienniki**.

2. Na stronie Kwerenda Dzienniki wpisz `Perf` edytor zapytań i wybierz pozycję **Uruchom**.
 
    ![Wyszukiwanie dzienników usługi Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Na przykład kwerenda na poniższej ilustracji zwróciła 10 000 rekordów wydajności. W Twoim przypadku wyników będzie znacznie mniej.

    ![Wynik przeszukiwania dzienników w usłudze Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, można usunąć agenta z komputera z systemem Linux i usunąć obszar roboczy usługi Log Analytics.  

Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux. Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Aby usunąć obszar roboczy, wybierz utworzony wcześniej obszar roboczy usługi Log Analytics, a na stronie zasobu wybierz pozycję **Usuń**.

![Usuwanie zasobu usługi Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy są zbierane dane operacyjne i dane wydajności z lokalnego komputera z systemem Linux, możesz w prosty sposób rozpocząć eksplorowanie i analizowanie danych oraz wykonywanie działań na *bezpłatnie* zebranych danych.  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
