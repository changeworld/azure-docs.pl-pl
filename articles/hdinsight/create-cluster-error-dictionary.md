---
title: Usługa Azure HDInsight Tworzenie klastra — słownik błędów
description: Dowiedz się, jak rozwiązywać problemy z błędami występującymi podczas tworzenia klastrów usługi Azure HDInsight
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302733"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Usługa Azure HDInsight: błędy tworzenia klastra

W tym artykule opisano rozwiązania problemów z błędami, które mogą wystąpić podczas tworzenia klastrów.

> [!NOTE]
> Pierwsze trzy błędy opisane w tym artykule są błędy sprawdzania poprawności. Mogą one wystąpić, gdy produkt usługi Azure HDInsight używa **CsmDocument_2_0** klasy.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument 'CsmDocument_2_0' nie powiodło się sprawdzanie poprawności

### <a name="error"></a>Błąd

"Lokalizacja akcji skryptu nie może\<uzyskać\>dostępu do identyfikatora URI: adres URL AKCJI SKRYPTU"

#### <a name="error-message"></a>Komunikat o błędzie

"Serwer zdalny zwrócił błąd: (404) Nie znaleziono."

### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania Utwórz klaster. Usługa odbiera poprzedni komunikat o błędzie podczas próby uzyskania dostępu do akcji skryptu.

### <a name="resolution"></a>Rozwiązanie

- W przypadku adresu URL HTTP lub HTTPS sprawdź adres URL, próbując przejść do niego z okna przeglądarki incognito.
- W przypadku adresu URL WASB upewnij się, że skrypt istnieje na koncie magazynu, który podałeś w żądaniu. Upewnij się również, że klucz magazynu dla tego konta magazynu jest poprawny.
- W przypadku adresu URL ADLS upewnij się, że skrypt istnieje na koncie magazynu.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument 'CsmDocument_2_0' nie powiodło się sprawdzanie poprawności

### <a name="error"></a>Błąd

"Lokalizacja akcji skryptu nie może \<uzyskać dostępu\>do identyfikatora URI: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Komunikat o błędzie

"Podany skrypt identyfikatora \<URI\> SCRIPT_URI jest w ADLS, ale ten klaster nie ma głównego magazynu usługi data lake"

### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania Utwórz klaster. Usługa odbiera poprzedni komunikat o błędzie podczas próby uzyskania dostępu do akcji skryptu.

### <a name="resolution"></a>Rozwiązanie

Dodaj odpowiednie konto usługi Azure Data Lake Storage Gen 1 do klastra. Dodaj również jednostkę usługi, która uzyskuje dostęp do konta Usługi Data Lake Storage Gen 1 do klastra.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument 'CsmDocument_2_0' nie powiodło się sprawdzanie poprawności

### <a name="error"></a>Błąd

"Rozmiar maszyny\<Wirtualnej '\>CUSTOMER_SPECIFIED_VM_SIZE ' pod warunkiem, że w\<żądaniu jest nieprawidłowy lub nie obsługiwane dla roli ' ROLE\>'. Prawidłowe wartości \<to:\>VALID_VM_SIZE_FOR_ROLE ."

### <a name="cause"></a>Przyczyna

Określony rozmiar maszyny wirtualnej nie jest dozwolony dla roli. Ten błąd może wystąpić, ponieważ wartość rozmiaru maszyny Wirtualnej nie działa zgodnie z oczekiwaniami lub nie jest odpowiedni dla roli komputera.

### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie zawiera listę prawidłowych wartości rozmiaru maszyny Wirtualnej. Wybierz jedną z tych wartości i ponów próbę żądania Utwórz klaster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kod błędu: InvalidVirtualNetworkId  

### <a name="error"></a>Błąd

"Identyfikator VirtualNetworkId jest nieprawidłowy. VirtualNetworkId\<' USER_VIRTUALNETWORKID\>'*"

### <a name="cause"></a>Przyczyna

Wartość **VirtualNetworkId** określona podczas tworzenia klastra nie jest w poprawnym formacie.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że **virtualnetworkid** i podsieci wartości są w prawidłowym formacie. Aby uzyskać wartość **VirtualNetworkId:**

1. Przejdź do witryny Azure Portal.
1. Wybierz sieć wirtualną.
1. Wybierz pozycję menu **Właściwości.** Wartość właściwości **ResourceID** jest wartością **VirtualNetworkId.**

Oto przykład identyfikatora sieci wirtualnej:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kod błędu: CustomizationFailedErrorCode

### <a name="error"></a>Błąd

"Wdrożenie klastra nie powiodło się z powodu błędu w akcji skryptu niestandardowego. Nieudane akcje: \<SCRIPT_NAME\>, Przejdź do interfejsu użytkownika Ambari, aby dodatkowo debugować błąd."

### <a name="cause"></a>Przyczyna

Skrypt niestandardowy podany podczas żądania Utwórz klaster jest wykonywany po pomyślnym wdrożeniu klastra. Ten kod błędu wskazuje, że podczas wykonywania skryptu \<niestandardowego\>o nazwie SCRIPT_NAME wystąpił błąd.

### <a name="resolution"></a>Rozwiązanie

