---
title: Klaster HPC Pack w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować klastra Microsoft HPC Pack 2016 na platformie Azure z usługą Azure Active Directory
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2017
ms.locfileid: "25452576"
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Zarządzanie klastra HPC Pack platformie Azure przy użyciu usługi Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) obsługuje integrację z [usługi Azure Active Directory](../../active-directory/index.md) (Azure AD) dla administratorów, którzy wdrożenie klastra HPC Pack na platformie Azure.



Wykonaj kroki opisane w tym artykule następujące zadania wysokiego poziomu: 
* Ręcznie integracji klastra HPC Pack z dzierżawy usługi Azure AD
* Zarządzanie i planowanie zadań w klastrze HPC Pack na platformie Azure 

Integracja z usługą Azure AD rozwiązanie klastra HPC Pack wykonał standardowe kroki, aby Integracja z innymi usługami i aplikacjami. W tym artykule przyjęto założenie, że czytelnik zna podstawowe użytkownika zarządzania w usłudze Azure AD. Aby uzyskać więcej informacji i tło, zobacz [dokumentacji usługi Azure Active Directory](../../active-directory/index.md) i poniższej sekcji.

## <a name="benefits-of-integration"></a>Korzyści wynikające z integracji


Azure Active Directory (Azure AD) to wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługa, która zapewnia dostęp rejestracji jednokrotnej (SSO) do rozwiązań w chmurze.

Integracja z usługą Azure AD klastra HPC Pack może pomóc w następujących celach:

