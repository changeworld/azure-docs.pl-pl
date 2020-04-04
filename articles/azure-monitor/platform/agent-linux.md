---
title: Łączenie komputerów z systemem Linux z monitorem platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób łączenia komputerów z systemem Linux hostowanych w innych chmurach lub lokalnie z usługą Azure Monitor za pomocą agenta usługi Log Analytics dla systemu Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637517"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Łączenie komputerów z systemem Linux z monitorem platformy Azure

Aby monitorować maszyny wirtualne lub komputery fizyczne w lokalnym centrum danych lub innym środowisku chmury i zarządzać nimi za pomocą usługi Azure Monitor, należy wdrożyć agenta usługi Log Analytics i skonfigurować go do raportowania w obszarze roboczym usługi Log Analytics. Agent obsługuje również rolę proces roboczy hybrydowego dla usługi Azure Automation.

Agent usługi Log Analytics dla systemu Linux można zainstalować przy użyciu jednej z następujących metod. Szczegółowe informacje na temat korzystania z każdej metody są podane w dalszej części artykułu.

* [Ręcznie pobierz i zainstaluj](#install-the-agent-manually) agenta. Jest to wymagane, gdy komputer z systemem Linux nie ma dostępu do Internetu i będzie komunikować się z usługą Azure Monitor lub usługa Azure Automation za pośrednictwem [bramy analizy dzienników.](gateway.md) 
* [Zainstaluj agenta dla systemu Linux przy użyciu skryptu otoki](#install-the-agent-using-wrapper-script) hostowanego w usłudze GitHub. Jest to zalecana metoda instalowania i uaktualniania agenta, gdy komputer ma łączność z Internetem, bezpośrednio lub za pośrednictwem serwera proxy.

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Linux](log-analytics-agent.md#supported-linux-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-agent.md#network-requirements).

>[!NOTE]
>Agenta usługi Log Analytics dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics. Można go skonfigurować tylko do raportowania zarówno do grupy zarządzania programu System Center Operations Manager, jak i do obszaru roboczego usługi Log Analytics jednocześnie lub do pojedynczego.

## <a name="agent-install-package"></a>Pakiet instalacyjny agenta

Agent analizy dzienników dla systemu Linux składa się z wielu pakietów. Plik wydania zawiera następujące pakiety, które są dostępne `--extract` po uruchomieniu pakietu powłoki z parametrem:

**Pakiet** | **Wersja** | **Opis**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agent analizy dzienników dla systemu Linux
omsconfig | 1.1.1 | Agent konfiguracji agenta usługi Log Analytics
Omi | 1.6.3 | Otwórz infrastrukturę zarządzania (OMI) — lekki serwer CIM. *Należy zauważyć, że OMI wymaga dostępu administratora do uruchomienia zadania cron niezbędnego do funkcjonowania usługi*
Scx | 1.6.3 | Dostawcy OMI CIM dla metryk wydajności systemu operacyjnego
apache-cimprov | 1.0.1 | Apache DOSTAWCA MONITOROWANIA WYDAJNOŚCI SERWERA HTTP dla OMI. Zainstalowany tylko wtedy, gdy zostanie wykryty serwer HTTP Apache.
mysql-cimprov | 1.0.1 | Dostawca monitorowania wydajności serwera MySQL dla OMI. Zainstalowany tylko wtedy, gdy zostanie wykryty serwer MySQL/MariaDB.
docker-cimprov | 1.0.0 | Dostawca platformy Docker dla OMI. Zainstalowany tylko wtedy, gdy zostanie wykryty program Docker.

### <a name="agent-installation-details"></a>Szczegóły instalacji agenta

Po zainstalowaniu agenta usługi Log Analytics dla pakietów systemu Linux zostaną zastosowane następujące dodatkowe zmiany konfiguracji dla całego systemu. Te artefakty są usuwane po odinstalowaniu pakietu omsagent.

* Tworzony jest użytkownik nieuprzywilejowany o nazwie: `omsagent` . Demon działa w ramach tego poświadczenia. 
* Sudoers *plik dołączany* `/etc/sudoers.d/omsagent`jest tworzony w . Upoważnia `omsagent` to do ponownego uruchomienia demonów syslog i omsagent. Jeśli sudo *zawierają* dyrektywy nie są obsługiwane w zainstalowanej wersji sudo, `/etc/sudoers`te wpisy zostaną zapisane do .
* Konfiguracja syslog jest modyfikowana w celu przekazania podzbioru zdarzeń do agenta. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zbierania danych Syslog](data-sources-syslog.md).

Na monitorowanym komputerze z systemem Linux `omsagent`agent jest wymieniony jako . `omsconfig`jest agentem usługi Log Analytics dla agenta konfiguracji systemu Linux, który szuka nowej konfiguracji po stronie portalu co 5 minut. Nowa i zaktualizowana konfiguracja jest stosowana do `/etc/opt/microsoft/omsagent/conf/omsagent.conf`plików konfiguracyjnych agenta znajdujących się w programie .

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego

Przed zainstalowaniem agenta usługi Log Analytics dla systemu Linux potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics. Te informacje są wymagane podczas konfigurowania agenta, aby poprawnie skonfigurować go i upewnić się, że może pomyślnie komunikować się z usługą Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź **usługa Log Analytics**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz **obszary robocze usługi Log Analytics**.

2. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy utworzony wcześniej. (Być może nazwałeś ją **DefaultLAWorkspace).)**

