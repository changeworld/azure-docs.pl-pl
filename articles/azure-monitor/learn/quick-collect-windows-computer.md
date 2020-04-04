---
title: Zbieranie danych z hybrydowego komputera z systemem Windows za pomocą usługi Azure Monitor
description: W tym przewodniku Szybki start dowiesz się, jak wdrożyć agenta usługi Log Analytics dla komputerów z systemem Windows działających poza platformą Azure i włączyć zbieranie danych za pomocą dzienników usługi Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 6435535ee14450c590a79ea68c8855c29df8bd9a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637883"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Zbieranie danych z komputera z systemem Windows w środowisku hybrydowym za pomocą usługi Azure Monitor

[Usługa Azure Monitor](../overview.md) może zbierać dane bezpośrednio z fizycznych lub wirtualnych komputerów z systemem Windows w danym środowisku do obszaru roboczego analizy dzienników w celu szczegółowej analizy i korelacji. Instalowanie [agenta usługi Log Analytics](../platform/log-analytics-agent.md) umożliwia platformie Azure Monitor zbieranie danych z centrum danych lub innego środowiska w chmurze. W tym przewodniku Szybki start opisano, jak w kilku prostych krokach skonfigurować i rozpocząć zbieranie danych z komputera z systemem Windows. Aby uzyskać informacje na temat maszyn wirtualnych systemu Windows platformy Azure, zobacz [Zbieranie danych o maszynach wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

Aby zapoznać się z obsługiwana konfiguracją, zobacz [Obsługiwane systemy operacyjne Windows](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) i [konfiguracja zapory sieciowej](../../azure-monitor/platform/log-analytics-agent.md#network-requirements).
 
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Wybierz **pozycję Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego usługi Log Analytics,** takiego jak *DefaultLAWorkspace*.  
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
   * W polu **Lokalizacja** wybierz lokalizację, w której wdrożono maszyny wirtualne.  Aby uzyskać dodatkowe informacje, sprawdź, w których [regionach jest dostępna usługa Log Analytics](https://azure.microsoft.com/regions/services/).
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje dotyczące konkretnej warstwy, zobacz [Log Analytics — cennik](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Tworzenie bloku zasobów usługi Log Analytics](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po podaniu wymaganych informacji w okienku **obszaru roboczego usługi Log Analytics** wybierz przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 


## <a name="get-the-workspace-id-and-key"></a>Pobierz identyfikator i klucz obszaru roboczego

Przed zainstalowaniem agenta usługi Log Analytics dla systemu Windows (nazywanego również agentem microsoft monitoring agent (MMA)) potrzebny jest identyfikator obszaru roboczego i klucz do obszaru roboczego usługi Log Analytics. Kreator instalacji potrzebuje tych informacji, aby poprawnie skonfigurować agenta i upewnić się, że może komunikować się z usługą Azure Monitor.  

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź **usługa Log Analytics**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz **obszary robocze usługi Log Analytics**.

2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy utworzony wcześniej. (Być może nazwałeś ją **DefaultLAWorkspace).)**

3. Wybierz **ustawienia zaawansowane:**

    ![Ustawienia zaawansowane usługi Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.

5. Skopiuj wartości na prawo od **identyfikatora obszaru roboczego** i **klucza podstawowego**. Wklej je do ulubionego edytora.

## <a name="install-the-agent-for-windows"></a>Instalacja agenta dla systemu Windows

Poniższe kroki zainstalować i skonfigurować agenta usługi Log Analytics na platformie Azure i platformy Azure dla instytucji rządowych. Użyjesz programu instalacyjnego programu Microsoft Monitoring Agent, aby zainstalować agenta na komputerze.

1. Kontynuując od poprzedniego zestawu kroków na stronie **Serwery systemu Windows** wybierz wersję **programu Windows Agent,** którą chcesz pobrać. Wybierz odpowiednią wersję dla architektury procesora systemu operacyjnego Windows.

2. Uruchom Instalatora, aby zainstalować agenta na komputerze.

3. Na **stronie powitalnej** wybierz pozycję **Dalej**.

4. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie wybierz pozycję **Zgadzam się**.

5. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie wybierz pozycję **Dalej**.

6. Na stronie **Opcje instalacji agenta** połącz agenta z usługą Azure Log Analytics, a następnie wybierz pozycję **Dalej**.

7. Na stronie **Usługi Azure Log Analytics** wykonaj następujące kroki:

   1. Wklej identyfikator **obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy),** który został skopiowany wcześniej. Jeśli komputer powinien zgłosić się do obszaru roboczego usługi Log Analytics w usłudze Azure Government, wybierz pozycję **Azure US Government** na liście Azure **Cloud.**  
   2. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, wybierz pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło do uwierzytelniania na serwerze proxy, a następnie wybierz pozycję **Dalej**.  

8. Po dodaniu ustawień konfiguracyjnych wybierz **pozycję Dalej:**

    ![Instalator programu Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie wybierz pozycję **Zainstaluj**.

10. Na stronie **Konfiguracja zakończona pomyślnie** wybierz pozycję **Zakończ**.

Po zakończeniu instalacji i instalacji program Microsoft Monitoring Agent pojawi się w Panelu sterowania. Można przejrzeć konfigurację i sprawdzić, czy agent jest połączony z obszarem roboczym usługi Log Analytics. Po nawiązaniu połączenia na karcie **Usługa Azure Log Analytics** agent wyświetla ten komunikat: Agent monitorowania firmy Microsoft pomyślnie połączył się z **usługą Microsoft Log Analytics.**<br><br> ![Stan połączenia MMA](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Zbieranie danych zdarzeń i wydajności

Usługa Azure Monitor może zbierać zdarzenia określone z dziennika zdarzeń systemu Windows i liczników wydajności w celu długoterminowej analizy i raportowania. Może również podjąć działania, gdy wykryje określony warunek. Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika zdarzeń systemu Windows oraz, na początek, z kilku typowych liczników wydajności.  

1. W lewym dolnym rogu witryny Azure portal wybierz pozycję **Więcej usług**. W polu wyszukiwania wprowadź **usługa Log Analytics**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz **obszary robocze usługi Log Analytics**.

2. Wybierz **ustawienia zaawansowane:**

    ![Ustawienia zaawansowane usługi Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Wybierz pozycję **Dane**, a następnie pozycję **Dzienniki zdarzeń systemu Windows**.  

4. Dodaj dziennik zdarzeń, wprowadzając nazwę dziennika. Wprowadź **system**, a następnie**+** wybierz znak plus ( ).  

5. W tabeli wybierz **ważność błędów** i **ostrzeżeń.**

6. Wybierz **pozycję Zapisz** u góry strony.

7. Wybierz pozycję **Liczniki wydajności systemu Windows**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Windows.

8. Po pierwszym skonfigurowaniu liczników wydajności systemu Windows dla nowego obszaru roboczego usługi Log Analytics, masz możliwość szybkiego utworzenia kilku typowych liczników. Każda opcja jest wyświetlana, obok pola wyboru:

    ![Liczniki wydajności systemu Windows](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Wybierz **pozycję Dodaj wybrane liczniki wydajności**. Liczniki są dodawane i predefiniowane z dziesięciosekundowym interwałem pobierania próbek.

9. Wybierz **pozycję Zapisz** u góry strony.

## <a name="view-collected-data"></a>Wyświetlanie zebranych danych

Po włączeniu zbierania danych uruchommy proste wyszukiwanie w dzienniku, aby wyświetlić niektóre dane z komputera docelowego.  

1. W wybranym obszarze roboczym z lewego okienka wybierz pozycję **Dzienniki**.

2. Na stronie Kwerenda Dzienniki wpisz `Perf` edytor zapytań i wybierz pozycję **Uruchom**.
 
    ![Wyszukiwanie dzienników usługi Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Na przykład kwerenda na tym obrazie zwróciła 10 000 rekordów wydajności. W Twoim przypadku wyników będzie znacznie mniej.

    ![Wynik przeszukiwania dzienników w usłudze Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można usunąć agenta z komputera i usunąć obszar roboczy usługi Log Analytics, jeśli nie jest już potrzebny.  

Aby usunąć agenta, wykonaj następujące czynności:

1. Otwórz Panel sterowania.

2. Otwórz aplet **Programy i funkcje**.

3. W **obszarze Programy i funkcje**wybierz pozycję Microsoft Monitoring **Agent,** a następnie wybierz pozycję **Odinstaluj**.

Aby usunąć utworzony wcześniej obszar roboczy usługi Log Analytics, zaznacz go, a na stronie zasobu wybierz pozycję **Usuń:**

![Usuwanie obszaru roboczego usługi Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zbierasz dane operacyjne i wydajnościowe z komputera z systemem Windows, możesz łatwo rozpocząć eksplorowanie, analizowanie i działanie na gromadzonych danych za *darmo.*  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](tutorial-viewdata.md)
