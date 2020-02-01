---
title: Tworzenie słownika błędów klastra
description: Dowiedz się, jak utworzyć słownik błędów klastra.
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 6e5ed996a0f44bae6c37027bc01f30be85d164f9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905485"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Błędy tworzenia klastra

W tym artykule opisano rozwiązania błędów, które mogą występować podczas tworzenia klastrów.

> [!NOTE]
> Pierwsze trzy błędy opisane w tym artykule są błędami walidacji. Mogą występować, gdy produkt Azure HDInsight używa klasy **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error"></a>Błąd

"Nie można uzyskać dostępu do lokalizacji akcji skryptu URI:\<adres URL akcji skryptu\>"

#### <a name="error-message"></a>Komunikat o błędzie

"Serwer zdalny zwrócił błąd: (404) nie znaleziono".

### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania utworzenia klastra. Usługa otrzymuje poprzedni komunikat o błędzie, gdy próbuje uzyskać dostęp do akcji skryptu.

### <a name="resolution"></a>Rozdzielczość

- W przypadku adresu URL HTTP lub HTTPS Sprawdź adres URL, próbując przejść do niego z okna przeglądarki incognito.
- W przypadku adresu URL WASB upewnij się, że skrypt istnieje na koncie magazynu podanym w żądaniu. Upewnij się również, że klucz magazynu dla tego konta magazynu jest prawidłowy.
- W przypadku adresu URL ADLS upewnij się, że skrypt istnieje na koncie magazynu.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error"></a>Błąd

"Nie można uzyskać dostępu do lokalizacji akcji skryptu URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Komunikat o błędzie

"Podany identyfikator URI \<skryptu SCRIPT_URI\> znajduje się w ADLS, ale ten klaster nie ma podmiotu zabezpieczeń Data Lake Storage"

### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania utworzenia klastra. Usługa otrzymuje poprzedni komunikat o błędzie, gdy próbuje uzyskać dostęp do akcji skryptu.

### <a name="resolution"></a>Rozdzielczość

Dodaj odpowiednie konto Azure Data Lake Storage Gen 1 do klastra. Należy również dodać nazwę główną usługi, która uzyskuje dostęp do tego konta Data Lake Storage Gen 1 do klastra.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error"></a>Błąd

"Rozmiar maszyny wirtualnej"\<CUSTOMER_SPECIFIED_VM_SIZE\>"podany w żądaniu jest nieprawidłowy lub nieobsługiwany dla roli"\<roli\>". Prawidłowe wartości to: \<VALID_VM_SIZE_FOR_ROLE\>".

### <a name="cause"></a>Przyczyna

Określony rozmiar maszyny wirtualnej nie jest dozwolony dla tej roli. Ten błąd może wystąpić, ponieważ wartość rozmiaru maszyny wirtualnej nie działa zgodnie z oczekiwaniami lub nie jest odpowiednia dla roli komputera.

### <a name="resolution"></a>Rozdzielczość

Komunikat o błędzie zawiera listę prawidłowych wartości dla rozmiaru maszyny wirtualnej. Wybierz jedną z tych wartości i ponów próbę utworzenia żądania klastra.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kod błędu: InvalidVirtualNetworkId  

### <a name="error"></a>Błąd

"VirtualNetworkId jest nieprawidłowy. VirtualNetworkId\<USER_VIRTUALNETWORKID\>' * '

### <a name="cause"></a>Przyczyna

Wartość **VirtualNetworkId** określona podczas tworzenia klastra nie ma poprawnego formatu.

### <a name="resolution"></a>Rozdzielczość

Upewnij się, że wartości **VirtualNetworkId** i podsieci mają poprawny format. Aby uzyskać wartość **VirtualNetworkId** :

1. Przejdź do witryny Azure Portal.
1. Wybierz sieć wirtualną.
1. Wybierz element menu **Właściwości** . Wartość właściwości **ResourceID** jest wartością **VirtualNetworkId** .

Oto przykład identyfikatora sieci wirtualnej:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kod błędu: CustomizationFailedErrorCode

### <a name="error"></a>Błąd

"Wdrożenie klastra nie powiodło się z powodu błędu w akcji skryptu niestandardowego. Akcje zakończone niepowodzeniem: \<SCRIPT_NAME\>, przejdź do interfejsu użytkownika Ambari, aby kontynuować debugowanie błędu ".

### <a name="cause"></a>Przyczyna

Skrypt niestandardowy dostarczony podczas żądania utworzenia klastra jest wykonywany po pomyślnym wdrożeniu klastra. Ten kod błędu wskazuje, że wystąpił błąd podczas wykonywania niestandardowego skryptu o nazwie \<SCRIPT_NAME\>.

### <a name="resolution"></a>Rozdzielczość

