Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Aby wyświetlić dostępne lokalizacje, użyj polecenia `az appservice list-locations`. Zasadniczo tworzysz zasoby w pobliskim regionie.
