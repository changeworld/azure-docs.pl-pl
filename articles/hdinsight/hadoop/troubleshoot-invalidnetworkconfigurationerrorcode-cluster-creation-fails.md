---
title: Błąd invalidNetworkConfigurationErrorCode — usługa Azure HDInsight
description: Różne przyczyny nieudanych tworzenia klastra z invalidNetworkConfigurationErrorCode w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720388"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Tworzenie klastra kończy się niepowodzeniem dzięki kodowi InvalidNetworkConfigurationErrorCode w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

Jeśli zostanie wyświetlony `InvalidNetworkConfigurationErrorCode` kod błędu z opisem "Konfiguracja sieci wirtualnej nie jest zgodna z wymaganiem hdinsight", zwykle wskazuje na problem z [konfiguracją sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) dla klastra. Na podstawie pozostałej części opisu błędu wykonaj poniższe sekcje, aby rozwiązać problem.

## <a name="hostname-resolution-failed"></a>"Rozpoznawanie nazwy hosta nie powiodło się"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "Rozpoznawanie nazwy hosta nie powiodło się".

### <a name="cause"></a>Przyczyna

Ten błąd wskazuje na problem z niestandardową konfiguracją DNS. Serwery DNS w sieci wirtualnej mogą przesyłać dalej kwerendy DNS do cyklicznych programów rozpoznawania nazw platformy Azure w celu rozpoznawania nazw hostów w tej sieci wirtualnej (szczegółowe informacje można znaleźć [w funkcji Rozpoznawania nazw w sieciach wirtualnych).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Dostęp do cyklicznych programów rozpoznawania nazw platformy Azure jest zapewniany za pośrednictwem wirtualnego adresu IP 168.63.129.16. Ten adres IP jest dostępny tylko z maszyn wirtualnych platformy Azure. Nie będzie więc działać, jeśli używasz serwera DNS OnPrem lub serwer DNS jest maszyną wirtualną platformy Azure, która nie jest częścią sieci wirtualnej klastra.

### <a name="resolution"></a>Rozwiązanie

1. Ssh do maszyny Wirtualnej, która jest częścią `hostname -f`klastra, i uruchom polecenie . Spowoduje to zwrócenie w pełni kwalifikowanej nazwy `<host_fqdn>` domeny hosta (określanej jako poniższe instrukcje).

1. Następnie uruchom polecenie `nslookup <host_fqdn>` (na `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`przykład ). Jeśli to polecenie rozpoznaje nazwę adresu IP, oznacza to, że serwer DNS działa poprawnie. W takim przypadku należy zgłosić sprawę pomocy technicznej w przypadku aplikacji HDInsight, a my zbadamy twój problem. W przypadku pomocy technicznej należy uwzględnić kroki rozwiązywania problemów wykonane. Pomoże nam to szybciej rozwiązać problem.

1. Jeśli powyższe polecenie nie zwraca adresu IP, uruchom `nslookup <host_fqdn> 168.63.129.16` `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`(na przykład ). Jeśli to polecenie jest w stanie rozpoznać adres IP, oznacza to, że serwer DNS nie przekazuje kwerendy do usługi DNS platformy Azure lub nie jest maszyną wirtualną, która jest częścią tej samej sieci wirtualnej co klaster.

1. Jeśli nie masz maszyny Wirtualnej platformy Azure, która może działać jako niestandardowy serwer DNS w sieci wirtualnej klastra, należy najpierw dodać tę maszynę wirtualną. Utwórz maszynę wirtualną w sieci wirtualnej, która zostanie skonfigurowana jako usługa przesyłania dalej DNS.

1. Po wdrożeniu maszyny Wirtualnej w sieci wirtualnej skonfiguruj reguły przekazywania dns na tej maszynie wirtualnej. Prześlij wszystkie żądania rozpoznawania nazw iDNS do wersji 168.63.129.16, a resztę do serwera DNS. [Oto](../hdinsight-plan-virtual-network-deployment.md) przykład tej konfiguracji niestandardowego serwera DNS.

1. Dodaj adres IP tej maszyny Wirtualnej jako pierwszy wpis DNS dla konfiguracji DNS sieci wirtualnej.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nie można połączyć się z kontem usługi Azure Storage"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "Nie można połączyć się z kontem usługi Azure Storage" lub "Nie można połączyć się z usługą Azure SQL".

### <a name="cause"></a>Przyczyna

Usługa Azure Storage i SQL nie mają stałych adresów IP, więc musimy zezwolić na połączenia wychodzące do wszystkich adresów IP, aby umożliwić dostęp do tych usług. Dokładne kroki rozwiązywania problemów zależą od tego, czy skonfigurowano grupę zabezpieczeń sieciowych (NSG), czy reguły zdefiniowane przez użytkownika (UDR). Szczegółowe informacje na temat tych konfiguracji można znaleźć w sekcji [dotyczącej kontrolowania ruchu sieciowego za pomocą funkcji HDInsight za pomocą grup zabezpieczeń sieciowych i tras zdefiniowanych przez użytkownika.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)

### <a name="resolution"></a>Rozwiązanie

* Jeśli klaster używa [sieciowej grupy zabezpieczeń .](../../virtual-network/virtual-network-vnet-plan-design-arm.md)

    Przejdź do witryny Azure portal i zidentyfikować sieciowej grupy sieciowej, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **Reguły zabezpieczeń wychodzących** zezwalaj na dostęp wychodzący do Internetu bez ograniczeń (pamiętaj, że mniejszy numer **priorytetu** oznacza wyższy priorytet). Ponadto w sekcji **podsieci** upewnij się, czy ta grupa sieciowa jest stosowana do podsieci klastra.

* Jeśli klaster używa [trasy zdefiniowane przez użytkownika (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Przejdź do witryny Azure portal i zidentyfikuj tabelę tras skojarzoną z podsiecią, w której jest wdrażany klaster. Po znalezieniu tabeli tras dla podsieci sprawdź w niej sekcję **tras.**

    Jeśli zdefiniowano trasy, upewnij się, że istnieją trasy dla adresów IP dla regionu, w którym wdrożono klaster, a **NextHopType** dla każdej trasy to **Internet**. Dla każdego wymaganego adresu IP, opisanego w wyżej wymienionym artykule, powinna istnieć trasa zdefiniowana.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfiguracja sieci wirtualnej nie jest zgodna z wymaganiami HDInsight"

### <a name="issue"></a>Problem

Opisy błędów zawierają komunikaty podobne w następujący sposób:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Przyczyna

Prawdopodobnie problem z niestandardową konfiguracją DNS.

### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy 168.63.129.16 znajduje się w niestandardowym łańcuchu DNS. Serwery DNS w sieci wirtualnej mogą przesyłać dalej kwerendy DNS do cyklicznych programów rozpoznawania nazw platformy Azure w celu rozpoznawania nazw hostów w tej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozpoznawanie nazw w sieciach wirtualnych](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Dostęp do cyklicznych programów rozpoznawania nazw platformy Azure jest zapewniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Wykonaj następujące polecenie:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Powinny zostać wyświetlone informacje podobne do następujących:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Na podstawie wyniku — wybierz jedną z następujących czynności:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 nie ma na tej liście

**Wariant 1**  
Dodaj 168.63.129.16 jako pierwszy niestandardowy dns dla sieci wirtualnej, wykonując kroki opisane w [planowaniu sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Te kroki mają zastosowanie tylko wtedy, gdy niestandardowy serwer DNS działa w systemie Linux.

**Wariant 2**  
Wdrażanie maszyny Wirtualnej serwera DNS dla sieci wirtualnej. Obejmuje to następujące kroki:

* Utwórz maszynę wirtualną w sieci wirtualnej, która zostanie skonfigurowana jako usługa przesyłania dalej DNS (może to być maszyna wirtualna z systemem Linux lub Windows).
* Skonfiguruj reguły przekazywania dns na tej maszynie wirtualnej (prześlij wszystkie żądania rozpoznawania nazw iDNS do wersji 168.63.129.16, a resztę do serwera DNS).
* Dodaj adres IP tej maszyny Wirtualnej jako pierwszy wpis DNS dla konfiguracji DNS sieci wirtualnej.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 znajduje się na liście

W takim przypadku utwórz przypadek pomocy technicznej z HDInsight, a my zbadamy twój problem. Uwzględnij wyniki poniższych poleceń w przypadku pomocy technicznej. Pomoże nam to szybciej zbadać i rozwiązać problem.

Z sesji ssh w węźle głównym edytuj, a następnie uruchom następujące czynności:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
