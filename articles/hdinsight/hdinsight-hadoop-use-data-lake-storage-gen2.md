---
title: Za pomocą usług Azure Data Lake Storage Gen2 klastrów Azure HDInsight
description: Dowiedz się, jak używać usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 186a9bafe70ab9644666868f11d5ddd865a66b8d
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802545"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Za pomocą usług Azure Data Lake Storage Gen2 klastrów Azure HDInsight

Azure Data Lake magazynu Gen2 to usługa magazynu w chmurze do analizy danych big data, oparta na usłudze Azure Blob storage w wersji dedykowanej. Data Lake Storage Gen2 łączy możliwości usługi Azure Blob storage i Azure Data Lake Storage Gen1. Wynikowy usługa oferuje funkcje z usługi Azure Data Lake Storage Gen1 semantyki systemu plików, poziomu katalogów i plików poziom zabezpieczeń i skalowalności, wraz z ekonomicznych i warstwowego, wysoką dostępność i możliwości odzyskiwania po awarii z usługi Azure Blob storage.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 dostępności

Data Lake Storage Gen2 jest dostępna jako opcji magazynu dla prawie wszystkich typów klastra Azure HDInsight jako domyślny i dodatkowe konto magazynu. Baza danych HBase, jednak może mieć tylko jednego konta Data Lake Storage Gen2.

> [!Note] 
> Po wybraniu Data Lake Storage Gen2 jako swojej **podstawowy typ magazynu**, nie można wybrać konta Data Lake Storage Gen1 jako dodatkowego magazynu.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Tworzenie klastra z usługą Data Lake Storage Gen2 za pośrednictwem witryny Azure portal

Aby utworzyć klaster usługi HDInsight, które przechowują Data Lake Storage Gen2, wykonaj następujące kroki, aby skonfigurować konta Data Lake Storage Gen2.

### <a name="create-a-user-managed-identity"></a>Tworzenie tożsamości zarządzane przez użytkownika

Utwórz przypisanych przez użytkownika tożsamości zarządzanej, jeśli nie masz jeszcze jeden. Zobacz [tworzenia, listy, usuwania lub przypisać rolę do przypisanych przez użytkownika tożsamości zarządzanej przy użyciu witryny Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Aby uzyskać więcej informacji na temat zarządzanych pracy tożsamości w usłudze Azure HDInsight, zobacz [zarządzanych tożsamości w usłudze Azure HDInsight](hdinsight-managed-identities.md).

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Tworzenie konta Data Lake Storage Gen2

Utwórz konto usługi Azure Data Lake Storage Gen2. Upewnij się, że **hierarchicznej przestrzeni nazw** opcja jest włączona. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Zrzut ekranu przedstawiający tworzenie konta magazynu w witrynie Azure portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Ustawienia uprawnień dla tożsamości zarządzanej konta Data Lake Storage Gen2

Przypisz tożsamość zarządzaną do **właściciela danych obiektu Blob magazynu** roli na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzaj praw dostępu do danych obiektów Blob platformy Azure i kolejek przy użyciu RBAC (wersja zapoznawcza)](../storage/common/storage-auth-aad-rbac.md).

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.
1. Wybierz konto magazynu, a następnie wybierz **kontrola dostępu (IAM)** Aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz **przypisań ról** kartę, aby wyświetlić listę przypisań ról.
    
    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Wybierz **+ Dodaj przypisanie roli** przycisk, aby dodać nową rolę.
