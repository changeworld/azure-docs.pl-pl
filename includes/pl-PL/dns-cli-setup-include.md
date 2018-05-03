## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure DNS

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Linux i MAC. Więcej informacji znajduje się w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Otwórz okno konsoli i uwierzytelnij się przy użyciu swoich poświadczeń. Aby uzyskać więcej informacji, zobacz [Log in to Azure from the Azure CLI](/cli/azure/authenticate-azure-cli) (Logowanie do platformy Azure z poziomu wiersza polecenia platformy Azure)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Przełącz tryb interfejsu wiersza polecenia

Usługa Azure DNS korzysta z usługi Azure Resource Manager. Pamiętaj, aby przełączyć tryb interfejsu wiersza polecenia w celu używania poleceń usługi Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Wybierz subskrypcję

Sprawdź subskrypcje dostępne na koncie.

```azurecli
azure account list
```

Wybierz subskrypcję platformy Azure do użycia.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Będzie ona używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Ponieważ jednak wszystkie zasoby DNS są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure DNS.

Ten krok można pominąć, jeśli używasz istniejącej grupy zasobów.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Usługa Azure DNS jest zarządzana przez dostawcę zasobów Microsoft.Network. Aby można było korzystać z usługi Azure DNS, Twoja subskrypcja platformy Azure musi być zarejestrowana do używania tego dostawcy zasobów. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
azure provider register --namespace Microsoft.Network
```

