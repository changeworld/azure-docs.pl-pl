---
title: Tworzenie słownika błędów klastra
description: Dowiedz się, jak utworzyć słownik błędów klastra.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483065"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: Błędy tworzenia klastra

W tym artykule opisano rozwiązania błędów napotkanych podczas tworzenia klastrów. 

> [!NOTE]
> Trzy pierwsze błędy na poniższej liście występują z powodu błędów walidacji, gdy Klasa CsmDocument_2_0 jest używana przez produkt HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Błąd**: nie można uzyskać dostępu do lokalizacji akcji skryptu URI:\<adres URL akcji skryptu\>

**Komunikat o błędzie: "serwer zdalny zwrócił błąd: (404) nie znaleziono".**

### <a name="cause"></a>Przyczyna
Adres URL akcji skryptu, który jest dostarczany jako część żądania utworzenia klastra, nie jest dostępny z usługi HDInsight. W przypadku próby uzyskania dostępu do akcji skryptu zostanie wyświetlona wartość "ErrorMessage".

### <a name="resolution"></a>Rozdzielczość 
  - W przypadku adresu URL protokołu HTTP/HTTPS możesz sprawdzić, próbując otworzyć adres URL z okna przeglądarki incognito. 
  - W przypadku adresu URL WASB upewnij się, że skrypt istnieje na koncie magazynu podanym w żądaniu. Upewnij się, że klucz magazynu dla tego konta magazynu jest prawidłowy. 
  - W przypadku adresu URL ADLS upewnij się, że skrypt istnieje na koncie magazynu.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Błąd**: nie można uzyskać dostępu do lokalizacji akcji skryptu URI: \<adres URL akcji skryptu\>

**Komunikat o błędzie: podany identyfikator URI skryptu \<identyfikator URI skryptu\> znajduje się w ADLS, ale ten klaster nie ma podmiotu zabezpieczeń Data Lake Storage**

### <a name="cause"></a>Przyczyna
Adres URL akcji skryptu, który jest dostarczany jako część żądania utworzenia klastra, nie jest dostępny w usłudze HDInsight. W przypadku próby uzyskania dostępu do akcji skryptu zostanie wyświetlona wartość "ErrorMessage". 

### <a name="resolution"></a>Rozdzielczość

Upewnij się, że odpowiednie konto Azure Data Lake Store Gen 1 jest dodane do klastra. Nazwa główna usługi, która jest używana do uzyskiwania dostępu do konta Azure Data Lake Store generacji 1, jest również dodawana do klastra. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Błąd**: rozmiar maszyny wirtualnej "\<rozmiar maszyny wirtualnej określony przez klienta\>" podany w żądaniu jest nieprawidłowy lub nieobsługiwany dla roli "\<roli\>". Prawidłowe wartości to: \<prawidłowy rozmiar maszyny wirtualnej dla\>roli.

### <a name="cause"></a>Przyczyna
Rozmiar maszyny wirtualnej określony przez klienta nie jest dozwolony dla tej roli. Może to być prawdziwe, ponieważ wartość rozmiaru maszyny wirtualnej nie działa zgodnie z oczekiwaniami lub nie jest odpowiednia dla roli komputera. 

### <a name="resolution"></a>Rozdzielczość
Komunikat o błędzie zawiera listę prawidłowych wartości dla rozmiaru maszyny wirtualnej. Wybierz jedną z tych prawidłowych wartości i ponów próbę utworzenia żądania klastra. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kod błędu: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Błąd**: VirtualNetworkId jest nieprawidłowy. VirtualNetworkId\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Przyczyna
Wartość **VirtualNetworkId** określona podczas tworzenia klastra ma niepoprawny format. 

### <a name="resolution"></a>Rozdzielczość
Upewnij się, że **VirtualNetworkId** i podsieć mają poprawny format. Aby uzyskać wartość **VirtualnetworkId** , przejdź do Azure Portal, wybierz sieć wirtualną, a następnie wybierz pozycję **Właściwości** w menu. Właściwość **ResourceID** jest wartością **VirtualNetworkId** . 

Przykładem identyfikatora sieci wirtualnej jest: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Kod błędu: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Błąd**: Wdrożenie klastra nie powiodło się z powodu błędu w akcji skryptu niestandardowego. Akcje zakończone niepowodzeniem: \<SCRIPT_NAME\>, przejdź do interfejsu użytkownika Ambari, aby kontynuować debugowanie błędu.

