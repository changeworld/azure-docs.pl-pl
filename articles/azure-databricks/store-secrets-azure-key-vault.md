---
title: Samouczek — dostęp do magazynu obiektów blob przy użyciu magazynu kluczy przy użyciu usługi Azure Databricks
description: W tym samouczku opisano sposób uzyskiwania dostępu do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu wpisów tajnych przechowywanych w magazynie kluczy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889057"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Samouczek: uzyskaj dostęp do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu usługi Azure Key Vault

W tym samouczku opisano sposób uzyskiwania dostępu do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu wpisów tajnych przechowywanych w magazynie kluczy.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu i kontenera obiektów blob
> * Tworzenie usługi Azure Key Vault i dodawanie klucza tajnego
> * Tworzenie obszaru roboczego usługi Azure Databricks i dodawanie tajnego zakresu
> * Dostęp do kontenera obiektów blob z usługi Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

> [!Note]
> Tego samouczka nie można przeprowadzić przy użyciu **bezpłatnej subskrypcji próbnej platformy Azure.**
> Jeśli masz darmowe konto, przejdź do swojego profilu i zmień subskrypcję na **płatność zgodnie z rzeczywistymami.** Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [poproś o zwiększenie przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych w twoim regionie. Podczas tworzenia obszaru roboczego usługi Azure Databricks, można wybrać **warstwę cenową trial (Premium — 14-dniowe bezpłatne dbu),** aby dać obszarowi roboczemu dostęp do bezpłatnych witryn dbu usługi Premium Azure Databricks przez 14 dni.

## <a name="create-a-storage-account-and-blob-container"></a>Tworzenie konta magazynu i kontenera obiektów blob

1. W witrynie Azure portal wybierz pozycję **Utwórz magazyn zasobów** > **Storage**. Następnie wybierz **pozycję Konto magazynu**.

   ![Znajdowanie zasobu konta magazynu platformy Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Wybierz subskrypcję i grupę zasobów lub utwórz nową grupę zasobów. Następnie wprowadź nazwę konta magazynu i wybierz lokalizację. Wybierz **pozycję Recenzja + Utwórz**.

   ![Ustawianie właściwości konta magazynu](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Jeśli sprawdzanie poprawności nie powiedzie się, rozwiąże problemy i spróbuj ponownie. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Utwórz** i poczekaj na utworzenie konta magazynu.

4. Przejdź do nowo utworzonego konta magazynu i wybierz pozycję **Obiekty blob** w obszarze **Usługi** na stronie **Przegląd.** Następnie wybierz **+ Kontener** i wprowadź nazwę kontenera. Kliknij przycisk **OK**.

   ![Tworzenie nowego kontenera](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Znajdź plik, który chcesz przekazać do kontenera magazynu obiektów blob. Jeśli nie masz pliku, użyj edytora tekstu, aby utworzyć nowy plik tekstowy z pewnymi informacjami. W tym przykładzie plik o nazwie **hw.txt** zawiera tekst "hello world". Zapisz plik tekstowy lokalnie i przekaż go do kontenera magazynu obiektów blob.

   ![Przekazywanie pliku do kontenera](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Wróć do konta magazynu i wybierz pozycję **Klawisze dostępu** w obszarze **Ustawienia**. **Kopiuj nazwę konta magazynu** i **klucz 1** do edytora tekstu do późniejszego użycia w tym samouczku.

   ![Znajdowanie kluczy dostępu do konta magazynu](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Tworzenie usługi Azure Key Vault i dodawanie klucza tajnego

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** i wprowadź w polu wyszukiwania **magazyn kluczy.**

   ![Tworzenie pola wyszukiwania zasobów platformy Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Zasób Przechowalnia kluczy jest wybierany automatycznie. Wybierz **pozycję Utwórz**.

   ![Tworzenie zasobu magazynu kluczy](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na stronie **Tworzenie magazynu kluczy** wprowadź następujące informacje i zachowaj wartości domyślne dla pozostałych pól:

   |Właściwość|Opis|
   |--------|-----------|
   |Nazwa|Unikatowa nazwa magazynu kluczy.|
   |Subskrypcja|wybierz subskrypcję.|
   |Grupa zasobów|Wybierz grupę zasobów lub utwórz nową.|
   |Lokalizacja|Wybierz lokalizację.|

   ![Właściwości magazynu kluczy platformy Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Po podaniu powyższych informacje wybierz przycisk **Utwórz**. 

4. Przejdź do nowo utworzonego magazynu kluczy w witrynie Azure portal i wybierz pozycję **Wpisy tajne**. Następnie wybierz pozycję **+ Generuj/Zaimportuj**. 

   ![Generowanie nowego klucza tajnego magazynu kluczy](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na stronie **Utwórz klucz tajny** podaj następujące informacje i zachowaj wartości domyślne dla pozostałych pól:

   |Właściwość|Wartość|
   |--------|-----------|
   |Opcje przekazywania|Ręcznie|
   |Nazwa|Przyjazna nazwa klucza konta magazynu.|
   |Wartość|key1 z konta pamięci masowej.|

   ![Właściwości nowego klucza tajnego magazynu kluczy](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Zapisz nazwę klucza w edytorze tekstu do użycia w dalszej części tego samouczka, a następnie wybierz pozycję **Utwórz**. Następnie przejdź do menu **Właściwości.** Skopiuj **nazwę DNS** i **identyfikator zasobu** do edytora tekstu do użycia w dalszej części samouczka.

   ![Kopiowanie nazwy DNS usługi Azure Key Vault i identyfikatora zasobu](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Tworzenie obszaru roboczego usługi Azure Databricks i dodawanie tajnego zakresu

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Azure Databricks**.

    ![Databricks w witrynie Azure portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. W obszarze **Usługa Azure Databricks podaj**następujące wartości, aby utworzyć obszar roboczy Databricks.

   |Właściwość  |Opis  |
   |---------|---------|
   |Nazwa obszaru roboczego     | Podaj nazwę obszaru roboczego usługi Databricks.        |
   |Subskrypcja     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
   |Grupa zasobów     | Wybierz tę samą grupę zasobów zawierającą magazyn kluczy. |
   |Lokalizacja     | Wybierz tę samą lokalizację co usługa Azure Key Vault. W przypadku wszystkich dostępnych regionów zobacz [Usługi platformy Azure dostępne według regionu](https://azure.microsoft.com/regions/services/).        |
   |Warstwa cenowa     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Właściwości obszaru roboczego Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Wybierz **pozycję Utwórz**.

3. Przejdź do nowo utworzonego zasobu usługi Azure Databricks w witrynie Azure portal i wybierz pozycję **Uruchom obszar roboczy**.

   ![Uruchamianie obszaru roboczego usługi Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Gdy obszar roboczy usługi Azure Databricks jest otwarty w osobnym oknie, dołącz **#secrets/createScope** do adresu URL. Adres URL powinien mieć następujący format: 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Wprowadź nazwę zakresu i wprowadź wcześniej zapisaną nazwę DNS usługi Azure Key Vault i identyfikator zasobu. Zapisz nazwę zakresu w edytorze tekstu do użycia w dalszej części tego samouczka. Następnie wybierz przycisk **Create** (Utwórz).

   ![Tworzenie tajnego zakresu w obszarze roboczym usługi Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Dostęp do kontenera obiektów blob z usługi Azure Databricks

1. Na stronie głównej obszaru roboczego usługi Azure Databricks wybierz pozycję **Nowy klaster** w obszarze **Typowe zadania**.

   ![Tworzenie nowego notesu usługi Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Wprowadź nazwę klastra i wybierz pozycję **Utwórz klaster**. Tworzenie klastra trwa kilka minut, aby zakończyć.

3. Po utworzeniu klastra przejdź do strony głównej obszaru roboczego usługi Azure Databricks, wybierz pozycję **Nowy notes** w obszarze **Typowe zadania**.

   ![Tworzenie nowego notesu usługi Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Wprowadź nazwę notesu i ustaw język na Python. Ustaw klaster na nazwę klastra utworzonego w poprzednim kroku.

5. Uruchom następujące polecenie, aby zainstalować kontener magazynu obiektów blob. Pamiętaj, aby zmienić wartości dla następujących właściwości:

   * nazwa twojego kontenera
   * nazwa konta magazynu
   * nazwa instalacji
   * config-key
   * nazwa zakresu
   * nazwa klucza

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **nazwa instalacji** to ścieżka DBFS reprezentująca, w której zostanie zainstalowany kontener magazynu obiektów blob lub folder wewnątrz kontenera (określony w źródle).
   * **conf-key** może być `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` albo`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** to nazwa tajnego zakresu utworzonego w poprzedniej sekcji. 
   * **nazwa klucza** to nazwa klucza tajnego utworzonego dla klucza konta magazynu w magazynie kluczy.

   ![Tworzenie uchwytu magazynu obiektów blob w notesie](./media/store-secrets-azure-key-vault/command1.png)

6. Uruchom następujące polecenie, aby odczytać plik tekstowy w kontenerze magazynu obiektów blob do ramki danych. Zmień wartości w poleceniu, aby dopasować go do nazwy instalacji i nazwy pliku.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Odczyt pliku do ramki danych](./media/store-secrets-azure-key-vault/command2.png)

7. Użyj następującego polecenia, aby wyświetlić zawartość pliku.

   ```python
   df.show()
   ```
   ![Pokaż ramę danych](./media/store-secrets-azure-key-vault/command3.png)

8. Aby odinstalować magazyn obiektów blob, uruchom następujące polecenie:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Odinstaluj konto magazynu](./media/store-secrets-azure-key-vault/command4.png)

9. Należy zauważyć, że po odinstalowaniu instalacji nie można już odczytać z konta magazynu obiektów blob.

   ![Błąd odinstalowynia konta magazynu](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń całą grupę zasobów, wykonując następujące czynności:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **grup zasobów** i przejdź do grupy zasobów.

2. Wybierz **pozycję Usuń grupę zasobów** i wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zaimplementować środowisko Databricks wstrzykuje się w sieci wirtualnej z włączonym punktem końcowym usługi dla usługi Cosmos DB.
> [!div class="nextstepaction"]
> [Samouczek: Implementowanie usługi Azure Databricks z punktem końcowym usługi Cosmos DB](service-endpoint-cosmosdb.md)
