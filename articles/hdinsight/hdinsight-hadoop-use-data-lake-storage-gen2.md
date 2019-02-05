---
title: Za pomocą usług Azure Data Lake Storage Gen2 klastrów Azure HDInsight
description: Dowiedz się, jak utworzyć Użyj klastrów Azure Data Lake Storage Gen2 za pomocą usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: fae92f8e09cc2ad6b63cb15599e0b1ab72588ed8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728847"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Za pomocą usług Azure Data Lake Storage Gen2 klastrów Azure HDInsight

Azure Gen2 usługi Data Lake Storage (magazyn usługi Data Lake) to zestaw funkcji przeznaczonych do analizy danych big data, oparta na usłudze Azure Blob storage. Data Lake Storage Gen2 jest wynikiem połączenia możliwości usługi Azure Blob storage i Azure Data Lake Storage Gen1. Wynik to usługa, która oferuje funkcje z usługi Azure Data Lake Storage Gen1, takich jak semantyki systemu plików katalogu i zabezpieczenia na poziomie pliku i są skalowane wraz z ekonomicznych i warstwowego magazynu, wysokiej dostępności/po awarii funkcje odzyskiwania z magazynu obiektów Blob platformy Azure Magazyn.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 dostępności

Azure Data Lake magazynu Gen2 jest dostępna jako opcji magazynu dla prawie wszystkich typów klastra Azure HDInsight jako domyślny i dodatkowe konto magazynu. Baza danych HBase, jednak może mieć tylko jednego konta Data Lake Storage Gen2.

> [!Note] 
> Po wybraniu Data Lake Storage Gen2 jako swojej **podstawowy typ magazynu**, nie można wybrać konta Data Lake Storage Gen1 jako dodatkowego magazynu.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Tworzenie klastra usługi HDInsight za pomocą Data Lake Storage Gen2

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Do utworzenia klastra HDInsight, który używa Data Lake Storage Gen2 magazynu, należy użyć następujące kroki do utworzenia konta Data Lake Storage Gen2, który jest skonfigurowany prawidłowo.

