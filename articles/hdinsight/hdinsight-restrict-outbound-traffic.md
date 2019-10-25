---
title: Konfigurowanie ograniczenia wychodzącego ruchu sieciowego dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak skonfigurować ograniczenie ruchu sieciowego ruchu wychodzącego dla klastrów usługi Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 56e745a4f4e4bfbe82da00b46b7a5c0a58e3785e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789806"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Konfigurowanie wychodzącego ruchu sieciowego dla klastrów usługi Azure HDInsight przy użyciu zapory (wersja zapoznawcza)

W tym artykule przedstawiono procedurę zabezpieczania ruchu wychodzącego z klastra usługi HDInsight przy użyciu zapory platformy Azure. W poniższych krokach przyjęto założenie, że konfigurujesz zaporę platformy Azure dla istniejącego klastra. Jeśli wdrażasz nowy klaster i za zaporą, najpierw utwórz klaster usługi HDInsight i podsieć, a następnie wykonaj kroki opisane w tym przewodniku.

## <a name="background"></a>Tło

Klastry usługi Azure HDInsight są zwykle wdrażane we własnej sieci wirtualnej. Klaster ma zależności od usług spoza tej sieci wirtualnej, które wymagają dostępu do sieci w celu poprawnego działania.

Istnieje kilka zależności, które wymagają ruchu przychodzącego. Ruch przychodzący zarządzania nie może być wysyłany przez urządzenie zapory. Adresy źródłowe dla tego ruchu są znane i są publikowane w [tym miejscu](hdinsight-management-ip-addresses.md). Można również utworzyć reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) z tymi informacjami, aby zabezpieczyć ruch przychodzący do klastrów.

Zależności ruchu wychodzącego usługi HDInsight są prawie całkowicie zdefiniowane przy użyciu nazw FQDN, które nie zawierają statycznych adresów IP. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie mogą być używane do blokowania ruchu wychodzącego z klastra. Adresy zmieniają się często, ponieważ nie mogą ustawiać reguł na podstawie bieżącego rozpoznawania nazw i używania go do konfigurowania reguł sieciowej grupy zabezpieczeń.

