---
title: Samouczek dotyczący używania usługi Azure App Configuration Key Vault References w aplikacji rozruchu ze sprężyną Java | Microsoft Docs
description: W tym samouczku dowiesz się, jak używać odwołań Key Vault konfiguracji aplikacji platformy Azure z poziomu aplikacji z rozruchem ze sprężyną Java
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 17d86f25de6eecee535d6f812f4ef0b078a4b6db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752515"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Samouczek: Używanie odwołań Key Vault w aplikacji ze sprężyną Java

W tym samouczku dowiesz się, jak korzystać z usługi Azure App Configuration razem z Azure Key Vault. Konfiguracja aplikacji i Key Vault to uzupełniające się usługi używane obok większości wdrożeń aplikacji.

Konfiguracja aplikacji ułatwia korzystanie z usług, tworząc klucze, które odwołują się do wartości przechowywanych w Key Vault. Gdy konfiguracja aplikacji tworzy takie klucze, przechowuje identyfikatory URI wartości Key Vault, a nie same wartości.

Aplikacja używa dostawcy klienta konfiguracji aplikacji do pobierania odwołań Key Vault, podobnie jak w przypadku innych kluczy przechowywanych w konfiguracji aplikacji. W takim przypadku wartości przechowywane w konfiguracji aplikacji to identyfikatory URI, które odwołują się do wartości w Key Vault. Nie Key Vault wartości ani poświadczeń. Ponieważ dostawca klienta rozpoznaje klucze jako odwołania Key Vault, używa Key Vault do pobierania ich wartości.

Aplikacja jest odpowiedzialna za prawidłowe uwierzytelnianie zarówno w konfiguracji aplikacji, jak i Key Vault. Te dwie usługi nie komunikują się bezpośrednio.

W tym samouczku pokazano, jak zaimplementować Key Vault odwołania w kodzie. Opiera się on na aplikacji internetowej wprowadzonej w przewodnikach Szybki start. Przed kontynuowaniem należy najpierw [utworzyć aplikację ze sprężyną Java z konfiguracją aplikacji](./quickstart-java-spring-app.md) .

Aby wykonać kroki opisane w tym samouczku, można użyć dowolnego edytora kodu. Na przykład [Visual Studio Code](https://code.visualstudio.com/) to Międzyplatformowy Edytor kodu, który jest dostępny dla systemów operacyjnych Windows, MacOS i Linux.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault.
> * Uzyskaj dostęp do wartości tego klucza z aplikacji ze sprężyną Java.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Tworzenie magazynu

1. Wybierz opcję **Utwórz zasób** w lewym górnym rogu Azure Portal:

    ![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/search-services.png)
1. W polu wyszukiwania wprowadź **Key Vault**.
1. Z listy wyników wybierz pozycję **magazyny kluczy** po lewej stronie.
1. W obszarze **magazyny kluczy**wybierz pozycję **Dodaj**.
1. Po prawej stronie w temacie **Tworzenie magazynu kluczy**podaj następujące informacje:
    - Wybierz pozycję **subskrypcja** , aby wybrać subskrypcję.
    - W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    - W **nazwie magazynu kluczy**wymagana jest unikatowa nazwa. Na potrzeby tego samouczka wprowadź **contoso-vault2**.
    - Z listy rozwijanej **region** wybierz lokalizację.
1. Pozostaw inne opcje **tworzenia magazynu kluczy** z wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

W tym momencie Twoje konto platformy Azure jest jedynym autoryzowanym dostępem do tego nowego magazynu.

![Dane wyjściowe po zakończeniu tworzenia magazynu kluczy](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, należy wykonać zaledwie kilka dodatkowych kroków. W takim przypadku Dodaj komunikat, którego można użyć do przetestowania pobierania Key Vault. Wiadomość jest nazywana **komunikatem**i przechowuje w niej wartość "Hello from Key Vault".

1. Na stronie właściwości Key Vault wybierz pozycję wpisy **tajne**.
1. Wybierz pozycję **Generuj/Importuj**.
1. W okienku **Utwórz wpis tajny** wprowadź następujące wartości:
    - **Opcje przekazywania**: wprowadź **Ręczne**.
    - **Nazwa**: wprowadź **komunikat**.
    - **Wartość**: wprowadź **Hello z Key Vault**.
1. Pozostaw inne właściwości **klucza tajnego** z wartościami domyślnymi.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodaj odwołanie Key Vault do konfiguracji aplikacji

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Wybierz pozycję **wszystkie zasoby**, a następnie wybierz wystąpienie magazynu konfiguracji aplikacji utworzone w ramach przewodnika Szybki Start.

1. Wybierz pozycję **Eksplorator konfiguracji**.

1. Wybierz pozycję **+ Utwórz** **odwołanie do magazynu kluczy** > , a następnie określ następujące wartości:
    - **Klucz**: Wybierz **/Application/config.keyvaultmessage**
    - **Etykieta**: pozostaw tę wartość pustą.
    - **Subskrypcja**, **Grupa zasobów**i **Magazyn kluczy**: wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzedniej sekcji.
    - **Wpis tajny**: Wybierz **komunikat** o nazwie Secret utworzony w poprzedniej sekcji.

## <a name="connect-to-key-vault"></a>Połącz z Key Vault

1. W tym samouczku użyjesz nazwy głównej usługi do uwierzytelniania do Key Vault. Aby utworzyć tę nazwę główną usługi, użyj interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ta operacja zwraca serię par klucz/wartość:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Uruchom następujące polecenie, aby umożliwić jednostce usługi dostęp do magazynu kluczy:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Utwórz następujące zmienne środowiskowe przy użyciu wartości dla jednostki usługi, która została wyświetlona w poprzednim kroku:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Te poświadczenia Key Vault są używane tylko w aplikacji. Aplikacja uwierzytelnia się bezpośrednio do Key Vault przy użyciu tych poświadczeń. Nie są one nigdy przesyłane do usługi konfiguracji aplikacji.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Zaktualizuj kod, aby użyć odwołania Key Vault

1. Otwórz *MessageProperties. Java*. Dodaj nową zmienną o nazwie *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otwórz *HelloController. Java*. Zaktualizuj metodę *GetMessage* , aby uwzględnić komunikat pobrany z Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Skompiluj aplikację do rozruchu ze sprężyną przy użyciu Maven i uruchom ją, na przykład:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
1. Po uruchomieniu aplikacji należy użyć *zazwinięcie* do przetestowania aplikacji, na przykład:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Zobaczysz komunikat wprowadzony w magazynie konfiguracji aplikacji. Zobaczysz również komunikat wprowadzony w Key Vault.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz klucz konfiguracji aplikacji, który odwołuje się do wartości przechowywanej w Key Vault. Aby dowiedzieć się, jak używać flag funkcji w aplikacji ze sprężyną Java, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./quickstart-feature-flag-spring-boot.md)
