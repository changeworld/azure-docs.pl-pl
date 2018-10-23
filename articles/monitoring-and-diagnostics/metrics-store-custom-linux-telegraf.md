---
title: Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem Telegraf InfluxData
description: Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem Telegraf InfluxData
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990709"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Zbieranie metryk niestandardowych dla maszyny Wirtualnej systemu Linux z agentem Telegraf InfluxData

Usługa Azure Monitor umożliwia zbieranie metryk niestandardowych za pomocą telemetrii aplikacji, agenta uruchomionego na zasoby platformy Azure lub nawet poza w systemy monitorowania i przesyłanie ich bezpośrednio do usługi Azure Monitor. Ten artykuł koncentruje się na udostępniające instrukcje dotyczące sposobu wdrażania [InfluxData](https://www.influxdata.com/) Telegraf agenta na maszynie Wirtualnej systemu Linux na platformie Azure i skonfiguruj agenta do publikowania wskaźników do usługi Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf Agent 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) jest oparte na wtyczkę agenta, który umożliwia zbieranie metryk z ponad 150 różnych źródeł. W zależności od obciążenia działają na maszynie Wirtualnej (np.) MySQL serwera NGINX, Apache, itp.) można skonfigurować agenta Aby wykorzystać wyspecjalizowane wtyczek wejściowych zbierania metryk. Dane wyjściowe wtyczki, a następnie włączyć agenta do zapisu do miejsc docelowych wybrane. Telegraf agent została zintegrowana bezpośrednio z metryki niestandardowe w usłudze Azure Monitor interfejsu API REST i obsługuje "wtyczki dane wyjściowe usługi Azure Monitor". Dzięki temu agentowi zbieranie określonych metryk obciążenia na maszynie Wirtualnej systemu Linux i przesłać je jako metryki niestandardowe do usługi Azure Monitor. 

 ![Omówienie agenta telegraf](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych 

Na potrzeby tego samouczka możemy wdrożyć Maszynę wirtualną systemu Linux uruchomiony system operacyjny Ubuntu 16.04 LTS. Telegraf agent jest obsługiwany dla większości systemów operacyjnych Linux. Debian i obr. / min pakiety są dostępne wraz z rozpakowanych binarnych systemu Linux w portalu pobierania InfluxData. Zobacz ten przewodnik instalacji dla dodatkowego Telegraf instrukcje dotyczące instalacji i opcje. 

Zaloguj się do [witryny Azure Portal](https://portal.azure.com)

Aby utworzyć nową maszynę Wirtualną systemu Linux: 

1. Kliknij przycisk **Utwórz zasób** opcji w okienku nawigacji po lewej stronie. 
1. Wyszukaj *maszyny wirtualnej*  
1. Wybierz *Ubuntu 16.04 LTS* i kliknij przycisk **Create** 
1. Podaj nazwę maszyny Wirtualnej, takie jak *MyTelegrafVM*.  
1. Pozostaw typ dysku jako **SSD**, a następnie podaj **username**, takich jak *azureuser*. 
1. Dla *typ uwierzytelniania*, wybierz opcję **hasło**, wpisz hasło, będzie używana później do protokołu SSH do tej maszyny Wirtualnej. 
1. Możliwość **Utwórz nową grupę zasobów**, następnie podaj nazwę, taką jak *myResourceGroup*.  Wybierz odpowiednią lokalizację, a następnie wybierz **OK**. 

     ![Tworzenie maszyny Wirtualnej z systemem Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Wybierz rozmiar maszyny wirtualnej. Można filtrować na przykład przez typ obliczenia lub typ dysku. 

     ![Rozmiar maszyny wirtualnej telegraficznego omówienie agenta](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Na **strony ustawień**w **sieci** > **sieciowej grupy zabezpieczeń**   > **Dodaj publiczne porty wejściowe**, wybierz opcję *HTTP* i *SSH (22)*. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**. 

1. Na stronie podsumowania wybierz pozycję Utwórz, aby rozpocząć wdrażanie maszyny Wirtualnej. 

1. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej. 

1. W bloku maszyny Wirtualnej, przejdź do **tożsamości** kartę i upewnij się, maszyna wirtualna ma tożsamości przypisanej w systemie *na*. 
 
![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną 

Utwórz połączenie SSH z maszyną wirtualną. Kliknij przycisk Połącz na stronie Przegląd dla maszyny Wirtualnej. 

![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

W programie Connect na stronie maszyny wirtualnej Zachowaj domyślne opcje do łączenia z nazwą DNS za pośrednictwem portu 22. Podczas logowania za pomocą maszyny Wirtualnej jest wyświetlany konta lokalnego polecenia połączenie. Kliknij przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Wklej połączenie SSH polecenie w powłoce, takich jak Azure Cloud Shell, Bash w systemie Ubuntu w Windows, lub użyj klienta SSH wybrane, aby utworzyć połączenie. 

## <a name="install-and-configure-telegraf"></a>Instalowanie i konfigurowanie Telegraf 

Aby zainstalować pakiet Debian Telegraf na maszynie Wirtualnej, uruchom następujące polecenia z poziomu sesji protokołu SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Plik konfiguracji firmy telegraf definiuje operacje Telegraf firmy. Domyślnie przykładowy plik konfiguracji jest zainstalowany w ścieżce "/ etc/telegraf/telegraf.conf". Przykładowy plik konfiguracji zawiera listę wszystkich możliwych danych wejściowych i wyjściowych wtyczek. Jednak zamierzamy utworzyć plik konfiguracji niestandardowej i agentem, użyj jej, uruchamiając następujące polecenia 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> Powyższe zezwala tylko dwóch danych wejściowych wtyczek "cpu" i "zoptymalizowany pod kątem pamięci", możesz dodać więcej danych wejściowych wtyczki (Docker, MySQL, NGINX itp.) w zależności od obciążenia uruchomione na tym komputerze. Pełna lista wtyczek danych wejściowych można znaleźć tutaj. 

Na koniec uruchamianie agenta przy użyciu nowej konfiguracji, firma Microsoft wymusić agenta Aby zatrzymać i uruchomić, uruchamiając następujące polecenia 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Teraz agent zbieranie metryk ze wszystkich wejściowych wtyczek, które określono i dodaj je do usługi Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Wykresu metryki Telegraf w witrynie Azure portal 

1. Otwórz [witryny Azure portal](https://portal.azure.com) 

1. Przejdź do nowej karcie Monitor, a następnie wybierz **metryki**.  
     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Wybierz maszynę Wirtualną w selektorze zasobów

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Wybierz *Telegraf/procesora CPU* przestrzeni nazw i wybierz *usage_system* metryki. Wybierz filtr według wymiarów na tę metrykę lub podziału na nich.  

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Dodatkowa konfiguracja 

Powyższe przewodnik zawiera informacje dotyczące sposobu konfigurowania agenta Telegraf zbieranie metryk z kilku podstawowych wtyczek danych wejściowych. Telegraf agent zapewnia obsługę ponad 150 wtyczek danych wejściowych, z niektórych pomocnicze dodatkowe opcje konfiguracji. Został opublikowany InfluxData [listę obsługiwanych wtyczek](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) oraz instrukcje dotyczące [sposobów ich konfigurowania](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Ponadto ten przewodnik ma dozwolone umożliwia agenta Telegraf emitują metryki o agent zostanie wdrożony na maszynie Wirtualnej. Telegraf agent może również służyć jako usługi przesyłania dalej metryk oraz moduł zbierający dla innych zasobów. Aby dowiedzieć się, jak skonfigurować agenta aby emitować metryki dla innych zasobów platformy Azure, zobacz [Azure Monitor niestandardowe metryki danych wyjściowych Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów dla maszyny wirtualnej, wybierz opcję Usuń, a następnie potwierdź nazwę grupy zasobów do usunięcia. 

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [metryki niestandardowe](metrics-custom-overview.md).


