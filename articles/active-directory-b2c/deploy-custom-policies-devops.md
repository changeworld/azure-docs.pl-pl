---
title: Wdrażanie zasad niestandardowych za pomocą potoków platformy Azure
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wdrożyć zasady niestandardowe usługi Azure AD B2C w potoku ciągłej integracji/ciągłego wdrażania przy użyciu potoków platformy Azure w usługach Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188753"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Wdrażanie zasad niestandardowych za pomocą potoków platformy Azure

Przy użyciu potoku ciągłej integracji i dostarczania (CI/CD), który został skonfigurowany w [usłudze Azure Pipelines,][devops-pipelines]można uwzględnić niestandardowe zasady usługi Azure AD B2C w automatyzacji dostarczania oprogramowania i kontroli kodu. Podczas wdrażania w różnych środowiskach usługi Azure AD B2C, na przykład dev, test i produkcji, zaleca się usunięcie procesów ręcznych i wykonywanie automatycznych testów przy użyciu usługi Azure Pipelines.

Istnieją trzy podstawowe kroki wymagane do włączenia usługi Azure Potoki do zarządzania zasadami niestandardowymi w usłudze Azure AD B2C:

1. Tworzenie rejestracji aplikacji sieci Web w dzierżawie usługi Azure AD B2C
1. Konfigurowanie usługi Azure Repo
1. Konfigurowanie potoku platformy Azure

> [!IMPORTANT]
> Zarządzanie niestandardowymi zasadami usługi Azure AD B2C za pomocą usługi Azure `/beta` Pipeline obecnie używa operacji w wersji **zapoznawczej** dostępnych w punkcie końcowym interfejsu API programu Microsoft Graph. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych. Aby uzyskać więcej informacji, zobacz [odwołanie do punktu końcowego beta interfejsu API interfejsu API](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)systemu Microsoft Graph .

