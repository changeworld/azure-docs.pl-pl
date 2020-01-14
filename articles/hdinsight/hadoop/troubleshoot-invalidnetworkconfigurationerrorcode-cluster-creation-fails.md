---
title: Błąd InvalidNetworkConfigurationErrorCode — usługa Azure HDInsight
description: Różne przyczyny niepowodzenia tworzenia klastrów z InvalidNetworkConfigurationErrorCode w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: f857ee47f5dd8018d2e26aab47252533b0b17617
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887108"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Tworzenie klastra kończy się niepowodzeniem z InvalidNetworkConfigurationErrorCode w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

Jeśli widzisz kod błędu `InvalidNetworkConfigurationErrorCode` z opisem "Virtual Network konfiguracja nie jest zgodna z wymaganiem usługi HDInsight", zwykle wskazuje to na problem z [konfiguracją sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) dla klastra. W oparciu o resztę opisu błędu, postępuj zgodnie z poniższymi sekcjami, aby rozwiązać problem.

## <a name="hostname-resolution-failed"></a>"Rozpoznawanie nazwy hosta nie powiodło się"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "rozpoznawanie nazwy hosta nie powiodło się".

### <a name="cause"></a>Przyczyna

Ten błąd wskazuje na problem z niestandardową konfiguracją DNS. Serwery DNS w sieci wirtualnej mogą przekazywać zapytania DNS do tłumaczeń cyklicznych platformy Azure w celu rozpoznania nazw hostów w tej sieci wirtualnej (zobacz [rozpoznawanie nazw w sieciach wirtualnych](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) , aby uzyskać szczegółowe informacje). Dostęp do cyklicznych tłumaczeń platformy Azure jest udostępniany za pośrednictwem wirtualnego adresu IP 168.63.129.16. Ten adres IP jest dostępny tylko z maszyn wirtualnych platformy Azure. Dlatego nie będzie działała, jeśli używany jest serwer DNS lokalnego lub serwer DNS jest maszyną wirtualną platformy Azure, która nie jest częścią sieci wirtualnej klastra.

### <a name="resolution"></a>Rozdzielczość

1. Użyj polecenia SSH do maszyny wirtualnej, która jest częścią klastra, i uruchom polecenie `hostname -f`. Spowoduje to zwrócenie w pełni kwalifikowanej nazwy domeny hosta (nazywanej `<host_fqdn>` w poniższych instrukcjach).

1. Następnie uruchom polecenie `nslookup <host_fqdn>` (na przykład `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Jeśli to polecenie rozwiąże nazwę z adresem IP, oznacza to, że serwer DNS działa prawidłowo. W takim przypadku Zgłoś sprawę pomocy technicznej z usługą HDInsight i zbadamy swój problem. W przypadku pomocy technicznej należy uwzględnić wykonane kroki rozwiązywania problemów. Pomoże nam to przyspieszyć rozwiązywanie problemów.

1. Jeśli powyższe polecenie nie zwraca adresu IP, uruchom `nslookup <host_fqdn> 168.63.129.16` (na przykład `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Jeśli to polecenie jest w stanie rozpoznać adres IP, oznacza to, że serwer DNS nie przekazuje zapytania do usługi DNS systemu Azure lub nie jest to maszyna wirtualna, która jest częścią tej samej sieci wirtualnej co klaster.

1. Jeśli nie masz maszyny wirtualnej platformy Azure, która może działać jako niestandardowy serwer DNS w sieci wirtualnej klastra, musisz najpierw ją dodać. Utwórz maszynę wirtualną w sieci wirtualnej, która zostanie skonfigurowana jako usługa przesyłania dalej DNS.

1. Po wdrożeniu maszyny wirtualnej w sieci wirtualnej Skonfiguruj reguły przekazywania DNS na tej maszynie wirtualnej. Przekazuj wszystkie żądania rozpoznawania nazw IDN do 168.63.129.16, a pozostałe do serwera DNS. [Oto](../hdinsight-plan-virtual-network-deployment.md) przykład tego Instalatora dla NIESTANDARDOWEGO serwera DNS.

1. Dodaj adres IP tej maszyny wirtualnej jako pierwszy wpis DNS dla Virtual Network konfiguracji DNS.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nie można nawiązać połączenia z kontem usługi Azure Storage"

### <a name="issue"></a>Problem

Opis błędu zawiera komunikat "nie można nawiązać połączenia z kontem usługi Azure Storage" lub "nie można nawiązać połączenia z usługą Azure SQL".

### <a name="cause"></a>Przyczyna

Usługi Azure Storage i SQL nie mają stałych adresów IP, dlatego musimy zezwolić na połączenia wychodzące do wszystkich adresów IP, aby umożliwić uzyskiwanie dostępu do tych usług. Dokładne kroki rozwiązania zależą od tego, czy skonfigurowano sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń), czy reguły zdefiniowane przez użytkownika (UDR). Szczegółowe informacje o tych konfiguracjach można znaleźć w sekcji Sprawdzanie [ruchu sieciowego za pomocą usługi HDInsight z grupami zabezpieczeń sieci i trasami zdefiniowanymi przez użytkownika](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Rozdzielczość

* Jeśli klaster używa [sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Przejdź do Azure Portal i zidentyfikuj sieciowej grupy zabezpieczeń, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **reguły zabezpieczeń dla ruchu wychodzącego** Zezwalaj na dostęp wychodzący do Internetu bez ograniczenia (należy zauważyć, że w tym miejscu jest wyższy numer **priorytetu** ). Ponadto w sekcji **podsieci** Sprawdź, czy ta sieciowej grupy zabezpieczeń jest stosowana do podsieci klastra.

* Jeśli klaster używa [tras zdefiniowanych przez użytkownika (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Przejdź do Azure Portal i zidentyfikuj tabelę tras skojarzoną z podsiecią, w której jest wdrażany klaster. Po znalezieniu tabeli tras dla podsieci Sprawdź w niej sekcję **trasy** .

    Jeśli istnieją zdefiniowane trasy, upewnij się, że istnieją trasy dla adresów IP dla regionu, w którym wdrożono klaster, a **NextHopType** dla każdej trasy to **Internet**. Powinna istnieć trasa zdefiniowana dla każdego wymaganego adresu IP opisanego w wymienionym artykule.

---

### <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalne Microsoft Azure konto, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