Skrypt jest skryptem niestandardowym, dlatego zalecamy rozwiązanie problemu i ponowne uruchomienie skryptu w razie potrzeby. Aby rozwiązać problem z błędem skryptu, zapoznaj się z dziennikami w folderze/var/lib/Ambari-Agent/*. Lub Otwórz stronę **operacje** w interfejsie użytkownika Ambari, a następnie wybierz operację **run_customscriptaction** , aby wyświetlić szczegóły błędu.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kod błędu: InvalidDocumentErrorCode

### <a name="error"></a>Błąd

"\<META_STORE_TYPE\> wersji schematu magazynu metadanych \<METASTORE_MAJOR_VERSION\> w bazie danych \<database_name\> jest niezgodna z wersją klastra \<CLUSTER_VERSION\>"

### <a name="cause"></a>Przyczyna

Niestandardowy magazyn metadanych jest niezgodny z wybraną wersją klastra usługi HDInsight. Obecnie klastry usługi HDInsight 4,0 obsługują tylko usługi magazynu metadanych w wersji 3,0 lub nowszej, natomiast klastry usługi HDInsight 3,6 nie obsługują magazynu metadanych w wersji 3,0 lub nowszej.

### <a name="resolution"></a>Rozdzielczość

Używaj tylko wersji magazynu metadanych obsługiwanej przez wersję klastra usługi HDInsight. Jeśli nie określisz niestandardowego magazynu metadanych, Usługa HDInsight wewnętrznie tworzy magazyn metadanych, a następnie usuwa go po usunięciu klastra.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kod błędu: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Błąd

"Nie można nawiązać połączenia z punktem końcowym zarządzania klastrem w celu wykonania operacji skalowania. Sprawdź, czy reguły zabezpieczeń sieci nie blokują dostępu zewnętrznego do klastra i czy można pomyślnie uzyskać dostęp do interfejsu użytkownika Menedżera klastra (Ambari).

### <a name="cause"></a>Przyczyna

Reguła zapory w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) blokuje komunikację z klastrem przy użyciu krytycznych usług zarządzania kondycją platformy Azure.

### <a name="resolution"></a>Rozdzielczość

Jeśli planujesz użyć grup zabezpieczeń sieci do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

- Określ region platformy Azure, który ma być używany przez usługę HDInsight.
- Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Adresy IP zarządzania usługą HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight.
  - W przypadku sieciowych grup zabezpieczeń Zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja zapewnia, że usługi zarządzania usługą HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kod błędu: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Błąd

"Zarządzana tożsamość nie ma uprawnień na koncie magazynu. Sprawdź, czy rola "właściciel danych obiektów blob magazynu" jest przypisana do zarządzanej tożsamości dla konta magazynu. Magazyn:/subscriptions/\<Identyfikator subskrypcji\>/resourceGroups/\< nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwa konta magazynu\>, tożsamość zarządzana:/subscriptions/\<Identyfikator subskrypcji\>/resourceGroups//\< nazwa grupy zasobów\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nazwa tożsamości zarządzanej przez użytkownika\>"

### <a name="cause"></a>Przyczyna

Nie podano uprawnień wymaganych do zarządzania tożsamościami. Tożsamość zarządzana przypisana przez użytkownika nie ma roli współautor Blob Storage na koncie magazynu Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Rozdzielczość

1. Otwórz witrynę Azure Portal.
1. Przejdź do swojego konta magazynu.
1. Poszukaj w obszarze **Access Control (IAM)** .
1. Upewnij się, że rola współautor danych obiektów blob magazynu lub rola właściciela danych obiektu blob magazynu ma przypisany dostęp do tożsamości zarządzanej przypisanej przez użytkownika dla subskrypcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kod błędu: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Błąd

"Reguły zabezpieczeń w sieciowej grupie zabezpieczeń/subscriptions/\<subskrypcji\>< nazwa grupy zasobów\> domyślna/dostawcy/Microsoft. Network/networkSecurityGroups/\<sieciowa nazwa grupy zabezpieczeń\> skonfigurowana za pomocą podsieci/subscriptions/\<subskrypcji\>/resourceGroups/\<nazwa grupy zasobów\> RG-westeurope-vnet-tomtom-default/Providers/Microsoft. Network/virtualNetworks/\<Virtual Nazwa sieciowa\>/Subnets/\<nazwa podsieci\> nie zezwala na połączenia przychodzące i/lub wychodzące. Aby uzyskać więcej informacji, odwiedź stronę [Planowanie sieci wirtualnej dla usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)lub skontaktuj się z pomocą techniczną.

### <a name="cause"></a>Przyczyna

Jeśli sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika (UDR) kontrolują ruch przychodzący do klastra usługi HDInsight, należy się upewnić, że klaster może komunikować się z krytycznymi usługami Azure Health i Management.

### <a name="resolution"></a>Rozdzielczość

Jeśli planujesz użyć grup zabezpieczeń sieci do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

- Określ region platformy Azure, który ma być używany na potrzeby usługi HDInsight, i Utwórz bezpieczną listę adresów IP dla danego regionu. Aby uzyskać więcej informacji, zobacz temat [usługi kondycji i zarządzania: określone regiony](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [adresy IP zarządzania usługą HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight. W przypadku sieciowych grup zabezpieczeń Zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja zapewnia, że usługi zarządzania usługą HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kod błędu: Instalator klastra nie może zainstalować składników na jednym lub większej liczbie hostów

###  <a name="error"></a>Błąd

"Instalator klastra nie może zainstalować składników na co najmniej jednym hoście. Spróbuj ponownie wykonać żądanie. "

### <a name="cause"></a>Przyczyna 

Zazwyczaj ten błąd jest generowany, gdy występuje problem przejściowy lub awaria platformy Azure.

### <a name="resolution"></a>Rozdzielczość

Sprawdź na stronie [stanu platformy Azure](https://status.azure.com) ewentualne przestoje związane z platformą Azure, które mogą mieć wpływ na wdrożenie klastra. Jeśli nie ma przerwy w działaniu, ponów próbę wdrożenia klastra.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów podczas tworzenia klastra, zobacz [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
