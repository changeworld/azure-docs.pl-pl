---
title: Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem InfluxData Telegraf
description: Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem InfluxData Telegraf
author: lingliw
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 01/21/19
ms.author: v-lingwu
ms.subservice: metrics
ms.openlocfilehash: 14415b88cd6036642442ef9ae23e8dee301bb908
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741609"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem InfluxData Telegraf

Za pomocą usługi Azure Monitor, można zbierać metryki niestandardowe, za pomocą telemetrii aplikacji, agenta uruchomionego na zasoby platformy Azure lub nawet poza w systemy monitorowania. Następnie możesz przesłać je bezpośrednio do usługi Azure Monitor. Ten artykuł zawiera instrukcje dotyczące sposobu wdrażania [InfluxData](https://www.influxdata.com/) Telegraf agenta na maszynie Wirtualnej systemu Linux na platformie Azure i skonfiguruj agenta do publikowania wskaźników do usługi Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agent InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) jest oparte na w plug agenta, który umożliwia zbieranie metryk z ponad 150 różnych źródeł. W zależności od tego, co obciążenia uruchamiane na maszynie Wirtualnej można skonfigurować agenta Aby wykorzystać wyspecjalizowane wejściowych wtyczek zbierania metryk. Przykłady to Apache, MySQL i NGINX. Za pomocą wtyczki danych wyjściowych, agent może następnie zapisywać do miejsc docelowych, które wybierzesz. Telegraf agent została zintegrowana bezpośrednio z metryki niestandardowe w usłudze Azure Monitor interfejsu API REST. Obsługuje ona wtyczki dane wyjściowe usługi Azure Monitor. Przy użyciu tej wtyczki, agenta można zbierać metryki specyficznego dla obciążenia na maszynie Wirtualnej systemu Linux i przesłać je jako niestandardowe metryki do usługi Azure Monitor. 

 ![Omówienie agenta telegraf](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych 

Na potrzeby tego samouczka możemy wdrożyć Maszynę wirtualną systemu Linux z systemem operacyjnym Ubuntu 16.04 LTS. Telegraf agent jest obsługiwany dla większości systemów operacyjnych Linux. Debian i obr. / min pakiety są dostępne wraz z rozpakowanych binarnych systemu Linux na [portalu pobierania InfluxData](https://portal.influxdata.com/downloads). Zobacz ten [Przewodnik instalacji Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) dodatkowe instrukcje instalacji i opcji. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

Utwórz nową maszynę Wirtualną systemu Linux: 

1. Wybierz **Utwórz zasób** opcji w okienku nawigacji po lewej stronie. 
1. Wyszukaj **maszyny wirtualnej**.  
1. Wybierz **Ubuntu 16.04 LTS** i wybierz **Utwórz**. 
1. Podaj nazwę maszyny Wirtualnej, takie jak **MyTelegrafVM**.  
1. Pozostaw typ dysku jako **SSD**. Następnie podaj **username**, takich jak **azureuser**. 
1. Aby uzyskać **typ uwierzytelniania**, wybierz opcję **hasło**. Następnie wprowadzić hasło, które będą używane później SSH do tej maszyny Wirtualnej. 
1. Możliwość **Utwórz nową grupę zasobów**. Następnie podaj nazwę, taką jak **myResourceGroup**. Wybierz swoje **lokalizacji**. Następnie wybierz pozycję **OK**. 

    ![Tworzenie maszyny wirtualnej z systemem Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Wybierz rozmiar maszyny wirtualnej. Można filtrować według **typ obliczeń** lub **typ dysku**, na przykład. 

    ![Omówienie agenta telegraf rozmiar maszyny wirtualnej](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na **ustawienia** strony w **sieci** > **sieciowej grupy zabezpieczeń**   >  ** Dodaj publiczne porty wejściowe**, wybierz opcję **HTTP** i **SSH (22)**. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**. 

1. Na stronie podsumowania wybierz **Utwórz** rozpocząć wdrażanie maszyny Wirtualnej. 

1. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrożenia maszyny Wirtualnej zostanie automatycznie otwarte podsumowanie. 

1. W okienku maszyny Wirtualnej, przejdź do **tożsamości** kartę. Upewnij się, że Twoja maszyna wirtualna ma przypisany systemowo tożsamości, ustaw **na**. 
 
    ![Maszyna wirtualna telegraf tożsamości w wersji zapoznawczej](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną 

Utwórz połączenie SSH z maszyną wirtualną. Wybierz **Connect** przycisk na stronie Przegląd dla maszyny Wirtualnej. 

![Strona przeglądu telegraf maszyny Wirtualnej](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

W **Połącz z maszyną wirtualną** Zachowaj domyślne opcje do łączenia z nazwą DNS za pośrednictwem portu 22. W **Zaloguj się przy użyciu lokalnego konta maszyny Wirtualnej**, polecenie połączenia jest wyświetlany. Wybierz przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Wklej polecenie połączenia SSH z poziomu powłoki, takich jak Azure Cloud Shell lub funkcji Bash w systemie ubuntu działającym w Windows, lub Utwórz połączenie za pomocą dowolnego klienta SSH. 

## <a name="install-and-configure-telegraf"></a>Instalowanie i konfigurowanie Telegraf 

Aby zainstalować pakiet Debian Telegraf na maszynie Wirtualnej, uruchom następujące polecenia z poziomu sesji protokołu SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Plik konfiguracji firmy telegraf definiuje operacje Telegraf firmy. Domyślnie przykładowy plik konfiguracji jest zainstalowany w ścieżce **/etc/telegraf/telegraf.conf**. Przykładowy plik konfiguracji zawiera listę wszystkich możliwych danych wejściowych i wyjściowych wtyczek. Jednakże utworzymy plik konfiguracji niestandardowej i agentem, użyj jej, uruchamiając następujące polecenia: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Powyższy kod umożliwia tylko dwa wejściowych dodatków plug-in: **procesora** i **mem**. Możesz dodać więcej danych wejściowych typu plug-in, w zależności od obciążenia, które jest uruchamiane na komputerze. Przykładami są platformy Docker, MySQL i NGINX. Aby uzyskać pełną listę danych wejściowych wtyczek, zobacz **dodatkowych czynności konfiguracyjnych** sekcji. 

Na koniec aby uruchamianie agenta przy użyciu nowej konfiguracji, firma Microsoft wymusić agenta Aby zatrzymać i ponownie uruchomić, uruchamiając następujące polecenia: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Teraz agent zbieranie metryk ze wszystkich wejściowych wtyczek określone i dodaj je do usługi Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Wykresu metryki Telegraf w witrynie Azure portal 

1. Otwórz [portal Azure](https://portal.azure.com). 

1. Przejdź do nowego **Monitor** kartę. Następnie wybierz pozycję **metryki**.  

     ![Monitorowanie — metryki (wersja zapoznawcza)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Wybierz maszynę Wirtualną w selektorze zasobów.

     ![Wykres metryk](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Wybierz **Telegraf/procesora CPU** przestrzeni nazw i wybierz **usage_system** metryki. Można filtrować według wymiarów tej metryki lub podziału na nich.  

     ![Wybierz przestrzeń nazw i metryki](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Dodatkowa konfiguracja 

Poprzedni przewodnik zawiera informacje dotyczące sposobu konfigurowania agenta Telegraf zbieranie metryk z kilku podstawowych danych wejściowych dodatków plug-in. Telegraf agent zapewnia obsługę ponad 150 wejściowego typu plug-in, z niektórych pomocnicze dodatkowe opcje konfiguracji. Został opublikowany InfluxData [listę obsługiwanych wtyczek](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) oraz instrukcje dotyczące [sposobów ich konfigurowania](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Ponadto w tym instruktażu użyto agenta Telegraf do emitują metryki o agent zostanie wdrożony na maszynie Wirtualnej. Telegraf agent może również służyć jako usługi przesyłania dalej metryk oraz moduł zbierający dla innych zasobów. Aby dowiedzieć się, jak skonfigurować agenta aby emitować metryki dla innych zasobów platformy Azure, zobacz [Azure Monitor niestandardowe metryki danych wyjściowych Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Gdy nie są już potrzebne, możesz usunąć grupę zasobów, maszyna wirtualna i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz **Usuń**. Potwierdź nazwę grupy zasobów do usunięcia. 

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).


