| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Zasoby na [grupy zasobów](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (na typ zasobu) |800 |Zmienia się na typ zasobu |
| Wdrożenia dla każdej grupy zasobów w historii wdrożenia |800 |800 |
| Zasoby dla wdrożenia |800 |800 |
| Blokady zarządzania (na unikatowy zakres) |20 |20 |
| Liczba tagów (na zasób lub grupa zasobów) |15 |15 |
| Długość klucza tagu |512 |512 |
| Długość wartości tagu |256 |256 |


#### <a name="template-limits"></a>Limity szablonu

| Wartość | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Parametry |256 |256 |
| Zmienne |256 |256 |
| Zasoby (w tym liczba kopii) |800 |800 |
| Dane wyjściowe |64 |64 |
| Wyrażenia szablonu |24 576 znaków |24 576 znaków |
| Zasoby w wyeksportowanych szablonów |200 |200 | 
| Rozmiar szablonu |1 MB |1 MB |
| Rozmiar pliku parametrów |64 KB |64 KB |

Niektóre limity szablonu może przekroczyć przy użyciu szablonu zagnieżdżonego. Aby uzyskać więcej informacji, zobacz [przy użyciu szablonów połączonych w przypadku wdrażania zasobów Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub dane wyjściowe, można połączyć kilka wartości do obiektu. Aby uzyskać więcej informacji, zobacz [obiektów jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Jeśli użytkownik osiągnie limit 800 wdrożeń dla grupy zasobów, usunięcie wdrożeń z historii, które nie są już potrzebne. Możesz usunąć pozycji z historii kodu za pomocą [usunąć wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_delete) dla wiersza polecenia platformy Azure, lub [AzureRmResourceGroupDeployment Usuń](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) w programie PowerShell. Usuwanie wpisu z historii wdrożenia nie ma wpływu na zasoby wdrażania. 