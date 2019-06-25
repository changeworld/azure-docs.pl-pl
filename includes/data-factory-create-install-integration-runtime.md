---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: a2858ac73838b50c21a76db5860675171a306192
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183700"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z programu SQL Server na Twojej maszynie do magazynu usługi Azure Blob Storage. 

1. Utwórz zmienną dla nazwy środowiska Integration Runtime. Użyj unikatowej nazwy i zanotuj ją. Będziesz jej używać w dalszej części tego samouczka. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Utwórz własne środowisko Integration Runtime. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Oto przykładowe dane wyjściowe:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
  
3. Uruchom następujące polecenie, aby pobrać stan utworzonego środowiska Integration Runtime. Upewnij się, że właściwość **State** ma ustawioną wartość **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Oto przykładowe dane wyjściowe:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Uruchom następujące polecenie, aby pobrać klucze uwierzytelniania używane do zarejestrowania własnego środowiska Integration Runtime za pomocą usługi Azure Data Factory w chmurze: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Oto przykładowe dane wyjściowe:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Skopiuj jeden z kluczy (pomijając cudzysłowy) używanych do rejestracji własnego środowiska Integration Runtime, które zainstalujesz na swojej maszynie w kolejnych krokach.  

## <a name="install-the-integration-runtime"></a>Instalowanie środowiska Integration Runtime
1. Jeśli środowisko Integration Runtime jest już zainstalowane na maszynie, odinstaluj je za pomocą apletu **Dodaj lub usuń programy**. 

2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) środowisko Integration Runtime (Self-hosted) na lokalną maszynę z systemem Windows. Uruchom instalację.

3. Na **stronie powitalnej instalatora środowiska Microsoft Integration Runtime** wybierz przycisk **Dalej**.

4. Na stronie **Umowa Licencyjna Użytkownika Oprogramowania** zaakceptuj warunki i umowę licencyjną, a następnie wybierz przycisk **Dalej**.

5. Na stronie **Folder docelowy** wybierz pozycję **Dalej**.

6. Na stronie **Gotowe do zainstalowania środowiska Microsoft Integration Runtime** wybierz pozycję **Zainstaluj**.

7. Jeśli zostanie wyświetlony komunikat ostrzegawczy informujący o skonfigurowaniu komputera do przejścia w tryb uśpienia lub hibernacji, gdy nie będzie używany, wybierz przycisk **OK**.

8. Jeśli zostanie wyświetlona strona **Opcje zasilania**, zamknij ją i przejdź do strony instalatora.

9. N astronie **zakończenia pracy z Kreatorem instalacji środowiska Microsoft Integration Runtime** wybierz pozycję **Zakończ**.

10. Na stronie **Rejestrowanie środowiska Integration Runtime (Self-hosted)** wklej klucz zapisany w poprzedniej sekcji i wybierz pozycję **Zarejestruj**. 

    ![Rejestrowanie środowiska Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Gdy własne środowisko Integration Runtime zostanie pomyślnie zarejestrowane, zostanie wyświetlony następujący komunikat:

    ![Pomyślnie zarejestrowano](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Na stronie **Nowy węzeł Integration Runtime (Self-hosted)** wybierz pozycję **Dalej**. 

    ![Strona nowego węzła środowiska Integration Runtime (Self-hosted)](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Na stronie **Kanał komunikacji intranetowej** wybierz pozycję **Pomiń**. Wybierz certyfikację protokołów TLS/SSL w celu zabezpieczenia komunikacji w środowisku Integration Runtime z wieloma węzłami. 

    ![Strona Kanał komunikacji intranetowej](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. Na stronie **Rejestrowanie produktu Integration Runtime (Self-hosted)** kliknij pozycję **Uruchom program Configuration Manager**.

15. Gdy węzeł zostanie połączony z usługą w chmurze, zostanie wyświetlona następująca strona:

    ![Strona Węzeł jest połączony](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Teraz przetestuj łączność z bazą danych programu SQL Server.

    ![Karta Diagnostyka](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Na stronie programu **Configuration Manager** przejdź na kartę **Diagnostyka**.

    b. Wybierz typ źródła danych **SqlServer**.

    c. Wprowadź nazwę serwera.

    d. Wprowadź nazwę bazy danych.

    e. Wybierz tryb uwierzytelniania.

    f. Wprowadź nazwę użytkownika.

    g. Wprowadź hasło dla nazwy użytkownika.

    h. Wybierz przycisk **Testuj**, aby upewnić się, że środowisko Integration Runtime może połączyć się z wystąpieniem programu SQL Server. Jeśli połączenie zostanie pomyślnie nawiązane, zostanie wyświetlony zielony znacznik wyboru. Jeśli nawiązywanie połączenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie. Rozwiąż wszelkie problemy i upewnij się, że środowisko Integration Runtime może połączyć się z programem SQL Server.    

    > [!NOTE]
    > Zapisz wartości: typ uwierzytelniania, serwer, baza danych, użytkownik i hasło. Będziesz ich używać w dalszej części tego samouczka. 
    
