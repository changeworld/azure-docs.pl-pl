---
title: Monitorowanie zaplanowanych zdarzeń maszyn wirtualnych z systemem Windows na platformie Azure
description: Dowiedz się, jak monitorować maszyny wirtualne platformy Azure pod kątem zaplanowanych zdarzeń.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073310"
---
# <a name="monitoring-scheduled-events"></a>Monitorowanie zaplanowanych zdarzeń

Aktualizacje są stosowane do różnych części platformy Azure każdego dnia, aby zapewnić bezpieczeństwo i aktualnie uruchomione na nich usługi. Oprócz planowanych aktualizacji mogą również wystąpić nieplanowane zdarzenia. Na przykład w przypadku wykrycia awarii sprzętu lub usterki usługi platformy Azure mogą wymagać przeprowadzenia nieplanowanej konserwacji. Za pomocą migracji na żywo, pamięci zachowania aktualizacji i ogólnie utrzymanie ścisłego paska wpływu aktualizacji, w większości przypadków te zdarzenia są prawie przezroczyste dla klientów i nie mają wpływu lub co najwyżej spowodować kilka sekund zamrożenia maszyny wirtualnej. Jednak w przypadku niektórych aplikacji nawet kilka sekund zamrożenia maszyny wirtualnej może spowodować wpływ. Wiedza z wyprzedzeniem o nadchodzącej konserwacji platformy Azure jest ważna, aby zapewnić najlepsze środowisko dla tych aplikacji. [Usługa zaplanowane zdarzenia](scheduled-events.md) zapewnia interfejs programowy, który ma być powiadamiany o nadchodzącej konserwacji i umożliwia bezpiecznie obsługiwać konserwacji. 

W tym artykule pokażemy, jak można użyć zaplanowanych zdarzeń, które mają być powiadamiane o zdarzeniach konserwacji, które mogą mieć wpływ na maszyny wirtualne i utworzyć kilka podstawowych automatyzacji, które mogą pomóc w monitorowaniu i analizie.


## <a name="routing-scheduled-events-to-log-analytics"></a>Routing zaplanowanych zdarzeń do usługi Log Analytics

Zaplanowane zdarzenia są dostępne w ramach [usługi metadanych wystąpienia platformy Azure,](instance-metadata-service.md)która jest dostępna na każdej maszynie wirtualnej platformy Azure. Klienci mogą pisać automatyzacji do kwerendy punktu końcowego ich maszyn wirtualnych, aby znaleźć zaplanowane powiadomienia konserwacji i wykonać środki zaradcze, takie jak zapisywanie stanu i biorąc maszyny wirtualnej z rotacji. Zaleca się automatyzację tworzenia, aby rejestrować zaplanowane zdarzenia, dzięki czemu można mieć dziennik inspekcji zdarzeń konserwacji platformy Azure. 

W tym artykule poprowadzimy Cię przez sposób przechwytywania konserwacji zaplanowane zdarzenia do analizy dzienników. Następnie uruchomimy kilka podstawowych akcji powiadomień, takich jak wysyłanie wiadomości e-mail do zespołu i uzyskiwanie historycznego widoku wszystkich zdarzeń, które wpłynęły na maszyny wirtualne. W przypadku agregacji zdarzeń i automatyzacji użyjemy [usługi Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace)ale można użyć dowolnego rozwiązania do monitorowania, aby zebrać te dzienniki i wyzwolić automatyzację.