Rozwiązanie służące do zabezpieczania adresów wychodzących polega na użyciu urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Zapora platformy Azure może ograniczyć wychodzący ruch HTTP i HTTPS na podstawie nazwy FQDN tagów lokalizacji docelowej lub [nazwy FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurowanie zapory platformy Azure z usługą HDInsight

Podsumowanie kroków służących do blokowania ruchu wychodzącego z istniejącej usługi HDInsight przy użyciu zapory platformy Azure:
1. Utwórz zaporę.
1. Dodawanie reguł aplikacji do zapory
1. Dodaj reguły sieci do zapory.
1. Utwórz tabelę routingu.

### <a name="create-a-new-firewall-for-your-cluster"></a>Tworzenie nowej zapory dla klastra

1. Utwórz podsieć o nazwie **AzureFirewallSubnet** w sieci wirtualnej, w której znajduje się klaster. 
1. Utwórz nową zaporę **test-FW01** , wykonując kroki opisane w [samouczku: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurowanie zapory przy użyciu reguł aplikacji

Utwórz kolekcję reguł aplikacji, która umożliwia klastrowi wysyłanie i odbieranie ważnej komunikacji.

Wybierz nową zaporę **test-FW01** z Azure Portal. Kliknij pozycję **reguły** w obszarze **Ustawienia**  > **kolekcje reguł aplikacji**  > **Dodawanie kolekcji reguł aplikacji**.

![Title: Dodawanie kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Na ekranie **Dodawanie kolekcji reguł aplikacji** wykonaj następujące czynności:

1. Wprowadź **nazwę**, **priorytet**i kliknij pozycję **Zezwól** z menu rozwijanego **Akcja** , a następnie wprowadź następujące reguły w **sekcji Tagi FQDN** :

   | **Nazwa** | **Adres źródłowy** | **Tag FQDN** | **Uwagi** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight i WindowsUpdate | Wymagane dla usług HDI Services |

1. Dodaj następujące reguły do **docelowej sekcji FQDN** :

   | **Nazwa** | **Adres źródłowy** | **Protokół: Port** | **Docelowe nazwy FQDN** | **Uwagi** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https: 443 | login.windows.net | Zezwala na działanie logowania systemu Windows |
   | Rule_3 | * | https: 443 | login.microsoftonline.com | Zezwala na działanie logowania systemu Windows |
   | Rule_4 | * | https: 443, http: 80 | < storage_account_name. blob. Core. Windows. net > | Jeśli klaster jest objęty WASB, Dodaj regułę do WASB. Aby korzystać tylko z połączeń HTTPS, upewnij się, że na koncie magazynu jest włączone [żądanie bezpiecznego transferu](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) . |

1. Kliknij pozycję **Add** (Dodaj).

   ![Title: Wprowadź szczegóły kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurowanie zapory przy użyciu reguł sieci

Utwórz reguły sieciowe w celu poprawnego skonfigurowania klastra usługi HDInsight.

1. Wybierz nową zaporę **test-FW01** z Azure Portal.
1. Kliknij pozycję **reguły** w obszarze **Ustawienia**  > **zbieranie reguł sieci**  > **Dodawanie kolekcji reguł sieci**.
1. Na ekranie **Dodawanie kolekcji reguł sieci** wprowadź **nazwę**i **Priorytet**, a następnie kliknij pozycję **Zezwól** z menu rozwijanego **Akcja** .
1. Utwórz następujące reguły w sekcji **adresy IP** :

   | **Nazwa** | **Protokół** | **Adres źródłowy** | **Adres docelowy** | **Port docelowy** | **Uwagi** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Czas usługi |
   | Rule_2 | Dowolne | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Jeśli używasz pakiet Enterprise Security (ESP), a następnie Dodaj regułę sieciową w sekcji adresy IP, która umożliwia komunikację z usługą AAD-DS dla klastrów ESP. Adresy IP kontrolerów domeny można znaleźć w sekcji AAD-DS w portalu | 
   | Rule_3 | TCP | * | Adres IP konta Data Lake Storage | `*` | Jeśli używasz Azure Data Lake Storage, możesz dodać regułę sieciową w sekcji adresy IP, aby rozwiązać problem SNI z ADLS Gen1 i Gen2. Ta opcja spowoduje kierowanie ruchu do zapory, co może spowodować zwiększenie kosztów ładowania dużych ilości danych, ale ruch zostanie zarejestrowany i przemonitorowany w dziennikach zapory. Określ adres IP dla konta Data Lake Storage. Możesz użyć polecenia programu PowerShell, takiego jak `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`, aby rozpoznać nazwę FQDN jako adres IP.|
   | Rule_4 | TCP | * | * | `12000` | Obowiązkowe Jeśli używasz Log Analytics, Utwórz regułę sieci w sekcji adresy IP, aby włączyć komunikację z obszarem roboczym Log Analytics. |

1. Utwórz następujące reguły w sekcji **Tagi usługi** :

   | **Nazwa** | **Protokół** | **Adres źródłowy** | **Tagi usług** | **Port docelowy** | **Uwagi** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Konfigurowanie reguły sieci w sekcji Tagi usług dla programu SQL, która umożliwia rejestrowanie i inspekcję ruchu SQL, chyba że skonfigurowano punkty końcowe usługi dla SQL Server w podsieci HDInsight, co spowoduje ominięcie zapory. |

1. Kliknij przycisk **Dodaj** , aby ukończyć tworzenie kolekcji reguł sieci.

   ![Title: wprowadzanie kolekcji reguł aplikacji](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Tworzenie i Konfigurowanie tabeli tras

Utwórz tabelę tras z następującymi wpisami:

1. Sześć adresów z [tej listy wymaganych adresów IP zarządzania usługą HDInsight](../hdinsight/hdinsight-management-ip-addresses.md) z następnym przeskokiem **Internetu**:
    1. Cztery adresy IP dla wszystkich klastrów we wszystkich regionach
    1. Dwa adresy IP specyficzne dla regionu, w którym jest tworzony klaster
1. Jedna trasa wirtualnego urządzenia dla adresu IP 0.0.0.0/0 z następnym przeskokiem jako prywatny adres IP zapory platformy Azure.

Na przykład aby skonfigurować tabelę tras dla klastra utworzonego w regionie US "środkowe stany USA", wykonaj następujące czynności:

1. Zaloguj się do Portalu Azure.
1. Wybierz test zapory platformy Azure **— FW01**. Skopiuj **prywatny adres IP** wymieniony na stronie **Przegląd** . Na potrzeby tego przykładu będziemy używać **przykładowego adresu 10.1.1.4**
1. Utwórz nową tabelę tras.
1. Kliknij pozycję **trasy** w obszarze **Ustawienia**.
1. Kliknij przycisk **Dodaj** , aby utworzyć trasy dla adresów IP w poniższej tabeli.

| Nazwa trasy | Prefiks adresu | Typ następnego skoku | Adres następnego skoku |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Nie dotyczy |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Nie dotyczy |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Nie dotyczy |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Nie dotyczy |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Nie dotyczy |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Nie dotyczy |
| 0.0.0.0 | 0.0.0.0/0 | Urządzenie wirtualne | 10.1.1.4 |

Ukończ konfigurację tabeli tras:

1. Przypisz utworzoną przez siebie tabelę tras do podsieci usługi HDInsight, klikając pozycję **podsieci** w obszarze **Ustawienia** , a następnie pozycję **Skojarz**.
1. Na ekranie **Skojarz podsieć** wybierz sieć wirtualną, w której został utworzony klaster, oraz **podsieć usługi HDInsight** użytą dla klastra usługi HDInsight.
1. Kliknij przycisk **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Ruch graniczny węzła lub aplikacji niestandardowej

Powyższe kroki pozwolą, aby klaster mógł działać bez problemów. W razie potrzeby nadal trzeba skonfigurować zależności, aby uwzględnić aplikacje niestandardowe działające w węzłach brzegowych.

Zależności aplikacji muszą być identyfikowane i dodane do zapory platformy Azure lub tabeli tras.

Trasy muszą zostać utworzone dla ruchu aplikacji, aby uniknąć problemów z routingiem asymetrycznym.

Jeśli aplikacje mają inne zależności, należy je dodać do zapory platformy Azure. Utwórz reguły aplikacji, aby umożliwić ruch HTTP/HTTPS i reguły sieciowe dla wszystkich innych elementów.

## <a name="logging-and-scale"></a>Rejestrowanie i skalowanie

Zapora platformy Azure może wysyłać dzienniki do kilku różnych systemów magazynowania. Aby uzyskać instrukcje dotyczące konfigurowania rejestrowania dla zapory, wykonaj kroki opisane w [samouczku: monitorowanie dzienników i metryk zapory platformy Azure](../firewall/tutorial-diagnostics.md).

Po zakończeniu konfiguracji rejestrowania, Jeśli rejestrujesz dane do Log Analytics, można wyświetlić zablokowany ruch z użyciem zapytania, takiego jak następujące:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrowanie zapory platformy Azure z dziennikami Azure Monitor jest przydatne podczas pierwszego uruchamiania aplikacji, gdy nie są znane wszystkie zależności aplikacji. Więcej informacji na temat dzienników Azure Monitor można znaleźć [w temacie Analizowanie danych dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md)

Aby dowiedzieć się więcej o granicach skalowania zapory platformy Azure i zwiększania żądań, zobacz [ten](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#azure-firewall-limits) dokument lub zapoznaj się z [często zadawanymi pytaniami](https://docs.microsoft.com/en-us/azure/firewall/firewall-faq). 

## <a name="access-to-the-cluster"></a>Dostęp do klastra
Po pomyślnym skonfigurowaniu zapory można użyć wewnętrznego punktu końcowego (`https://<clustername>-int.azurehdinsight.net`), aby uzyskać dostęp do Ambari z wewnątrz sieci wirtualnej. 

Aby korzystać z publicznego punktu końcowego (`https://<clustername>.azurehdinsight.net`) lub punktu końcowego SSH (`<clustername>-ssh.azurehdinsight.net`), upewnij się, że masz odpowiednie trasy w tabeli tras i reguły sieciowej grupy zabezpieczeń, aby uniknąć opisanego [tutaj](https://docs.microsoft.com/azure/firewall/integrate-lb)problemu z routingiem assymetric. W tym przypadku należy zezwolić na adres IP klienta w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego, a także dodać go do tabeli tras zdefiniowanych przez użytkownika z następnym przeskokiem ustawionym jako `internet`. Jeśli to ustawienie nie zostanie prawidłowo skonfigurowane, zostanie wyświetlony komunikat o błędzie limitu czasu.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurowanie innego sieciowego urządzenia wirtualnego

>[!Important]
> Poniższe informacje są wymagane **tylko** wtedy, gdy chcesz skonfigurować wirtualne urządzenie sieciowe (urządzenie WUS) inne niż Zapora platformy Azure.

Poprzednie instrukcje ułatwiają skonfigurowanie zapory platformy Azure pod kątem ograniczenia ruchu wychodzącego z klastra usługi HDInsight. Zapora platformy Azure jest automatycznie konfigurowana w taki sposób, aby zezwalała na ruch dla wielu typowych scenariuszy. Jeśli chcesz użyć innego sieciowego urządzenia wirtualnego, musisz ręcznie skonfigurować szereg dodatkowych funkcji. Należy pamiętać o następujących kwestiach dotyczących konfigurowania wirtualnego urządzenia sieciowego:

* Usługi obsługujące punkt końcowy usługi należy skonfigurować za pomocą punktów końcowych usługi.
* Zależności adresów IP są związane z ruchem innym niż HTTP/S (ruchem TCP i UDP).
* Punkty końcowe HTTP/HTTPS w nazwie FQDN można umieścić na urządzeniu urządzenie WUS.
* Symbole wieloznaczne protokołu HTTP/HTTPS są zależnościami, które mogą się różnić w zależności od liczby kwalifikatorów.
* Przypisz tabelę tras utworzoną w podsieci usługi HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Zależności obsługujące punkt końcowy usługi

| **Punktu końcowego** |
|---|
| Azure SQL |
| Azure Storage |
| Usługa Active Directory systemu Azure |

#### <a name="ip-address-dependencies"></a>Zależności adresów IP

| **Punktu końcowego** | **Szczegóły** |
|---|---|
| \*:123 | Sprawdzanie zegara NTP. Ruch jest sprawdzany w wielu punktach końcowych na porcie 123 |
| Adresy IP opublikowane w [tym miejscu](hdinsight-management-ip-addresses.md) | To są usługi HDInsight |
| AAD — prywatne adresy IP DS dla klastrów ESP |
| \*:16800 dla aktywacji usługi KMS systemu Windows |
| \*12000 Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Zależności HTTP/HTTPS w nazwie FQDN

>[!Important]
> Poniższa lista zawiera tylko kilka najważniejszych nazw FQDN. Aby skonfigurować urządzenie WUS [w tym pliku](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json), można uzyskać pełną listę nazw FQDN.

| **Punktu końcowego**                                                          |
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

## <a name="next-steps"></a>Następne kroki

* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