### <a name="cause"></a>Przyczyna
Skrypt niestandardowy użytkownika podany podczas tworzenia żądania klastra jest wykonywany po pomyślnym wdrożeniu klastra. Ten kod błędu wskazuje, że wystąpił błąd podczas wykonywania tego niestandardowego skryptu o nazwie \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Rozdzielczość
Ponieważ jest to skrypt niestandardowy użytkownika, użytkownicy powinni rozwiązać ten problem, a jeśli to konieczne, ponownie uruchomić skrypt. Aby rozwiązać problem z błędem skryptu, zapoznaj się z dziennikami w folderze/var/lib/Ambari-Agent/*. Lub Otwórz stronę operacje w interfejsie użytkownika Ambari, a następnie wybierz operację **run_customscriptionaction** , aby wyświetlić szczegóły błędu. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kod błędu: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Błąd**: \<META_STORE_TYPE\> wersja schematu magazynu metadanych \<METASTORE_MAJOR_VERSION\> w bazie danych \<database_name\> jest niezgodna z wersją klastra \<CLUSTER_VERSION\>

### <a name="cause"></a>Przyczyna
Niestandardowy magazyn metadanych jest niezgodny z wybraną wersją klastra usługi HDInsight. Obecnie klaster usługi HDInsight 4,0 obsługuje tylko magazyn metadanych w wersji 3. *x*i HDInsight 3,6 nie obsługują magazynu metadanych w wersji 3. *x* lub nowszy. 

### <a name="resolution"></a>Rozdzielczość
Upewnij się, że używane są tylko wersje magazynu metadanych obsługiwane przez poszczególne wersje klastra usługi HDInsight. Należy zauważyć, że jeśli nie określono niestandardowego magazynu metadanych, Usługa HDInsight wewnętrznie tworzy magazyn metadanych. Jednak ten magazyn metadanych zostanie automatycznie usunięty po usunięciu klastra. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kod błędu: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Błąd**: nie można nawiązać połączenia z punktem końcowym zarządzania klastrem w celu wykonania operacji skalowania. Sprawdź, czy reguły zabezpieczeń sieci nie blokują dostępu zewnętrznego do klastra i czy można pomyślnie uzyskać dostęp do interfejsu użytkownika Menedżera klastra (Ambari).

### <a name="cause"></a>Przyczyna
Masz regułę zapory w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która blokuje komunikację z klastrem z krytycznymi usługami zarządzania i kondycji platformy Azure. 

### <a name="resolution"></a>Rozdzielczość
Jeśli planujesz użyć **sieciowych grup zabezpieczeń** w celu kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności: 
  - Określ region platformy Azure, który ma być używany przez usługę HDInsight. 
  - Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Adresy IP zarządzania usługą HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight. 
    - **Sieciowe grupy zabezpieczeń:** Zezwalaj na ruch **przychodzący** na porcie **443** z adresów IP. Daje to pewność, że usługi zarządzania usługą HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kod błędu: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Błąd**: zarządzana tożsamość nie ma uprawnień na koncie magazynu. Sprawdź, czy rola "właściciel danych obiektów blob magazynu" jest przypisana do zarządzanej tożsamości dla konta magazynu. Magazyn:/subscriptions/\<Identyfikator subskrypcji\>/resourceGroups/\< nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwa konta magazynu\>, tożsamość zarządzana:/subscriptions/\<Identyfikator subskrypcji\>/resourceGroups//\< nazwa grupy zasobów\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<nazwa tożsamości zarządzanej przez użytkownika\>

### <a name="cause"></a>Przyczyna
Nie podano wymaganych uprawnień do **zarządzania tożsamością.** **Tożsamość zarządzana przypisana przez użytkownika** nie ma roli współautor BLOB Storage na koncie magazynu ADLS Gen2. 

### <a name="resolution"></a>Rozdzielczość

Otwórz Azure Portal, przejdź do konta magazynu, poszukaj w obszarze **Access Control (IAM)** i upewnij się, że współautor danych obiektów blob magazynu lub rola właściciela danych obiektu blob magazynu ma dostęp do **tożsamości zarządzanej przypisanej przez użytkownika** dla subskrypcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kod błędu: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Błąd**: reguły zabezpieczeń w sieciowej grupie zabezpieczeń/subscriptions/\<subskrypcji >\/resourceGroups/<Resource Group name> default/Providers/Microsoft. Network/networkSecurityGroups/\<Network Security Group Name\> skonfigurowany przy użyciu podsieci/subscriptions/\<subskrypcji >\/resourceGroups/\<nazwa grupy zasobów > RG-westeurope-VNET-TomTom — domyślne\/dostawców/Microsoft. Network/virtualNetworks/\<wirtualne Nazwa sieci >\/podsieci/nazwy podsieci\<\> 
nie zezwala na wymaganą łączność ruchu przychodzącego i/lub wychodzącego. Aby uzyskać więcej informacji, odwiedź stronę [Planowanie sieci wirtualnej dla usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)lub skontaktuj się z pomocą techniczną. * *

### <a name="cause"></a>Przyczyna
W przypadku używania sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu przychodzącego do klastra usługi HDInsight należy upewnić się, że klaster może komunikować się z krytycznymi usługami Azure Health i Management.

### <a name="resolution"></a>Rozdzielczość
Jeśli planujesz użyć **sieciowych grup zabezpieczeń** w celu kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności: 
  - Określ region platformy Azure, który ma być używany przez usługę HDInsight, i Utwórz bezpieczną listę adresów IP dla regionu: [usługi kondycji i zarządzania: określone regiony](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [adresy IP zarządzania usługą HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight. **Sieciowe grupy zabezpieczeń**: Zezwalaj na ruch przychodzący na porcie **443** z adresów IP. Dzięki temu usługi HDInsight Management Services mogą dotrzeć do klastra spoza sieci wirtualnej.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kod błędu: Instalator klastra nie może zainstalować składników na jednym lub większej liczbie hostów

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Błąd**: Instalator klastra nie może zainstalować składników na co najmniej jednym hoście. Spróbuj ponownie wykonać żądanie. 

### <a name="cause"></a>Przyczyna 
Zazwyczaj ten błąd jest generowany, gdy występuje przejściowy problem lub występuje awaria platformy Azure 

### <a name="resolution"></a>Rozdzielczość

Sprawdź na stronie [stanu platformy Azure](https://status.azure.com/status) potencjalne przerwy w działaniu platformy Azure, które mogą mieć wpływ na wdrożenie klastra. Jeśli nie ma przerwy w działaniu, ponów próbę wdrożenia klastra. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z tworzeniem klastra, zobacz [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
