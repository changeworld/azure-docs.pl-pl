---
title: Skonfiguruj ograniczenia ruchu sieciowego ruchu wychodzącego w przypadku klastrów Azure HDInsight
description: Dowiedz się, jak skonfigurować ograniczenie ruchu sieciowego ruchu wychodzącego w przypadku klastrów Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/13/2019
ms.openlocfilehash: 44b6f099b5b17329976b9fec3c0ac38b5e394221
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978011"
---
# <a name="configure-outbound-network-traffic-restriction-for-azure-hdinsight-clusters-preview"></a>Skonfiguruj ograniczenia ruchu sieciowego ruchu wychodzącego w przypadku klastrów Azure HDInsight (wersja zapoznawcza)

Ten artykuł zawiera instrukcje umożliwiające bezpieczny ruch wychodzący z klastra usługi HDInsight przy użyciu zapory platformy Azure. W poniższych krokach przyjęto, konfigurowania zapory platformy Azure do istniejącego klastra. Jeżeli wdrażasz nowy klaster za zaporą, najpierw Tworzenie klastra HDInsight i podsieci i następnie wykonaj kroki opisane w tym przewodniku.

## <a name="background"></a>Tło

Klastry usługi Azure HDInsight są zwykle wdrażane w sieci wirtualnej. Klaster ma zależności od usługi spoza tej sieci wirtualnej, które wymagają dostępu do sieci, aby działać prawidłowo.