## <a name="prerequisites"></a>Wymagania wstępne

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)i poświadczenia dla użytkownika w katalogu z rolą [administratora zasad IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Zasady niestandardowe](custom-policy-get-started.md) przesłane do dzierżawy
* [Aplikacja do zarządzania](microsoft-graph-get-started.md) zarejestrowana w dzierżawie za pomocą interfejsu API programu Microsoft Graph *Policy.ReadWrite.TrustFramework*
* [Usługa Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)i dostęp do [projektu usługi Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Przepływ dotacji poświadczeń klienta

Scenariusz opisany w tym miejscu korzysta z wywołań usługi do usługi między potokami platformy Azure i usługi Azure AD B2C przy użyciu OAuth 2.0 [poświadczeń przepływać dotacji.](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md) Ten przepływ dotacji zezwala usługi sieci web, takich jak usługi Azure Pipelines (klient poufny) do korzystania z własnych poświadczeń zamiast personifikacji użytkownika do uwierzytelniania podczas wywoływania innej usługi sieci web (microsoft graph interfejsu API, w tym przypadku). Usługa Azure Pipelines uzyskuje token nieinterakcyjnie, a następnie żąda do interfejsu API programu Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Rejestrowanie aplikacji do zadań zarządzania

Jak wspomniano w [wymagania wstępne,](#prerequisites)potrzebujesz rejestracji aplikacji, które skrypty programu PowerShell — wykonywane przez usługi Azure Potoki — można użyć do uzyskiwania dostępu do zasobów w dzierżawie.

Jeśli masz już rejestrację aplikacji używaną do zadań automatyzacji, upewnij się, że została ona przyznana uprawnienie Zasad > **wykresu** >  **firmy****Microsoft.ReadWrite.TrustFramework** w **uprawnieniach interfejsu API** rejestracji aplikacji.

Aby uzyskać instrukcje dotyczące rejestrowania aplikacji do zarządzania, zobacz [Zarządzanie usługą Azure AD B2C za pomocą programu Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurowanie usługi Azure Repo

Po zarejestrowaniu aplikacji do zarządzania możesz skonfigurować repozytorium plików zasad.

1. Zaloguj się do organizacji usług Azure DevOps.
1. [Utwórz nowy projekt][devops-create-project] lub wybierz istniejący projekt.
1. W projekcie przejdź do **pozycji Repozytorium** i wybierz stronę **Pliki.** Wybierz istniejące repozytorium lub utwórz je dla tego ćwiczenia.
1. Utwórz folder o nazwie *B2CAssets*. Nazwij wymagany plik zastępczy *README.md* i **Zatwierdz plik.** Możesz usunąć ten plik później, jeśli chcesz.
1. Dodaj pliki zasad usługi Azure AD B2C do folderu *B2CAssets.* Obejmuje to *trustframeworkbase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*i inne utworzone zasady. Nagraj nazwę pliku każdego pliku zasad usługi Azure AD B2C do użycia w późniejszym kroku (są one używane jako argumenty skryptu programu PowerShell).
1. Utwórz folder o nazwie *Skrypty* w katalogu głównym repozytorium, nazwij plik zastępczy *DeployToB2c.ps1*. Nie zatwierdzaj pliku w tym momencie, zrobisz to w późniejszym kroku.
1. Wklej następujący skrypt programu PowerShell do *pliku DeployToB2c.ps1*, a następnie **zatwierdź** plik. Skrypt uzyskuje token z usługi Azure AD i wywołuje interfejs API programu Microsoft Graph, aby przekazać zasady w folderze *B2CAssets* do dzierżawy usługi Azure AD B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurowanie potoku platformy Azure

Po zainicjowaniu repozytorium i wypełnieniu plikami zasad niestandardowych możesz skonfigurować potok wersji.

### <a name="create-pipeline"></a>Tworzenie potoku

1. Zaloguj się do organizacji usługi Azure DevOps i przejdź do projektu.
1. W projekcie wybierz pozycję **Pipelines** > **Releases** > **New pipeline**.
1. W obszarze **Wybierz szablon**wybierz pozycję **Puste zadanie**.
1. Wprowadź **nazwę stołu montażowego**, na przykład *DeployCustomPolicies*, a następnie zamknij okienko.
1. Wybierz **pozycję Dodaj artefakt**i w obszarze Typ **źródła**wybierz pozycję **Repozytorium platformy Azure**.
    1. Wybierz repozytorium źródłowe zawierające folder *Skrypty* wypełnione skryptem programu PowerShell.
    1. Wybierz **domyślną gałąź**. Jeśli w poprzedniej sekcji utworzono nowe repozytorium, domyślną gałęzią jest *master*.
    1. Pozostaw **domyślne** ustawienie wersji *Latest z domyślnej gałęzi*.
    1. Wprowadź **alias źródłowy** dla repozytorium. Na przykład *policyRepo*. Nie należy umieszczać żadnych spacji w nazwie aliasu.
1. Wybierz **pozycję Dodaj**
1. Zmień nazwę potoku, aby odzwierciedlić jego intencji. Na przykład *wdrażanie potoku zasad niestandardowych*.
1. Wybierz **pozycję Zapisz,** aby zapisać konfigurację potoku.

### <a name="configure-pipeline-variables"></a>Konfigurowanie zmiennych potoku

1. Wybierz kartę **Zmienne.**
1. Dodaj następujące zmienne w obszarze **Zmienne potoku** i ustaw ich wartości w określony sposób:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `clientId` | **Identyfikator aplikacji (klienta)** aplikacji zarejestrowanej wcześniej. |
    | `clientSecret` | Wartość **klucza tajnego klienta,** który został utworzony wcześniej. <br /> Zmień typ zmiennej na **tajny** (wybierz ikonę kłódki). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, gdzie *twoja dzierżawa b2c* to nazwa dzierżawy usługi Azure AD B2C. |

1. Wybierz **pozycję Zapisz,** aby zapisać zmienne.

### <a name="add-pipeline-tasks"></a>Dodawanie zadań potoku

Następnie dodaj zadanie, aby wdrożyć plik zasad.

1. Wybierz kartę **Zadania.**
1. Wybierz zadanie **agenta**, a**+** następnie wybierz znak plus ( ), aby dodać zadanie do zadania agenta.
1. Wyszukaj i wybierz pozycję **PowerShell**. Nie należy wybierać "Azure PowerShell", "PowerShell na komputerach docelowych" lub innego wpisu programu PowerShell.
1. Wybierz nowo dodane zadanie **skryptu programu PowerShell.**
1. Wprowadź następujące wartości zadania skryptu programu PowerShell:
    * **Wersja zadania**: 2.*
    * **Nazwa wyświetlana**: Nazwa zasad, które należy przekazać to zadanie. Na przykład *B2C_1A_TrustFrameworkBase*.
    * **Typ**: Ścieżka pliku
    * **Ścieżka skryptu**: Wybierz wielokropek (***...***), przejdź do folderu *Skrypty,* a następnie wybierz plik *DeployToB2C.ps1.*
    * **Argumenty:**

        Wprowadź następujące wartości **argumentów**. Zamień `{alias-name}` alias określony w poprzedniej sekcji.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Na przykład, jeśli podany alias to *policyRepo*, wiersz argumentu powinien być:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Wybierz **pozycję Zapisz,** aby zapisać zadanie agenta.

Zadanie, które właśnie dodano, przekazuje *jeden* plik zasad do usługi Azure AD B2C. Przed kontynuowaniem ręcznie wyzwolić zadanie **(Utwórz zwolnienie),** aby upewnić się, że zakończy się pomyślnie przed utworzeniem dodatkowych zadań.

Jeśli zadanie zakończy się pomyślnie, dodaj zadania wdrażania, wykonując poprzednie kroki dla każdego z niestandardowych plików zasad. Modyfikowanie `-PolicyId` `-PathToFile` wartości argumentów i dla każdej zasady.

Jest `PolicyId` to wartość znaleziona na początku pliku zasad XML w węźle TrustFrameworkPolicy. Na przykład `PolicyId` w poniższej polityce XML jest *B2C_1A_TrustFrameworkBase:*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Podczas uruchamiania agentów i przekazywania plików zasad upewnij się, że są one przekazywane w następującej kolejności:

1. *Strona ZaufaniaFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *Plik ProfilEdit.xml*
1. *Plik PasswordReset.xml*

Struktura środowiska tożsamości wymusza tę kolejność, ponieważ struktura plików jest zbudowana na hierarchicznym łańcuchu.

## <a name="test-your-pipeline"></a>Testowanie potoku

Aby przetestować potok wersji:

1. Wybierz **pozycję Potoki,** a następnie **zwolnij**.
1. Wybierz utworzony wcześniej potok, na przykład *DeployCustomPolicies*.
1. Wybierz **pozycję Utwórz zwolnienie**, a następnie wybierz pozycję **Utwórz,** aby kolejkować wersję.

Powinien zostać wyświetlony baner powiadomień z informacją, że wydanie zostało umieszczone w kolejce. Aby wyświetlić jego stan, wybierz łącze na banerze powiadomień lub wybierz je na liście na karcie **Wydania.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

* [Wywołania usługi do serwisu przy użyciu poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Usługa Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
