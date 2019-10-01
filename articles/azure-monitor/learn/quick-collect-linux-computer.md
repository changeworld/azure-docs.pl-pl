---
title: 'Szybki Start: zbieranie danych z hybrydowego komputera z systemem Linux za pomocą Azure Monitor'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć agenta Log Analytics dla komputerów z systemem Linux działających poza platformą Azure i włączyć zbieranie danych przy użyciu dzienników Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: 72f50754a28f0bbf5648ae64299d28ff13e2ec31
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703051"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Szybki Start: zbieranie danych z komputera z systemem Linux w środowisku hybrydowym z Azure Monitor

[Azure monitor](../overview.md) może zbierać dane bezpośrednio z fizycznych lub wirtualnych komputerów z systemem Linux w środowisku do log Analytics obszaru roboczego w celu uzyskania szczegółowej analizy i korelacji. Zainstalowanie [agenta log Analytics](../platform/log-analytics-agent.md) pozwala Azure monitor zbierać dane z centrum danych lub innego środowiska chmury. Ten przewodnik Szybki Start przedstawia sposób konfigurowania i zbierania danych z serwera z systemem Linux przy użyciu kilku prostych kroków. Aby uzyskać informacje o maszynach wirtualnych z systemem Linux systemu Azure, zobacz [zbieranie danych o maszynach wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Aby zrozumieć obsługiwaną konfigurację, zobacz [obsługiwane systemy operacyjne Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) i [Konfiguracja zapory sieciowej](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-the-azure-portal"></a>Zaloguj się do Azure Portal

Zaloguj się do Azure Portal w [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Utwórz obszar roboczy

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **log Analytics**. Po rozpoczęciu wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. Wybierz pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego log Analytics**, na przykład *DefaultLAWorkspace*.  
   * Wybierz **subskrypcję** , z którą chcesz utworzyć łącze, wybierając pozycję z listy rozwijanej, jeśli wybrana wartość domyślna nie jest odpowiednia.
   * W obszarze **Grupa zasobów**wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
   * Wybierz **lokalizację** , w której zostaną wdrożone maszyny wirtualne.  Aby uzyskać dodatkowe informacje, zobacz [regiony, log Analytics są dostępne w programie](https://azure.microsoft.com/regions/services/).
   * Jeśli tworzysz obszar roboczy w nowej subskrypcji utworzonej po 2 kwietnia 2018, zostanie automatycznie użyty plan cenowy *na GB* , a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje o określonych warstwach, zobacz [log Analytics szczegóły cennika](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Utwórz blok zasobów Log Analytics](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. Po podania wymaganych informacji w okienku **obszaru roboczego log Analytics** wybierz pozycję **OK**.  

Gdy informacje są weryfikowane i obszar roboczy jest tworzony, możesz śledzić postęp w obszarze **powiadomienia** z menu. 

## <a name="obtain-workspace-id-and-key"></a>Uzyskaj identyfikator i klucz obszaru roboczego

Przed zainstalowaniem agenta Log Analytics dla systemu Linux wymagany jest identyfikator i klucz obszaru roboczego dla obszaru roboczego Log Analytics.  Te informacje są wymagane przez skrypt otoki agenta w celu poprawnego skonfigurowania agenta i upewnienia się, że może on pomyślnie komunikować się z Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. W lewym górnym rogu Azure Portal wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź **log Analytics**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze**.

2. Na liście obszarów roboczych Log Analytics wybierz utworzony wcześniej obszar roboczy. (Może być nazwany IT **DefaultLAWorkspace**).

3. Wybierz pozycję **Ustawienia zaawansowane**:

    ![Log Analytics ustawień zaawansowanych](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png) 
 
4. Wybierz pozycję **połączone źródła**, a następnie wybierz pozycję **serwery z systemem Linux**.

5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej oba elementy w ulubionym edytorze.

## <a name="install-the-agent-for-linux"></a>Zainstaluj agenta dla systemu Linux

Poniższe kroki konfigurują konfigurację agenta dla Log Analytics na platformie Azure i w chmurze Azure Government.  

>[!NOTE]
>Nie można skonfigurować agenta Log Analytics dla systemu Linux w celu raportowania w więcej niż jednym Log Analytics obszarze roboczym.  

Jeśli komputer z systemem Linux musi komunikować się za pomocą serwera proxy w celu Log Analytics, konfigurację serwera proxy można określić w wierszu polecenia, dołączając `-p [protocol://][user:password@]proxyhost[:port]`.  Właściwość *ProxyHost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy. Następujące polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera parametr `-p` proxy i przykładową składnię.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics w Azure Government chmurze, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy. Następujące polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera parametr `-p` proxy i przykładową składnię.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Zbieranie danych dotyczących zdarzeń i wydajności

Azure Monitor może zbierać zdarzenia z dzienników systemu Linux i liczników wydajności określonych na potrzeby analizy i raportowania w dłuższym czasie. Może również podejmować działania po wykryciu określonego warunku. Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika systemowego systemu Linux oraz kilka typowych liczników wydajności do uruchamiania w systemie.  

1. W lewym dolnym rogu Azure Portal wybierz pozycję **więcej usług**. W polu wyszukiwania wprowadź **log Analytics**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze**.

2. Wybierz pozycję **dane**, a następnie wybierz pozycję **Dziennik**systemowy.  

3. Aby dodać dziennik systemowy, wpisz nazwę dziennika. Wprowadź **Dziennik** systemowy, a następnie wybierz znak plus **+** .  

4. W tabeli Usuń zaznaczenie pola **Informacje o**serwerze, **informacja** i **debugowanie**. 

5. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

6. Wybierz pozycję **dane wydajności systemu Linux** , aby włączyć Zbieranie liczników wydajności na komputerze z systemem Linux. 

7. Podczas pierwszej konfiguracji liczników wydajności systemu Linux dla nowego obszaru roboczego Log Analytics można szybko utworzyć kilka typowych liczników. Są one wyświetlane obok każdego z pól wyboru.

    ![Wybrane domyślne liczniki wydajności systemu Windows](media/quick-collect-azurevm/linux-perfcounters-default.png)

    Wybierz pozycję **Zastosuj poniższą konfigurację do moich maszyn** , a następnie wybierz pozycję **Dodaj wybrane liczniki wydajności**. Są one dodawane i wstępnie ustawione z dziesięciu sekund przykładowego interwału kolekcji.  

8. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Przeglądanie zebranych danych

Teraz, po włączeniu zbierania danych, program umożliwia uruchamianie prostego przykładowego wyszukiwania w dzienniku, aby wyświetlić dane z komputera docelowego.  

1. W wybranym obszarze roboczym w okienku po lewej stronie wybierz pozycję **dzienniki**.

2. Na stronie kwerendy dzienników wpisz `Perf` w edytorze zapytań i wybierz polecenie **Uruchom**.
 
    ![Log Analytics przeszukiwania dzienników](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Na przykład zapytanie na poniższej ilustracji zwróciło 10 000 rekordów wydajności. Wyniki będą znacznie mniej.

    ![Wynik przeszukiwania dzienników Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, można usunąć agenta z komputera z systemem Linux i usunąć obszar roboczy Log Analytics.  

Aby usunąć agenta, uruchom następujące polecenie na komputerze z systemem Linux. Wartość *--przeczyszczania* spowoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Aby usunąć obszar roboczy, wybierz utworzony wcześniej obszar roboczy Log Analytics i na stronie zasób wybierz pozycję **Usuń**.

![Usuń zasób Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zbierasz dane operacyjne i wydajności z lokalnego komputera z systemem Linux, możesz łatwo rozpocząć Eksplorowanie, analizowanie i podejmowanie działań związanych z danymi, które są zbierane *bezpłatnie*.  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie lub analizowanie danych w Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