Ponieważ skrypt jest skryptem niestandardowym, zaleca się rozwiązanie problemu i ponowne uruchomienie skryptu, jeśli to konieczne. Aby rozwiązać problem z błędem skryptu, sprawdź dzienniki w folderze /var/lib/ambari-agent/* . Możesz też otworzyć stronę **Operacje** w interfejsie użytkownika ambari, a następnie wybrać operację **run_customscriptaction,** aby wyświetlić szczegóły błędu.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kod błędu: InvalidDocumentErrorCode

### <a name="error"></a>Błąd

"Wersja \<\> \<schematu META_STORE_TYPE\> Metastore METASTORE_MAJOR_VERSION w bazie \<danych DATABASE_NAME\> jest niezgodna \<z wersją\>klastra CLUSTER_VERSION "

### <a name="cause"></a>Przyczyna

Niestandardowy magazyn metasewalczy jest niezgodny z wybraną wersją klastra HDInsight. Obecnie klastry HDInsight 4.0 obsługują tylko metastore w wersji 3.0 lub nowszej, podczas gdy klastry HDInsight 3.6 nie obsługują metastore w wersji 3.0 i nowszej.

### <a name="resolution"></a>Rozwiązanie

Używaj tylko wersji metastore, które obsługuje wersja klastra HDInsight. Jeśli nie określisz niestandardowego magazynu metastore, usługa HDInsight wewnętrznie utworzy magazyn metasklepu, a następnie usunie go po usunięciu klastra.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kod błędu: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Błąd

"Nie można połączyć się z punktem końcowym zarządzania klastrem w celu wykonania operacji skalowania. Sprawdź, czy reguły zabezpieczeń sieciowych nie blokują zewnętrznego dostępu do klastra i czy można pomyślnie uzyskać dostęp do interfejsu użytkownika Menedżera klastra (Ambari).

### <a name="cause"></a>Przyczyna

Reguła zapory w sieciowej grupie zabezpieczeń (NSG) blokuje komunikację klastra z krytycznymi usługami kondycji i zarządzania platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jeśli planujesz używać sieciowych grup zabezpieczeń do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight należy podjąć następujące czynności:

- Zidentyfikuj region platformy Azure, który ma być używany dla usługi HDInsight.
- Zidentyfikuj adresy IP wymagane przez hdinsight. Aby uzyskać więcej informacji, zobacz [Adresy IP zarządzania usługą HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń podsieci, w której zamierzasz zainstalować usługę HDInsight.
  - W przypadku sieciowych grup zabezpieczeń zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja gwarantuje, że usługi zarządzania hdinsight mogą dotrzeć do klastra spoza sieci wirtualnej.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kod błędu: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Błąd

"Tożsamość zarządzana nie ma uprawnień do konta magazynu. Sprawdź, czy rola "Właściciel danych obiektu blob magazynu" jest przypisana do tożsamości zarządzanej dla konta magazynu. Magazyn: \</subscriptions/ Subscription\> ID /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/Storage \<Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /Resource\< Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Przyczyna

Nie podałeś uprawnień wymaganych do zarządzania tożsamością. Tożsamość zarządzana przypisana przez użytkownika nie ma roli współautora magazynu obiektów Blob na koncie magazynu usługi Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Rozwiązanie

1. Otwórz witrynę Azure Portal.
1. Przejdź do swojego konta magazynu.
1. Poszukaj **pod kontrolą dostępu (IAM)**.
1. Upewnij się, że użytkownik ma przypisaną rolę współautora danych obiektu blob magazynu lub właściciela obiektu blob magazynu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kod błędu: InvalidNetworkSecuritySecurityRules  

### <a name="error"></a>Błąd

"Reguły zabezpieczeń w sieciowej grupie\<zabezpieczeń\>/subskrypcje/ SubscriptionID /resourceGroups/<Nazwa\> grupy zasobów domyślna/dostawcy/Microsoft.Network/network Security\<Group Nazwa\> \<skonfigurowana\>z podsiecią /subskrypcje/ SubscriptionID /resourceGroups/\<Nazwa\> grupy zasobów RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Nazwa\>sieci\</podsieci/\> Nazwa podsieci nie zezwala na wymaganą łączność przychodzącą i/lub wychodzącą. Aby uzyskać więcej informacji, odwiedź stronę [Planowanie sieci wirtualnej dla usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)lub skontaktuj się z pomocą techniczną."

### <a name="cause"></a>Przyczyna

Jeśli sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika (UDR) kontrolują ruch przychodzący do klastra USŁUGI HDInsight, upewnij się, że klaster może komunikować się z krytycznymi usługami kondycji i zarządzania platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jeśli planujesz używać sieciowych grup zabezpieczeń do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight należy podjąć następujące czynności:

- Zidentyfikuj region platformy Azure, którego zamierzasz użyć dla usługi HDInsight, i utwórz bezpieczną listę adresów IP dla swojego regionu. Aby uzyskać więcej informacji, zobacz [Usługi w zakresie kondycji i zarządzania: Określone regiony](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Zidentyfikuj adresy IP, których wymaga usługa HDInsight. Aby uzyskać więcej informacji, zobacz [Adresy IP zarządzania hdinsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń podsieci, w której zamierzasz zainstalować usługę HDInsight. W przypadku sieciowych grup zabezpieczeń zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja gwarantuje, że usługi zarządzania hdinsight mogą dotrzeć do klastra spoza sieci wirtualnej.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kod błędu: instalacja klastra nie może zainstalować składników na co najmniej jednym hostie

###  <a name="error"></a>Błąd

"Instalacja klastra nie może zainstalować składników na co najmniej jednym hostie. Ponów próbę złożenia wniosku."

### <a name="cause"></a>Przyczyna 

Zazwyczaj ten błąd jest generowany, gdy występuje przejściowy problem lub awaria platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Sprawdź stronę [stanu platformy Azure](https://status.azure.com) pod kątem wszelkich awarii platformy Azure, które mogą mieć wpływ na wdrożenie klastra. Jeśli nie ma żadnych awarii, ponów próbę wdrożenia klastra.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z błędami podczas tworzenia [klastra, zobacz Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