* Usuń tradycyjnych kontrolera domeny usługi Active Directory z klastra HPC Pack. Ułatwia to zmniejszenie kosztów obsługi klastra, jeśli nie jest to konieczne dla firm i przyspieszyć proces wdrażania.
* Wykorzystać następujące korzyści, które zostały podane przy użyciu usługi Azure AD:
    *   Logowanie jednokrotne 
    *   Dla klastra HPC Pack na platformie Azure przy użyciu lokalnej tożsamości usługi AD 

    ![Środowiska usługi Active Directory platformy Azure](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Wymagania wstępne
* **Klaster HPC Pack 2016 wdrożonymi na maszynach wirtualnych Azure** — Aby uzyskać instrukcje, zobacz [wdrożenie klastra HPC Pack 2016 na platformie Azure](hpcpack-2016-cluster.md). Należy nazwy DNS węzła głównego i poświadczenia administratora klastra, aby wykonać kroki opisane w tym artykule.

  > [!NOTE]
  > Integracja z usługą Azure Active Directory nie jest obsługiwany w wersjach pakietu HPC przed HPC Pack 2016.



* **Komputer kliencki** -wymagają komputera klienckiego z systemem Windows lub Windows Server można uruchomić narzędzia klienta HPC Pack. Jeśli chcesz użyć portalu internetowego HPC Pack lub interfejsu API REST do przesyłania zadań, można użyć dowolnego komputera klienckiego wybranych przez użytkownika.

* **HPC Pack narzędzi klienta** -Zainstaluj narzędzia klienta HPC Pack na komputerze klienckim, używając pakietu instalacyjnego wolnego dostępne z Microsoft Download Center.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Krok 1: Zarejestruj serwer klastra HPC z dzierżawy usługi Azure AD
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy usługi Azure AD, kliknij konto w prawym górnym rogu. Następnie ustaw żądaną dzierżawy sesji portalu. Musi mieć uprawnienia dostępu do zasobów w katalogu. 
3. Kliknij przycisk **usługi Azure Active Directory** w lewym okienku nawigacji usługi, kliknij przycisk **użytkowników i grup**i upewnij się, że istnieją już konta użytkowników utworzone lub skonfigurowane.
4. W **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji** > **nowej rejestracji aplikacji**. Wprowadź następujące informacje:
    * **Nazwa** -HPCPackClusterServer
    * **Typ aplikacji** — wybierz tę opcję **aplikacji sieci Web / interfejs API**
    * **Adres URL logowania na**— podstawowy adres URL dla przykładu, który jest domyślnie`https://hpcserver`
    * Kliknij przycisk **Utwórz**.
5. Po dodaniu aplikacji, wybierz go w **rejestracji aplikacji** listy. Następnie kliknij przycisk **ustawienia** > **właściwości**. Wprowadź następujące informacje:
    * Wybierz **tak** dla **dzierżawcza Multi**.
    * Zmień **identyfikator URI aplikacji** do `https://<Directory_name>/<application_name>`. Zastąp `<Directory_name`> z pełną nazwę dzierżawy usługi Azure AD, na przykład `hpclocal.onmicrosoft.com`i Zastąp `<application_name>` o nazwie wybrano wcześniej.
6. Kliknij pozycję **Zapisz**. Po zakończeniu zapisywania, na stronie aplikacji kliknij **manifestu**. Edytuj manifest, lokalizując `appRoles` ustawienie i dodawanie następującej roli aplikacji, a następnie kliknij **zapisać**:

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. W **usługi Azure Active Directory**, kliknij przycisk **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**. Wybierz **HPCPackClusterServer** z listy.
8. Kliknij przycisk **właściwości**i zmień **przypisanie użytkownika wymagane** do **tak**. Kliknij pozycję **Zapisz**.
9. Kliknij przycisk **użytkowników i grup** > **Dodaj użytkownika**. Wybierz użytkownika i wybierz rolę, a następnie kliknij przycisk **przypisać**. Przypisać jeden z dostępnych ról (HpcUsers lub HpcAdminMirror) dla użytkownika. Powtórz ten krok z dodatkowym użytkownikom w katalogu. Aby uzyskać ogólne informacje o użytkownikach z klastra, zobacz [Zarządzanie użytkownikami klastra](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Krok 2: Zarejestrować klienta klastra HPC z dzierżawy usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy usługi Azure AD, kliknij konto w prawym górnym rogu. Następnie ustaw żądaną dzierżawy sesji portalu. Musi mieć uprawnienia dostępu do zasobów w katalogu. 
3. W **usługi Azure Active Directory**, kliknij przycisk **rejestracji aplikacji** > **nowej rejestracji aplikacji**. Wprowadź następujące informacje:

    * **Nazwa** -HPCPackClusterClient    
    * **Typ aplikacji** — wybierz tę opcję **macierzystego**
    * **Identyfikator URI przekierowania** - `http://hpcclient`
    * Kliknij przycisk **Utwórz**

4. Po dodaniu aplikacji, wybierz go w **rejestracji aplikacji** listy. Kopiuj **identyfikator aplikacji** wartość i zapisz go. Należy to później podczas konfigurowania aplikacji.

5. Kliknij przycisk **ustawienia** > **wymagane uprawnienia** > **Dodaj** > **wybierz interfejs API**. Wyszukaj i wybierz aplikację HpcPackClusterServer (utworzonego w kroku 1).

6. W **Włącz dostęp** wybierz pozycję **HpcClusterServer dostępu**. Następnie kliknij przycisk **Gotowe**.


## <a name="step-3-configure-the-hpc-cluster"></a>Krok 3: Konfigurowanie klastra HPC

1. Połączenie z węzłem głównym HPC Pack 2016 na platformie Azure.

2. Uruchom program HPC PowerShell.

3. Uruchom następujące polecenie:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    gdzie

    * `AADTenant`Określa nazwę dzierżawy usługi Azure AD, takich jak`hpclocal.onmicrosoft.com`
    * `AADClientAppId`Określa identyfikator aplikacji dla aplikacji utworzony w kroku 2.

4. Wykonaj jedną z następujących czynności, w zależności od konfiguracji węzła głównego:

    * W klastrze HPC Pack pojedynczego węzła głównego Uruchom ponownie usługę HpcScheduler.

    * W klastrze HPC Pack z wielu węzłów głównych uruchom następujące polecenia programu PowerShell w węźle głównym, aby ponownie uruchomić usługę HpcSchedulerStateful:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Krok 4: Zarządzania i przesyłania zadań z klienta

Aby zainstalować narzędzia klienta HPC Pack na komputerze, należy pobrać pliki instalacyjne HPC Pack 2016 (Instalacja pełna) z Microsoft Download Center. Po rozpoczęciu instalacji wybierz opcję instalacji dla **narzędzi klienta HPC Pack**.

Aby przygotować komputer kliencki, należy zainstalować certyfikat używany podczas [Konfiguracja klastra HPC](hpcpack-2016-cluster.md) na komputerze klienckim. Standardowe procedury zarządzania certyfikatów systemu Windows należy zainstalować certyfikat publiczny **Certyfikaty — bieżący użytkownik** > **zaufane główne urzędy certyfikacji** przechowywania. 

Można teraz uruchomić polecenia HPC Pack lub przesyłania zadania i zarządzać nimi klastra przy użyciu konta usługi Azure AD przy użyciu Menedżera klastra HPC Pack zadania graficznego interfejsu użytkownika. Dla opcji przesyłania zadań, zobacz [klastra HPC przesyłania zadań HPC Pack na platformie Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Podczas nawiązywania połączenia z klastrem HPC Pack na platformie Azure po raz pierwszy, zostanie wyświetlone menu podręczne systemu windows. Wprowadź poświadczenia usługi Azure AD, aby się zalogować. Token jest następnie buforowana. Nowsze połączeń z klastrem w usłudze Azure używać pamięci podręcznej tokenu, o ile nie jest brany pod uwagę zmiany lub pamięci podręcznej.
>
  
Na przykład po wykonaniu poprzednich kroków, możesz mogą wykonywać kwerendę o zadań z klienta lokalnego w następujący sposób:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Przydatne polecenia cmdlet umożliwiające przesyłanie zadań o integracji z usługą Azure AD 

### <a name="manage-the-local-token-cache"></a>Zarządzanie lokalnej pamięci podręcznej tokenu

HPC Pack 2016 zapewnia następujące polecenia cmdlet środowiska PowerShell klastra HPC do zarządzania w lokalnej pamięci podręcznej tokenu. Te polecenia cmdlet są przydatne do przesyłania zadań nieinteraktywnie. Zobacz poniższy przykład:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Ustaw poświadczenia do przesyłania zadań przy użyciu konta usługi Azure AD 

Czasami można uruchomić zadania w obszarze użytkownika klastra HPC (w przypadku przyłączonych do domeny HPC klastra Uruchom jako jedną domenę użytkownika; dla klastra HPC przyłączone do domeny, Uruchom jako jeden użytkownik lokalny w węźle głównym).

1. Aby ustawić poświadczenia, użyj następujących poleceń:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Następnie przesłać zadanie w następujący sposób. Zadanie uruchamia w obszarze $localUser w węzłach obliczeniowych.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Jeśli `–Credential` nie zostanie określony z `Submit-HpcJob`, zadania lub zadania uruchamiania lokalnego użytkownika mapowane jako konto usługi Azure AD. (Klaster HPC tworzy użytkownika lokalnego z taką samą nazwę jak konto usługi Azure AD można uruchomić zadania).
    
3. Ustaw zwiększoną ilość danych dla konta usługi Azure AD. Jest to przydatne podczas uruchamiania zadań MPI w węzłach systemu Linux przy użyciu konta usługi Azure AD.

   * Ustaw zwiększoną ilość danych dla konta usługi Azure AD sama

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Ustaw zwiększoną ilość danych, a następnie uruchom jako użytkownika klastra HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