1. Utwórz przypisanych przez użytkownika tożsamości zarządzanej, jeśli nie masz jeszcze jeden. Zobacz [tworzenia, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

    ![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Utwórz konto magazynu Azure Data Lake Storage Gen2. Upewnij się, że **hierarchiczny system plików** opcja jest włączona. Zobacz [Szybki start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md) Aby uzyskać więcej informacji.

    ![Zrzut ekranu przedstawiający tworzenie konta magazynu w witrynie Azure portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Przypisz tożsamość zarządzaną do **właściciela danych obiektu Blob magazynu (wersja zapoznawcza)** roli na koncie magazynu. Zobacz [Zarządzaj praw dostępu do danych obiektów Blob platformy Azure i kolejek przy użyciu RBAC (wersja zapoznawcza)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.
    1. Wybierz konto magazynu, a następnie wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.
    
        ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Kliknij przycisk **Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
    1. W **Dodaj przypisanie roli** wybierz **właściciela danych obiektu Blob magazynu (wersja zapoznawcza)** roli. Następnie wybierz subskrypcję, która ma zarządzanych tożsamości i konta magazynu. Następnie wyszukaj, aby zlokalizować użytkownik przypisany zarządzanych tożsamości, który został utworzony wcześniej. Na koniec wybierz pozycję tożsamość zarządzaną i będzie ono wyświetlane w obszarze **wybrane elementy członkowskie**.
    
        ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
    1. Kliknij pozycję **Zapisz**. Tożsamości przypisanych przez użytkownika, wybrany teraz znajduje się w obszarze **Współautor** roli.

    1. Po ukończeniu tej konfiguracji początkowej można utworzyć klastra za pośrednictwem portalu. Klaster musi być w tym samym regionie platformy Azure jako konto magazynu. W **magazynu** sekcji menu tworzenia klastra wybierz następujące opcje:
        
        * Aby uzyskać **podstawowy typ magazynu**, kliknij przycisk **usługi Azure Data Lake Storage Gen2**.
        * W obszarze **wybierz konto magazynu**, wyszukaj i wybierz nowo utworzone konto magazynu Data Lake Storage Gen2.
        
            ![Ustawienia magazynu Data Lake Storage Gen2 przy użyciu usługi Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * W obszarze **tożsamości** wybierz poprawną subskrypcję, a nowo utworzony użytkownik przypisany tożsamość zarządzaną.
        
            ![Ustawienia tożsamości Data Lake Storage Gen2 przy użyciu usługi Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="using-a-resource-manager-template-deployed-with-azure-cli"></a>Przy użyciu szablonu usługi Resource Manager wdrożona za pomocą wiersza polecenia platformy Azure

Możesz pobrać próbkę [pliku szablonu w tym miejscu](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) i [przykładowe parametry zgłosić się tutaj](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Przed rozpoczęciem korzystania z tego szablonu, Zastąp identyfikator subskrypcji platformy Azure rzeczywistego ciągu `<SUBSCRIPTION_ID>`. Ponadto Zastąp wybrany hasła dla ciągu `<PASSWORD>` można ustawić zarówno hasło logowania, która będzie używana do logowania do klastra, a także hasło SSH.

Poniższy fragment kodu wykonuje następujące kroki początkowe:

1. Zaloguj się do konta platformy Azure.
1. Ustaw aktywną subskrypcję, w której będzie można wykonać operacji tworzenia.
1. Utwórz nową grupę zasobów dla nowych działań wdrożenia `hdinsight-deployment-rg`.
1. Utwórz użytkownika Managed Service Identity (MSI) `test-hdinsight-msi`.
1. Dodaj rozszerzenia do wiersza polecenia platformy Azure, aby używać funkcji dla Data Lake Storage Gen2.
1. Tworzenie nowego konta Data Lake Storage Gen2 `hdinsightadlsgen2`, za pomocą `--hierarchical-namespace true` flagi.

```azurecli
az login
az account set --subscription <subscription_id>

#create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Następnie zaloguj się do portalu i dodać nowego pliku MSI do **Współautor danych obiektu Blob Storage (wersja zapoznawcza)** roli na koncie magazynu, zgodnie z opisem w kroku 3 powyżej w sekcji [przy użyciu witryny Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md#using-the-azure-portal).

Po zakończeniu pracy Instalatora MSI przypisanie roli w portalu, przejdź do wdrożyć szablon przy użyciu fragmencie kodu poniżej.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu dla usługi Data Lake Storage Gen2 w HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakiego rodzaju uprawnień obsługuje usługa Data Lake Storage Gen2?

Azure Data Lake magazynu Gen2 implementuje model kontroli dostępu, który obsługuje zarówno Azure roli na podstawie kontroli dostępu (RBAC), jak i listy kontroli dostępu POSIX i podobne (kontroli dostępu ACL). Usługa Data Lake Gen1 magazynowania obsługiwane tylko listy kontroli dostępu do kontrolowania dostępu do danych.

Azure opartej na rolach kontrola dostępu (RBAC) używa przypisań ról dotyczą skutecznie zestawy uprawnień użytkownikom, grupom i nazwy główne usług dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do najwyższego poziomu zasobów (na przykład konta usługi Azure Storage). Dla usługi Azure Storage, a także usługi Azure Data Lake Storage Gen2 ten mechanizm został rozszerzony do zasobów systemu plików.

 Aby uzyskać więcej informacji na uprawnienia do pliku przy użyciu RBAC, zobacz [kontroli dostępu opartej na rolach platformy Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Aby uzyskać więcej informacji na temat uprawnień pliku za pomocą listy kontroli dostępu, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Jak kontrolować dostęp do swoich danych w Gen2?

Możliwości dla klastra usługi HDInsight, dostęp do plików w Data Lake Storage Gen2 steruje się za pomocą zarządzanych tożsamości. Tożsamość zarządzana jest tożsamością zarejestrowane w usłudze Azure AD, którego poświadczenia są zarządzane przez platformę Azure. Nie trzeba rejestrować jednostek usługi w usłudze Azure AD i obsługa poświadczeń, takich jak certyfikaty.

Istnieją dwa typy zarządzanych tożsamości dla usług platformy Azure: przypisana przez system i przypisanych przez użytkownika. Usługa Azure HDInsight korzysta z tożsamości zarządzanej przypisanych przez użytkownika, dostęp do usługi Azure Data Lake Storage Gen2. Użytkownik przypisany tożsamość zarządzana zostanie utworzona jako autonomiczny zasobów platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure. Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji na temat zarządzanych tożsamości, zobacz [jakie zarządzanych tożsamości do pracy z zasobami platformy Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Jak ustawić uprawnienia dla użytkowników usługi Azure AD do zapytania o dane w Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług?

Użyj grup zabezpieczeń usługi Azure AD jako przypisanej jednostki na listach ACL w. Nie bezpośrednio przypisać poszczególnych użytkowników lub jednostek usług z uprawnieniami dostępu do pliku. Korzystając z grup zabezpieczeń usługi AD do sterowania przepływem uprawnienia, możesz dodać i usunąć użytkowników lub jednostek usługi bez ponowne zastosowanie listy ACL do całej struktury katalogów. Wystarczy tylko dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. List ACL nie są dziedziczone, a więc w celu ponownego zastosowania list ACL wymaga zaktualizowania listy ACL dla każdego pliku i podkatalogu.

## <a name="next-steps"></a>Kolejne kroki

* [Użyj usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza) przy użyciu klastrów usługi Azure HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Integracja usługi Azure HDInsight w wersji zapoznawczej Data Lake Storage Gen2 — lista ACL i zabezpieczeń aktualizacji](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do usługi Azure Data Lake Storage 2. generacji (wersja zapoznawcza)](../storage/blobs/data-lake-storage-introduction.md)