Istnieją pewne zależności, które wymagają ruchu przychodzącego. Nie można wysłać ruchu przychodzącego zarządzania za pomocą urządzenia zapory. Źródłowe adresy dla tego ruchu są znane i są publikowane [tutaj](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Dzięki tym informacjom można zabezpieczyć ruch przychodzący do klastrów, można również tworzyć reguły sieciowej grupy zabezpieczeń (NSG).

Zależności ruch wychodzący HDInsight prawie całkowicie są definiowane przy użyciu nazw FQDN, które nie mają statyczne adresy IP spodem. Brak statycznych adresów oznacza, że sieciowe grupy zabezpieczeń (NSG) nie można zablokować ruch wychodzący z klastra. Zmianie adresów wystarczająco często nie jeden skonfigurować reguły oparte na bieżącym rozpoznawania nazw i używać go do skonfigurowania reguły sieciowej grupy zabezpieczeń.

Rozwiązania do zabezpieczania adresy ruchu wychodzącego jest korzystanie z urządzenia zapory, który można kontrolować ruch wychodzący na podstawie nazw domen. Zaporę platformy Azure można ograniczyć ruchu wychodzącego ruchu HTTP i HTTPS na podstawie nazwy FQDN docelowego lub [tagów w pełni kwalifikowaną nazwę domeny](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurowanie zapory platformy Azure za pomocą HDInsight

Podsumowanie kroki, aby zablokować ruch wychodzący z usługi HDInsight istniejących za pomocą zapory usługi Azure są następujące:
1. Włączanie punktów końcowych usługi.
1. Tworzenie zapory.
1. Dodawanie reguły aplikacji do zapory
1. Dodawanie reguł sieci do zapory.
1. Tworzenie tabeli routingu.

### <a name="enable-service-endpoints"></a>Włączanie punktów końcowych usługi

Jeśli chcesz pominąć zapory (np. oszczędności kosztów na transfer danych), a następnie można włączyć punktów końcowych usługi dla języków SQL i magazynu w podsieci usługi HDInsight. W przypadku usługi włączonych punktów końcowych do bazy danych SQL Azure wszelkie zależności usługi Azure SQL, które ma klastra musi być skonfigurowany z również punktami końcowymi usługi.

Aby włączyć punkty końcowe usługi prawidłowe, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal i wybierz sieć wirtualną wdrożoną w klastrze usługi HDInsight.
1. Wybierz **podsieci** w obszarze **ustawienia**.
1. Wybierz podsieć, w którym jest wdrażany klaster.
1. Na ekranie, aby edytować ustawienia podsieci, kliknij przycisk **Microsoft.SQL** i/lub **Microsoft.Storage** z **punkty końcowe usługi**  >   **Usługi** polu listy rozwijanej.
1. Jeśli używasz klastra ESP, a następnie należy również zaznaczyć **Microsoft.AzureActiveDirectory** punktu końcowego usługi.
1. Kliknij pozycję **Zapisz**.

### <a name="create-a-new-firewall-for-your-cluster"></a>Tworzenie nowej zapory dla klastra

1. Utwórz podsieć o nazwie **AzureFirewallSubnet** w sieci wirtualnej, gdy istnieje klastra. 
1. Tworzenie nowej zapory **FW01 testu** wykonując kroki w [samouczka: Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu witryny Azure portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).
1. Wybierz Nowa Zapora w witrynie Azure portal. Kliknij przycisk **reguły** w obszarze **ustawienia** > **kolekcji reguł aplikacji** > **dodać kolekcję reguł aplikacji**.

    ![Tytuł: Dodawanie kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurowanie zapory przy użyciu reguł aplikacji

Utwórz kolekcję reguł aplikacji, która umożliwia klastrowi wysyłać i odbierać wiadomości ważne.

Wybierz pozycję Nowa Zapora **FW01 testu** w witrynie Azure portal. Kliknij przycisk **reguły** w obszarze **ustawienia** > **kolekcji reguł aplikacji** > **dodać kolekcję reguł aplikacji**.

Na **dodać kolekcję reguł aplikacji** ekranu, wykonaj następujące czynności:

1. Wprowadź **nazwa**, **priorytet**i kliknij przycisk **Zezwalaj** z **akcji** menu rozwijanego.
1. Dodaj następujące zasady:
    1. Reguła zezwalająca na ruch HDInsight i Windows Update:
        1. W **tagów w pełni kwalifikowaną nazwę domeny** sekcji, podaj **nazwa**i ustaw **źródłowych adresów** do `*`.
        1. Wybierz **HDInsight** i **Windows Update** z **tagów w pełni kwalifikowaną nazwę domeny** menu rozwijanego.
    1. Reguła zezwalająca na działanie logowania Windows:
        1. W **nazw FQDN docelowego** sekcji, podaj **nazwa**i ustaw **źródłowych adresów** do `*`.
        1. Wprowadź `https:443` w obszarze **protokołu: Port** i `login.windows.net` w obszarze **docelowej nazwy FQDN**.
    1. Reguła zezwalająca na telemetrii SQM:
        1. W **nazw FQDN docelowego** sekcji, podaj **nazwa**i ustaw **źródłowych adresów** do `*`.
        1. Wprowadź `https:443` w obszarze **protokołu: Port** i `sqm.telemetry.microsoft.com` w obszarze **docelowej nazwy FQDN**.
    1. Jeśli nie używasz powyższe punkty końcowe usługi klastra jest wspierana przez WASB, Dodaj regułę dla WASB:
        1. W **nazw FQDN docelowego** sekcji, podaj **nazwa**i ustaw **źródłowych adresów** do `*`.
        1. Wprowadź `http` lub [https] w zależności od Jeśli używasz wasb: / / lub wasbs: / / w obszarze **protokołu: Port** i adres url konta magazynu w ramach **nazw FQDN docelowego**.
1. Kliknij pozycję **Add** (Dodaj).

![Tytuł: Wprowadź szczegóły kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurowanie zapory przy użyciu reguł sieci

Tworzenie reguł sieci, aby poprawnie skonfigurować klastra usługi HDInsight.

> [!Important]
> Możesz wybrać między za pomocą tagów usługi SQL na zaporze, używając reguł sieci, zgodnie z opisem w tej sekcji lub SQL usługi punktu końcowego, co opisano w [sekcji na punkty końcowe usługi](#enable-service-endpoints). Jeśli wybierzesz opcję użycia znaczników SQL w regułach sieciowych, możesz zarejestrować błąd i inspekcji ruchu SQL. Przy użyciu punktu końcowego usługi będzie mieć ruch SQL ominąć zaporę.

1. Wybierz pozycję Nowa Zapora **FW01 testu** w witrynie Azure portal.
1. Kliknij przycisk **reguły** w obszarze **ustawienia** > **sieci kolekcji reguł** > **dodać kolekcję reguł sieci**.
1. Na **dodać kolekcję reguł sieci** ekranu, należy wprowadzić **nazwa**, **priorytet**i kliknij przycisk **Zezwalaj** z **akcji** menu rozwijanego.
1. Utworzenie następujących reguł:
    1. Reguła sieci, która umożliwia klastrowi Przeprowadź synchronizację zegara przy użyciu NTP.
        1. W **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **adresów źródłowych** i **adresów docelowych** do `*`.
        1. Ustaw **porty docelowe** do 123.
    1. Jeśli używasz pakietu zabezpieczeń przedsiębiorstwa (ESP), następnie dodać regułę sieciowej, która umożliwia komunikację za pomocą usługi AAD DS ESP klastrów.
        1. Określ dwa adresy IP dla kontrolerów domeny.
        1. W kolejnym wierszu w **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **źródłowych adresów** `*`.
        1. Wprowadź wszystkie adresy IP kontrolerów domeny w **adresów docelowych** rozdzielonych przecinkami.
        1. Ustaw **porty docelowe** do `*`.
    1. Jeśli używasz usługi Azure Data Lake Storage, można dodać regułę sieciowej do rozwiązania problemu SNI ADLS Gen1 i Gen2. Ta opcja będzie kierować ruch do zapory, która może prowadzić do wyższych kosztów dla dużej ilości danych, ale ruch będzie rejestrowane i inspekcji.
        1. Określ adres IP dla swojego konta usługi Data Lake Storage. Można użyć polecenia programu powershell, takie jak `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` rozpoznać nazwę FQDN jako adres IP.
        1. W kolejnym wierszu w **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **źródłowych adresów** `*`.
        1. Wprowadź adres IP konta magazynu w **adresów docelowych**.
        1. Ustaw **porty docelowe** do `*`.
    1. Regułę sieciowej w celu umożliwienia komunikacji z usługi zarządzania klucza dla Windows aktywacji.
        1. W kolejnym wierszu w **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **źródłowych adresów** `*`.
        1. Ustaw **adresów docelowych** do `*`.
        1. Ustaw **porty docelowe** do `1688`.
    1. Jeśli używasz usługi Log Analytics, następnie należy utworzyć regułę sieciowej w celu umożliwienia komunikacji z obszaru roboczego usługi Log Analytics.
        1. W kolejnym wierszu w **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **źródłowych adresów** `*`.
        1. Ustaw **adresów docelowych** do `*`.
        1. Ustaw **porty docelowe** do `12000`.
    1. Skonfiguruj tag usługi SQL Server, który umożliwi logowania i inspekcji ruchu SQL.
        1. W kolejnym wierszu w **reguły** sekcji, podaj **nazwa** i wybierz **wszelkie** z **protokołu** listy rozwijanej.
        1. Ustaw **źródłowych adresów** `*`.
        1. Ustaw **adresów docelowych** do `*`.
        1. Wybierz **Sql** z **tagi usługi** listy rozwijanej.
        1. Ustaw **porty docelowe** do `1433,11000-11999,14000-14999`.
1. Kliknij przycisk **Dodaj** wymagana do ukończenia tworzenia Twojej kolekcji reguł sieci.

![Tytuł: Wprowadź szczegóły kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Tworzenie i konfigurowanie tabelę tras

Utwórz tabelę tras z następujących pozycji:

1. Siedem adresów z [tę listę wymaganych adresów IP zarządzania HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) z następnym przeskokiem **Internet**:
    1. Cztery adresy IP dla wszystkich klastrów we wszystkich regionach
    1. Dwa adresy IP, które są specyficzne dla regionu, w którym został utworzony klaster
    1. Jeden adres IP dla platformy Azure, cyklicznego programu rozpoznawania nazw
1. Jedna trasa urządzenie wirtualne dla adresu IP adresu 0.0.0.0/0 za pomocą następnego przeskoku jest prywatny adres IP swojej zapory usługi Azure.

Na przykład aby skonfigurować tabeli tras dla klastra, utworzone w regionie USA "Środkowe stany USA", należy użyć poniższe czynności:

1. Zaloguj się do Portalu Azure.
1. Wybierz pozycję Azure zapory **FW01 testu**. Kopiuj **prywatny adres IP** na **Przegląd** strony. W tym przykładzie użyjemy **przykładowy adres 10.1.1.4**
1. Utwórz nową tabelę tras.
1. Kliknij przycisk **trasy** w obszarze **ustawienia**.
1. Kliknij przycisk **Dodaj** można utworzyć trasy dla adresów IP w poniższej tabeli.

| Nazwa trasy | Prefiks adresu | Typ następnego skoku | Adres następnego przeskoku |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Nie dotyczy |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Nie dotyczy |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Nie dotyczy |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Nie dotyczy |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Nie dotyczy |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Nie dotyczy |
| 168.63.129.16 | 168.63.129.16/32 | Internet | Nie dotyczy |
| 0.0.0.0 | 0.0.0.0/0 | Urządzenie wirtualne | 10.1.1.4 |

![Tytuł: Tworzenie tabeli tras](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-route-table.png)

Zakończ konfigurację tabeli tras:

1. Przypisz tabeli tras podsieci usługi HDInsight utworzony przez kliknięcie przycisku **podsieci** w obszarze **ustawienia** i następnie **skojarzyć**.
1. Na **Skojarz podsieć** ekranu, wybierz sieć wirtualną, że klaster został utworzony w i **AzureFirewallSubnet** utworzone do użytku z zaporą.
1. Kliknij przycisk **OK**.

![Tytuł: Tworzenie tabeli tras](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-route-table-associate-subnet.png)

## <a name="edge-node-application-traffic"></a>Ruch aplikacji węzła brzegowego

Powyższych czynności umożliwi klastra aby działać bez problemów. Nadal należy skonfigurować zależności, aby uwzględnić niestandardowe aplikacje uruchomione w węzłach brzegowych, jeśli ma to zastosowanie.

Zależności aplikacji, należy zidentyfikować i dodane do tabeli tras lub zapory usługi Azure.

Trasy musi zostać utworzony dla ruchu aplikacji uniknąć problemów z routingu asymetrycznego.

Jeśli aplikacje mają inne zależności, muszą można dodać do zapory platformy Azure. Tworzenie reguł aplikacji, które zezwalają na ruch HTTP/HTTPS i reguł sieciowych dla wszystkich innych.

## <a name="logging"></a>Rejestrowanie

Zaporę platformy Azure umożliwia wysyłanie dzienników do kilku systemów do innego magazynu. Instrukcje dotyczące konfigurowania rejestrowania dla zapory, postępuj zgodnie z instrukcjami w [samouczka: Monitoruj dzienniki zapory platformy Azure i metryk](../firewall/tutorial-diagnostics.md).

Po zakończeniu instalacji rejestrowania, jeśli dane logowania do usługi Log Analytics możesz wyświetlić zablokowanego ruchu z zapytaniem, takie jak następujące:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrowanie zapory platformy Azure przy użyciu dzienników usługi Azure Monitor jest przydatne w przypadku, gdy najpierw pobierania aplikacji działać po użytkownik nie rozpoznają wszystkie zależności aplikacji. Dowiedz się więcej na temat dzienników usługi Azure Monitor z [Analizuj dane dzienników w usłudze Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="configure-another-network-virtual-appliance"></a>Skonfiguruj inną wirtualnego urządzenia sieciowego

>[!Important]
> Poniżej przedstawiono informacje **tylko** wymagane, jeśli chcesz skonfigurować wirtualnego urządzenia sieciowego (WUS) niż Zapora usługi Azure.

Poprzednich instrukcji pomocne w konfigurowaniu zapory usługi Azure w celu ograniczania ruchu wychodzącego z klastra usługi HDInsight. Zaporę platformy Azure jest automatycznie konfigurowany zezwalająca na ruch dla wielu typowych scenariuszy ważne. Jeśli chcesz użyć innego wirtualnego urządzenia sieciowego, należy ręcznie skonfigurować szereg dodatkowych funkcji. Pamiętać o następujących jako swojej skonfigurować wirtualne urządzenie sieciowe:

* Punkty końcowe usługi należy skonfigurować usługi zdolne do punktu końcowego usługi.
* Adres IP zależności są przeznaczone dla ruchu innego niż do protokołu HTTP/S (ruch TCP i UDP).
* Punktów końcowych HTTP/HTTPS nazwy FQDN można umieścić w urządzeniu WUS.
* Symbol wieloznaczny HTTP/HTTPS punkty końcowe są zależności, które mogą się różnić na podstawie liczby kwalifikatorów.
* Przypisz tabeli tras, które tworzysz do podsieci usługi HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Zależności zdolne do punktu końcowego usługi

| **Punkt końcowy** |
|---|
| Azure SQL |
| Azure Storage |
| Usługa Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Adres IP zależności

| **Punkt końcowy** | **Szczegóły** |
|---|---|
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na port 123 |
| Opublikowane adresy IP [tutaj](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Są to usługi HDInsight |
| Klastry usługi AAD DS prywatnych adresów IP dla ESP |
| \*: 16800 aktywacji Windows usługi zarządzania Kluczami |
| \*12000 usługi Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Zależności w pełni kwalifikowaną nazwę domeny HTTP/HTTPS

>[!Important]
> Poniższa lista zawiera tylko kilka najważniejszych nazwy FQDN. Można uzyskać pełną listę nazw FQDN konfigurowania Twoje urządzenie WUS [w tym pliku](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Punkt końcowy**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Kolejne kroki

* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