![Diagram przedstawiający cykl życia zdarzenia](./media/notifications/events.png)

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie należy utworzyć [maszynę wirtualną systemu Windows w zestawie dostępności](tutorial-availability-sets.md). Zaplanowane zdarzenia zapewniają powiadomienia o zmianach, które mogą mieć wpływ na dowolne maszyny wirtualne w zestawie dostępności, usłudze w chmurze, zestawie skalowania maszyny wirtualnej lub autonomicznych maszynach wirtualnych. Będziemy uruchamiać [usługę,](https://github.com/microsoft/AzureScheduledEventsService) która sonduje dla zaplanowanych zdarzeń na jednej z maszyn wirtualnych, która będzie działać jako moduł zbierający, aby uzyskać zdarzenia dla wszystkich innych maszyn wirtualnych w zestawie dostępności.    

Nie usuwaj grupy zasobów grupy na końcu samouczka.

Należy również [utworzyć obszar roboczy usługi Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace) który będzie używany do agregowania informacji z maszyn wirtualnych w zestawie dostępności.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Teraz powinien mieć 2 początkowe maszyny wirtualne w zestawie dostępności. Teraz musimy utworzyć 3 rd VM, o nazwie myCollectorVM, w tym samym zestawie dostępności. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Pobierz plik .zip instalacji projektu z [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Połącz się z **myCollectorVM** i skopiuj plik .zip na maszynę wirtualną i wyodrębnij wszystkie pliki. Na maszynie wirtualnej otwórz monit programu PowerShell. Przenieś monit do folderu `SchService.ps1`zawierającego `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`na przykład: i skonfiguruj usługę.

```powershell
.\SchService.ps1 -Setup
```

Uruchom usługę.

```powershell
.\SchService.ps1 -Start
```

Usługa rozpocznie sondowanie co 10 sekund dla wszystkich zaplanowanych zdarzeń i zatwierdzi zdarzenia, aby przyspieszyć konserwację.  Freeze, Reboot, Redeploy i Preempt to zdarzenia przechwycone przez zdarzenia harmonogramu.   Należy zauważyć, że można rozszerzyć skrypt, aby wyzwolić pewne środki zaradcze przed zatwierdzeniem zdarzenia.

Sprawdź poprawność stanu usługi i upewnij się, że jest uruchomiona.

```powershell
.\SchService.ps1 -status  
```

To powinno `Running`powrócić .

Usługa rozpocznie sondowanie co 10 sekund dla wszystkich zaplanowanych zdarzeń i zatwierdzi zdarzenia, aby przyspieszyć konserwację.  Freeze, Reboot, Redeploy i Preempt to zdarzenia przechwycone przez zdarzenia harmonogramu. Można rozszerzyć skrypt, aby wyzwolić pewne środki zaradcze przed zatwierdzeniem zdarzenia.

Gdy którekolwiek z powyższych zdarzeń są przechwytywane przez usługę Zdarzenia harmonogramu, zostanie ona zarejestrowana w stan zdarzenia dziennika zdarzeń aplikacji, typ zdarzenia, zasoby (nazwy maszyn wirtualnych) i NotBefore (minimalny okres wypowiedzenia). Zdarzenia można zlokalizować o identyfikatorze 1234 w dzienniku zdarzeń aplikacji.

Po skonfigurowaniu i uruchomieniu usługi będzie rejestrować zdarzenia w dziennikach aplikacji systemu Windows.   Aby sprawdzić, czy to działa, uruchom ponownie jedną z maszyn wirtualnych w zestawie dostępności i powinien zostać wyświetlony zdarzenie rejestrowane w Podglądzie zdarzeń w dziennikach > aplikacji systemu Windows z ponownym uruchomieniem maszyny wirtualnej. 

![Zrzut ekranu przedstawiający przeglądarkę zdarzeń.](./media/notifications/event-viewer.png)

Gdy zdarzenia są przechwytywane przez usługę Zdarzenia harmonogramu, zostanie ona zalogowana w aplikacji nawet dziennika ze stanem zdarzenia, typem zdarzenia, zasobami (nazwa maszyny wirtualnej) i NotBefore (minimalny okres wypowiedzenia). Zdarzenia można zlokalizować o identyfikatorze 1234 w dzienniku zdarzeń aplikacji.

> [!NOTE] 
> W tym przykładzie maszyny wirtualne były w zestawie dostępności, który umożliwił nam wyznaczyć pojedynczą maszynę wirtualną jako moduł zbierający do nasłuchiwać i kierować zaplanowane zdarzenia do naszej analizy dziennika działa miejsca. Jeśli masz autonomiczne maszyny wirtualne, można uruchomić usługę na każdej maszynie wirtualnej, a następnie połączyć je indywidualnie z obszaru roboczego analizy dziennika.
>
> Dla naszej konfiguracji wybraliśmy system Windows, ale możesz zaprojektować podobne rozwiązanie na Linuksie.

W dowolnym momencie można zatrzymać/usunąć usługę zaplanowanego `–stop` zdarzenia `–remove`za pomocą przełączników i .

## <a name="connect-to-the-workspace"></a>Łączenie się z obszarem roboczym


Teraz chcemy połączyć obszar roboczy analizy dzienników z maszyną wirtualną modułu zbierającego. Obszar roboczy usługi Log Analytics działa jako repozytorium i skonfigurujemy kolekcję dzienników zdarzeń do przechwytywania dzienników aplikacji z maszyny wirtualnej modułu zbierającego. 

 Aby przekierować zaplanowane zdarzenia do dziennika zdarzeń, który zostanie zapisany jako dziennik aplikacji przez naszą usługę, musisz połączyć maszynę wirtualną z obszarem roboczym usługi Log Analytics.  
 
1. Otwórz stronę utworzonego obszaru roboczego.
1. W obszarze **Połącz ze źródłem danych** wybierz **maszyny wirtualne platformy Azure .**

    ![Łączenie się z maszyną wirtualną jako źródłem danych](./media/notifications/connect-to-data-source.png)

1. Wyszukaj i wybierz **myCollectorVM**. 
1. Na nowej stronie **dla myCollectorVM**wybierz pozycję **Połącz**.

Spowoduje to zainstalowanie [agenta monitorowania firmy Microsoft](/azure/virtual-machines/extensions/oms-windows) na maszynie wirtualnej. Połączenie maszyny wirtualnej z obszarem roboczym i zainstalowanie rozszerzenia zajmie kilka minut. 

## <a name="configure-the-workspace"></a>Konfigurowanie obszaru roboczego

1. Otwórz stronę obszaru roboczego i wybierz pozycję **Ustawienia zaawansowane**.
1. Wybierz **pozycję Dane** z lewego menu, a następnie wybierz pozycję **Dzienniki zdarzeń systemu Windows**.
1. W **obszarze Zbieranie z następujących dzienników zdarzeń rozpocznij**wpisywanie *aplikacji,* a następnie wybierz **pozycję Aplikacja** z listy.

    ![Wybieranie ustawień zaawansowanych](./media/notifications/advanced.png)

1. Pozostaw **błąd ,** **OSTRZEŻENIE**i **informacje** zaznaczone, a następnie wybierz pozycję **Zapisz,** aby zapisać ustawienia.


> [!NOTE]
> Będzie pewne opóźnienie, a może upłynąć do 10 minut, zanim dziennik jest dostępny. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Tworzenie reguły alertów za pomocą usługi Azure Monitor 


Po przesunięciu zdarzeń do usługi Log Analytics można uruchomić następującą [kwerendę,](/azure/azure-monitor/log-query/get-started-portal) aby wyszukać harmonogram zdarzeń.

1. U góry strony wybierz pozycję **Dzienniki** i wklej następujące elementy w polu tekstowym:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Wybierz **pozycję Zapisz**, a następnie wpisz *logQuery* dla nazwy, pozostaw **kwerendę** jako typ, wpisz *VMLogs* jako **kategorię**, a następnie wybierz pozycję **Zapisz**. 

    ![Zapisywanie kwerendy](./media/notifications/save-query.png)

1. Wybierz przycisk **Nowa reguła alertu**. 
1. Na stronie **Reguła** `collectorworkspace` tworzenia pozostaw jako **Zasób**.
1. W **obszarze Warunek**wybierz wpis *Zawsze, <login undefined>gdy wyszukiwanie w dzienniku klienta jest *. Zostanie otwarta strona **Logika konfiguruj sygnał.**
1. W obszarze **Wartość progu**wprowadź *0,* a następnie wybierz pozycję **Gotowe**.
1. W obszarze **Akcje**wybierz pozycję **Utwórz grupę akcji**. Zostanie otwarta strona **Dodaj grupę akcji.**
1. W **pliku Action group name**wpisz *myActionGroup*.
1. W **skróconej nazwie**wpisz **myActionGroup**.
1. W **grupie zasobów**wybierz **myResourceGroupAvailability**.
1. W obszarze Akcje wpisz **nazwę akcji** **Wpisz e-mail,** a następnie wybierz pozycję **E-mail/SMS/Push/Voice**. Zostanie otwarta strona **e-mail/SMS/Push/Voice.**
1. Wybierz **pozycję Poczta e-mail**, wpisz swój adres e-mail, a następnie wybierz **przycisk OK**.
1. Na stronie **Dodaj grupę akcji** wybierz pozycję **OK**. 
1. Na stronie **Tworzenie reguły** w obszarze **SZCZEGÓŁY ALERTU**wpisz *myAlert* dla **nazwy reguły alertu,** a następnie wpisz *regułę alertu e-mail* dla **opisu**.
1. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**.
1. Uruchom ponownie jedną z maszyn wirtualnych w zestawie dostępności. W ciągu kilku minut powinieneś otrzymać wiadomość e-mail z powiadomieniem o wyzwoleniu alertu.

Aby zarządzać regułami alertów, przejdź do grupy zasobów, wybierz **alerty** z lewego menu, a następnie wybierz pozycję **Zarządzaj regułami alertów** u góry strony.

     
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz stronę [usługi zaplanowane zdarzenia](https://github.com/microsoft/AzureScheduledEventsService) w usłudze GitHub.
