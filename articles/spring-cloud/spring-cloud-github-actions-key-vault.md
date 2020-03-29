---
title: Uwierzytelnij wiosenną chmurę platformy Azure za pomocą usługi Key Vault w akcjach usługi GitHub
description: Jak korzystać z magazynu kluczy z przepływem pracy ciągłej integracji/ciągłego wdrażania dla usługi Azure Spring Cloud za pomocą akcji GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945466"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Uwierzytelnij wiosenną chmurę platformy Azure za pomocą usługi Key Vault w akcjach usługi GitHub
Magazyn kluczy jest bezpiecznym miejscem do przechowywania kluczy. Użytkownicy korporacyjni muszą przechowywać poświadczenia dla środowisk ciągłej integracji/ciągłego wdrażania w kontrolowanym przez nie zakresie. Klucz, aby uzyskać poświadczenia w magazynie kluczy powinny być ograniczone do zakresu zasobów.  Ma dostęp tylko do zakresu magazynu kluczy, a nie do całego zakresu platformy Azure. To jak klucz, który może tylko otworzyć silne pudełko, a nie klucz główny, który może otworzyć wszystkie drzwi w budynku. Jest to sposób, aby uzyskać klucz z innym kluczem, który jest przydatny w przepływie pracy CICD. 

## <a name="generate-credential"></a>Generowanie poświadczeń
Aby wygenerować klucz, aby uzyskać dostęp do magazynu kluczy, wykonaj polecenie poniżej na komputerze lokalnym:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Zakres określony przez `--scopes` parametr ogranicza dostęp klucza do zasobu.  Dostęp do silnego pudełka jest dostępny tylko dla silnego pola.

Z wynikami:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Następnie zapisz wyniki w **wpisach tajnych** Usługi GitHub zgodnie z opisem w [obszarze Konfigurowanie repozytorium Usługi GitHub i uwierzytelnij się za pomocą platformy Azure.](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)

## <a name="add-access-policies-for-the-credential"></a>Dodawanie zasad dostępu dla poświadczeń
Poświadczenia utworzone powyżej można uzyskać tylko ogólne informacje o Przechowalni kluczy, a nie zawartość, którą przechowuje.  Aby uzyskać wpisy tajne przechowywane w magazynie kluczy, należy ustawić zasady dostępu dla poświadczeń.

Przejdź do pulpitu nawigacyjnego **Usługi Key Vault** w witrynie Azure **Apps** portal, **Type** kliknij **scope**menu **Kontrola dostępu,** a następnie otwórz kartę **Przypisania ról.** `This resource`  Poświadczenia utworzone w poprzednim kroku powinny być widoczne:

 ![Ustawianie zasad dostępu](./media/github-actions/key-vault1.png)

Skopiuj nazwę `azure-cli-2020-01-19-04-39-02`poświadczeń, na przykład . Otwórz menu **Zasady programu Access,** kliknij **+Dodaj łącze Zasady dostępu.**  Wybierz `Secret Management` pozycję **Szablon**, a następnie wybierz pozycję **Główny**. Wklej nazwę poświadczeń w polu**wprowadzania wyboru** **głównego:**/

 ![Wybierz pozycję](./media/github-actions/key-vault2.png)

 Kliknij przycisk **Dodaj** w oknie dialogowym **Dodawanie zasad dostępu,** a następnie kliknij pozycję **Zapisz**.

## <a name="generate-full-scope-azure-credential"></a>Generowanie poświadczeń platformy Azure w pełnym zakresie
Jest to klucz główny, aby otworzyć wszystkie drzwi w budynku. Procedura jest podobna do poprzedniego kroku, ale w tym miejscu zmieniamy zakres, aby wygenerować klucz główny:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Ponownie, wyniki:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Skopiuj cały ciąg JSON.  Bo powrót do **pulpitu nawigacyjnego Magazynu kluczy.** Otwórz menu **Wpisy tajne,** a następnie kliknij przycisk **Generuj/Importuj.** Wprowadź nazwę tajną, `AZURE-CRENDENTIALS-FOR-SPRING`taką jak . Wklej ciąg poświadczeń JSON do pola **wprowadzania Wartość.** Można zauważyć, że pole wprowadzania wartości jest jednowierszowym polem tekstowym, a nie wielowierszowym obszarem tekstowym.  Można tam wkleić pełny ciąg JSON.

 ![Poświadczenia pełnego zakresu](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Łączenie poświadczeń w akcjach usługi GitHub
Ustaw poświadczenia używane podczas wykonywania potoku CICD:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Następne kroki
* [Wiosenne działania GitHub w chmurze](./spring-cloud-howto-github-actions.md)