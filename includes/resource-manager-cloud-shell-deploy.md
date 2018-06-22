## <a name="deploy-template-from-cloud-shell"></a>Wdrażanie szablonu za pomocą usługi Cloud Shell

Do wdrożenia swojego szablonu możesz użyć usługi [Cloud Shell](../articles/cloud-shell/overview.md). Jednak należy najpierw załadować szablonu do konta magazynu dla powłoki chmury. Jeśli nie używasz usługi Cloud Shell, zobacz [Overview of Azure Cloud Shell (Omówienie usługi Azure Cloud Shell)](../articles/cloud-shell/overview.md), aby uzyskać informacje o jej konfigurowaniu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz swoją grupę zasobów usługi Cloud Shell. Wzorzec nazwy to `cloud-shell-storage-<region>`.

   ![Wybieranie grupy zasobów](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Wybierz konto magazynu dla usługi Cloud Shell.

   ![Wybieranie konta magazynu](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Wybierz **obiekty BLOB**.

   ![Wybierz obiekty BLOB](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Wybierz **+ kontener**.

   ![Dodaj kontener](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Z kontenera należy podać nazwę i poziom dostępu. Przykładowy szablon w tym artykule nie zawiera żadnych poufnych informacji, pozwalają na anonimowy dostęp do odczytu. Kliknij przycisk **OK**.

   ![Podaj wartości kontenera](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Wybierz kontener, który został utworzony.

   ![Wybierz nowy kontener](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Wybierz pozycję **Przekaż**.

   ![Przekazywanie obiektu blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Znajdź i przekaż swój szablon.

   ![Przekazywanie pliku](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po przesłał, wybierz szablon.

   ![Wybierz nowy szablon](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Skopiuj adres URL.

   ![Skopiuj adres URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otwórz wiersz polecenia.

   ![Otwieranie usługi Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