3. Wybierz **ustawienia zaawansowane:**

    ![Menu Ustawienia zaawansowane dla usługi Log Analytics w witrynie Azure portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Wybierz **Połączone źródła**, a następnie **Serwery Linux**.

5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej obie wartości do ulubionego edytora.

## <a name="install-the-agent-manually"></a>Ręczna instalacja agenta

Agent analizy dzienników dla systemu Linux jest dostarczany w samorozdymniającym się i instalowalnym pakiecie skryptów powłoki. Ten pakiet zawiera pakiety Debiana i RPM dla każdego ze składników agenta i może być zainstalowany bezpośrednio lub wyodrębniony w celu pobrania poszczególnych pakietów. Jeden pakiet jest przewidziany dla architektury x64 i jeden dla architektur x86. 

> [!NOTE]
> Dla maszyn wirtualnych platformy Azure zaleca się zainstalowanie agenta na nich przy użyciu [rozszerzenia maszyny Wirtualnej usługi Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) dla systemu Linux. 

1. [Pobierz](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) i przenieś odpowiedni pakiet (x64 lub x86) na maszynę wirtualną z systemem Linux lub komputer fizyczny, używając scp/sftp.

2. Zainstaluj pakiet przy `--install` użyciu argumentu. Aby poniżej obszaru roboczego usługi Log `-w <WorkspaceID>` Analytics `-s <workspaceKey>` podczas instalacji, podaj parametry i parametry skopiowane wcześniej.

    >[!NOTE]
    >Należy użyć argumentu, `--upgrade` jeśli zainstalowane są wszystkie pakiety zależne, takie jak omi, scx, omsconfig lub ich starsze wersje, tak jak w przypadku, gdy agent programu Operacyjnego Centrum systemu dla systemu Linux jest już zainstalowany. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Aby skonfigurować agenta systemu Linux do instalowania i łączenia się z obszarem roboczym usługi Log Analytics za pośrednictwem bramy usługi Log Analytics, uruchom następujące polecenie, podając parametry serwera proxy, identyfikatora obszaru roboczego i klucza obszaru roboczego. Tę konfigurację można określić w `-p [protocol://][user:password@]proxyhost[:port]`wierszu polecenia, dołączając plik . Właściwość *serwera proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera bramy usługi Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Jeśli uwierzytelnianie jest wymagane, należy określić nazwę użytkownika i hasło. Przykład: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym usługi Log Analytics w chmurze platformy Azure dla instytucji rządowych, uruchom następujące polecenie, podając identyfikator obszaru roboczego i klucz podstawowy skopiowany wcześniej.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Jeśli chcesz zainstalować pakiety agenta i skonfigurować je do raportowania do określonego obszaru roboczego usługi Log Analytics w późniejszym czasie, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Jeśli chcesz wyodrębnić pakiety agenta z pakietu bez instalowania agenta, uruchom następujące polecenie:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instalowanie agenta przy użyciu skryptu otoki

Poniższe kroki konfigurują konfigurację agenta usługi Log Analytics na platformie Azure i w chmurze azure dla instytucji rządowych przy użyciu skryptu otoki dla komputerów z systemem Linux, który może komunikować się bezpośrednio lub za pośrednictwem serwera proxy, aby pobrać agenta hostowanego w usłudze GitHub i zainstalować agenta.  

Jeśli komputer z systemem Linux musi komunikować się za pośrednictwem serwera proxy do `-p [protocol://][user:password@]proxyhost[:port]`usługi Log Analytics, tę konfigurację można określić w wierszu polecenia, dołączając program . Właściwość *protokołu* `http` akceptuje `https`lub , a właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://proxy01.contoso.com:30443`

Jeśli uwierzytelnianie jest wymagane w obu przypadkach, należy określić nazwę użytkownika i hasło. Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym usługi Log Analytics, uruchom następujące polecenie podając identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera `-p` parametr serwera proxy i przykład składnię, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym usługi Log Analytics w chmurze platformy Azure dla instytucji rządowych, uruchom następujące polecenie podając identyfikator obszaru roboczego i klucz podstawowy skopiowany wcześniej. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera `-p` parametr serwera proxy i przykład składnię, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Uaktualnienie z poprzedniej wersji

Uaktualnianie z poprzedniej wersji, począwszy od wersji 1.0.0-47, jest obsługiwane w każdej wersji. Wykonaj instalację `--upgrade` z parametrem, aby uaktualnić wszystkie składniki agenta do najnowszej wersji.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [zarządzanie i utrzymywanie agenta usługi Log Analytics dla systemów Windows i Linux,](agent-manage.md) aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Przejrzyj [rozwiązywanie problemów z agentem systemu Linux,](agent-linux-troubleshoot.md) jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.