1. W **Dodaj przypisanie roli** wybierz **właściciela danych obiektu Blob magazynu** roli. Następnie wybierz subskrypcję, która ma zarządzanych tożsamości i konta magazynu. Następnie wyszukaj, aby zlokalizować użytkownik przypisany zarządzanych tożsamości, który został utworzony wcześniej. Na koniec wybierz pozycję tożsamość zarządzaną i będzie ono wyświetlane w obszarze **wybrane elementy członkowskie**.
    
    ![Zrzut ekranu przedstawiający sposób Przypisz rolę RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Wybierz pozycję **Zapisz**. Tożsamości przypisanych przez użytkownika, wybrany znajduje się teraz w ramach wybranej roli.
1. Po ukończeniu tej konfiguracji początkowej można utworzyć klastra za pośrednictwem portalu. Klaster musi być w tym samym regionie platformy Azure jako konto magazynu. W **magazynu** sekcji menu tworzenia klastra wybierz następujące opcje:
        
    * Aby uzyskać **podstawowy typ magazynu**, wybierz opcję **usługi Azure Data Lake Storage Gen2**.
    * W obszarze **wybierz konto magazynu**, wyszukaj i wybierz nowo utworzone konto magazynu Data Lake Storage Gen2.
        
        ![Ustawienia magazynu Data Lake Storage Gen2 przy użyciu usługi Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * W obszarze **tożsamości**, wybierz poprawną subskrypcję, a nowo utworzony przypisanych przez użytkownika tożsamości zarządzanej.
        
        ![Ustawienia tożsamości Data Lake Storage Gen2 przy użyciu usługi Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Można dodać co najmniej jednego konta Data Lake Storage Gen2 jako pomocniczego magazynu na tym samym klastrze. Po prostu Powtórz powyższe kroki dla każdego konta Data Lake Storage Gen2, który chcesz dodać, używając tej samej tożsamości zarządzanej.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Tworzenie klastra z usługą Data Lake Storage Gen2 za pośrednictwem wiersza polecenia platformy Azure

Możesz [pobrać przykładowy plik szablonu](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) i [pobrać przykładowy plik parametrów](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Przed rozpoczęciem korzystania z tego szablonu, Zastąp ciąg `<SUBSCRIPTION_ID>` przy użyciu swojego identyfikatora rzeczywiste subskrypcji platformy Azure. Ponadto Zastąp ciąg `<PASSWORD>` z wybranym hasło można ustawić zarówno hasło, które będzie używane do logowania do klastra i hasło protokołu SSH.

Poniższy fragment kodu wykonuje następujące kroki początkowe:

1. Dzienniki w do konta platformy Azure.
1. Ustawia aktywną subskrypcję, w której zostanie wykonane operacje tworzenia.
1. Tworzy nową grupę zasobów dla nowych działań wdrożenia o nazwie `hdinsight-deployment-rg`.
1. Tworzy przypisanych przez użytkownika tożsamości zarządzanej o nazwie `test-hdinsight-msi`.
1. Dodaje rozszerzenie do wiersza polecenia platformy Azure, aby używać funkcji dla Data Lake Storage Gen2.
1. Tworzy nowe konto Data Lake Storage Gen2 o nazwie `hdinsightadlsgen2`, za pomocą `--hierarchical-namespace true` flagi.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Następnie zaloguj się do portalu. Dodaj nowy użytkownik przypisany zarządzaną tożsamością do **Współautor danych obiektu Blob magazynu** roli na koncie magazynu, zgodnie z opisem w kroku 3 w sekcji [przy użyciu witryny Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Po przypisaniu roli dla zarządzanych tożsamości przypisanych przez użytkownika, należy wdrożyć szablon przy użyciu poniższego fragmentu kodu.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu dla usługi Data Lake Storage Gen2 w HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakiego rodzaju uprawnień obsługuje usługa Data Lake Storage Gen2?

Data Lake Storage Gen2 korzysta z modelu kontroli dostępu, który obsługuje zarówno kontroli dostępu opartej na rolach (RBAC), jak i listy kontroli dostępu POSIX i podobne (kontroli dostępu ACL). Usługa Data Lake Storage Gen1 obsługuje dostęp do listy kontroli tylko w celu kontrolowania dostępu do danych.

RBAC używa przypisania roli do efektywnego dotyczą zestawów uprawnień użytkownikom, grupom i nazwy główne usług dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do najwyższego poziomu zasobów (na przykład konta usługi Azure storage). Dla usługi Azure Storage, a także Data Lake Storage Gen2 ten mechanizm został rozszerzony do zasobów systemu plików.

 Aby uzyskać więcej informacji na temat uprawnień pliku za pomocą funkcji RBAC zobacz [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Aby uzyskać więcej informacji na temat uprawnień pliku za pomocą list ACL zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak kontrolować dostęp do swoich danych w Data Lake Storage Gen2?

Klaster HDInsight możliwość uzyskiwania dostępu do plików w Data Lake Storage Gen2 steruje się za pomocą zarządzanych tożsamości. Tożsamość zarządzana jest tożsamością zarejestrowane w usłudze Azure Active Directory (Azure AD) którego poświadczenia są zarządzane przez platformę Azure. Za pomocą tożsamości zarządzanej nie trzeba zarejestrować nazwy główne usług w usłudze Azure AD lub Obsługa poświadczeń, takich jak certyfikaty.

Usług platformy Azure ma dwa typy zarządzanych tożsamości: przypisana przez system i przypisanych przez użytkownika. HDInsight korzysta z tożsamości zarządzanej Data Lake Storage Gen2 dostęp do przypisanych przez użytkownika. Użytkownik przypisany tożsamość zarządzana zostanie utworzona jako autonomiczny zasobów platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji o zarządzanych tożsamości, zobacz [jak zarządzanych tożsamości do pracy z zasobami platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak ustawić uprawnienia dla użytkowników usługi Azure AD do zapytania o dane w Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług

Aby ustawić uprawnienia dla użytkowników zapytania o dane, należy użyć grup zabezpieczeń usługi Azure AD jako przypisanej jednostki na listach ACL w. Bezpośrednio nie przypisuj uprawnienia dostępu do plików do poszczególnych użytkowników lub jednostki usługi. Korzystając z grup zabezpieczeń usługi Azure AD do sterowania przepływem uprawnienia, możesz dodać i usunąć użytkowników lub jednostek usług bez ponowne zastosowanie listy ACL do całej struktury katalogów. Wystarczy tylko dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. Nie są dziedziczone listy ACL, dlatego w celu ponownego zastosowania list ACL wymaga zaktualizowania listy ACL dla każdego pliku i podkatalogu.

## <a name="next-steps"></a>Kolejne kroki

* [Użyj usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza) przy użyciu klastrów usługi Azure HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Integracja usługi Azure HDInsight w wersji zapoznawczej Data Lake Storage Gen2 — lista ACL i zabezpieczeń aktualizacji](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do usługi Azure Data Lake Storage 2. generacji (wersja zapoznawcza)](../storage/blobs/data-lake-storage-introduction.md)
