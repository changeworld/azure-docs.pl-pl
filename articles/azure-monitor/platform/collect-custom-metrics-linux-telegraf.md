---
title: Zbieranie niestandardowych metryk dla maszyny Wirtualnej systemu Linux za pomocą agenta InfluxData Telegraf
description: Instrukcje dotyczące wdrażania agenta Telegraf InfluxData na maszynie Wirtualnej systemu Linux na platformie Azure i konfigurowania agenta do publikowania metryk w usłudze Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655467"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Zbieranie niestandardowych metryk dla maszyny Wirtualnej z systemem Linux za pomocą agenta InfluxData Telegraf

Za pomocą usługi Azure Monitor można zbierać metryki niestandardowe za pośrednictwem danych telemetrycznych aplikacji, agenta działającego na zasoby platformy Azure, a nawet poza systemami monitorowania. Następnie można przesłać je bezpośrednio do usługi Azure Monitor. Ten artykuł zawiera instrukcje dotyczące wdrażania [agenta Telegraf InfluxData](https://www.influxdata.com/) na maszynie Wirtualnej systemu Linux na platformie Azure i konfigurowania agenta do publikowania metryk w usłudze Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>Agent InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/) jest agentem opartym na wtyczce, który umożliwia zbieranie danych z ponad 150 różnych źródeł. W zależności od tego, jakie obciążenia są uruchamiane na maszynie wirtualnej, można skonfigurować agenta do wykorzystania specjalistycznych wtyczek wejściowych do zbierania metryk. Przykładami są MySQL, NGINX i Apache. Za pomocą wtyczek wyjściowych agent może następnie zapisywać do wybranych miejsc docelowych. Agent Telegraf zintegrował się bezpośrednio z interfejsem API REST metryk niestandardowych usługi Azure Monitor. Obsługuje wtyczkę wyjściową usługi Azure Monitor. Korzystając z tej wtyczki, agent może zbierać metryki specyficzne dla obciążenia na maszynie Wirtualnej systemu Linux i przesyłać je jako metryki niestandardowe do usługi Azure Monitor. 

 ![Przegląd agenta telegraficznego](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Wysyłanie niestandardowych danych 

W tym samouczku wdrażamy maszynę wirtualną z systemem Linux, która uruchamia system operacyjny Ubuntu 16.04 LTS. Agent Telegraf jest obsługiwany dla większości systemów operacyjnych Linux. Pakiety Debian i RPM są dostępne wraz z nieopakowanymi plikami binarnymi Linuksa na [portalu pobierania InfluxData.](https://portal.influxdata.com/downloads) Dodatkowe instrukcje i opcje instalacji można znaleźć w tym [podręczniku instalacji telegrafu.](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) 

Zaloguj się do [Portalu Azure](https://portal.azure.com).

Utwórz nową maszynę wirtualną z systemem Linux: 

1. Wybierz opcję **Utwórz zasób** z lewego okienka nawigacji. 
1. Wyszukaj **maszynę wirtualną**.  
1. Wybierz **Ubuntu 16.04 LTS** i wybierz **pozycję Utwórz**. 
1. Podaj nazwę maszyny Wirtualnej, taką jak **MyTelegrafVM**.  
1. Pozostaw typ dysku jako **SSD**. Następnie podaj **nazwę użytkownika**, takie jak **azureuser**. 
1. W przypadku **typu Uwierzytelnianie**wybierz **pozycję Hasło**. Następnie wprowadź hasło, którego użyjesz później do SSH do tej maszyny Wirtualnej. 
1. Wybierz pozycję **Utwórz nową grupę zasobów**. Następnie podaj nazwę, taką jak **myResourceGroup**. Wybierz swoją **lokalizację**. Następnie wybierz przycisk **OK**. 

    ![Tworzenie maszyny wirtualnej z systemem Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Wybierz rozmiar maszyny wirtualnej. Możesz filtrować między innymi według wartości **Typ obliczeń** lub **Typ dysku**. 

    ![Omówienie agenta o rozmiarze maszyny wirtualnej Telegraph](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na stronie **Ustawienia** w **sieciowej** > **grupie** > zabezpieczeń**Wybierz publiczne porty przychodzące**wybierz **HTTP** i **SSH (22)**. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**. 

1. Na stronie podsumowania wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej. 

1. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny Wirtualnej. 

1. W okienku maszyny Wirtualnej przejdź do karty **Tożsamość.** **On** 
 
    ![Podgląd tożsamości maszyny Wirtualnej telegrafu](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną 

Utwórz połączenie SSH z maszyną wirtualną. Na stronie przeglądu wybierz przycisk **Połącz** dla swojej maszyny wirtualnej. 

![Strona przeglądu maszyny Wirtualnej Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą nazwy DNS przez port 22. W **pozycji Logowanie przy użyciu konta lokalnego maszyny Wirtualnej**jest wyświetlane polecenie połączenia. Wybierz przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Wklej polecenie połączenia SSH do powłoki, takiej jak Usługa Azure Cloud Shell lub Bash na Ubuntu w systemie Windows, lub użyj wybranego klienta SSH, aby utworzyć połączenie. 

## <a name="install-and-configure-telegraf"></a>Instalowanie i konfigurowanie programu Telegraf 

Aby zainstalować pakiet Telegraf Debian na maszynie wirtualnej, uruchom następujące polecenia z sesji SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Plik konfiguracyjny Telegraf definiuje działalność Telegrafu. Domyślnie przykładowy plik konfiguracyjny jest instalowany na ścieżce **/etc/telegraf/telegraf.conf**. Przykładowy plik konfiguracyjny zawiera listę wszystkich możliwych wtyczek wejściowych i wyjściowych. Jednak utworzymy niestandardowy plik konfiguracyjny i agent użyje go, uruchamiając następujące polecenia: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Poprzedni kod umożliwia tylko dwie wtyczki wejściowe: **cpu** i **mem**. Można dodać więcej wtyczek wejściowych, w zależności od obciążenia, które działa na komputerze. Przykładami są Platformy Docker, MySQL i NGINX. Aby uzyskać pełną listę wtyczek wejściowych, zobacz sekcję **Konfiguracja dodatkowa.** 

Na koniec, aby agent zaczął używać nowej konfiguracji, wymuszamy agenta, aby zatrzymać i rozpocząć od uruchomienia następujących poleceń: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Teraz agent będzie zbierać metryki z każdej z określonych wtyczek wejściowych i emitować je do usługi Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Wykreślanie danych telegrafu w witrynie Azure portal 

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 

1. Przejdź do nowej karty **Monitor.** Następnie wybierz **metryki**.  

     ![Monitor - Metryki (wersja zapoznawcza)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Wybierz maszynę wirtualną w selektorze zasobów.

     ![Wykres metryczny](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Wybierz obszar nazw **Telegraf/CPU** i wybierz metrykę **usage_system.** Można filtrować według wymiarów na tej metryki lub podzielić na nich.  

     ![Wybieranie obszaru nazw i metryki](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Dodatkowa konfiguracja 

Poprzedni instruktaż zawiera informacje na temat konfigurowania agenta Telegraf do zbierania metryk z kilku podstawowych wtyczek wejściowych. Agent Telegraf obsługuje ponad 150 wtyczek wejściowych, z niektórymi obsługującymi dodatkowe opcje konfiguracji. InfluxData opublikował [listę obsługiwanych wtyczek](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) i [instrukcje, jak je skonfigurować.](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)  

Ponadto w tym instruktażu użyto agenta Telegraf do emitowania metryk dotyczących maszyny Wirtualnej, na której jest wdrożony agent. Agent Telegraf może być również używany jako moduł zbierający i przesyłający dalej metryki dla innych zasobów. Aby dowiedzieć się, jak skonfigurować agenta do emitowania metryk dla innych zasobów platformy Azure, zobacz [Niestandardowe dane wyjściowe metryki usługi Azure Monitor dla telegrafu](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Gdy nie są już potrzebne, można usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów dla maszyny wirtualnej i wybierz pozycję **Usuń**. Następnie potwierdź nazwę grupy zasobów do usunięcia. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](metrics-custom-overview.md).



