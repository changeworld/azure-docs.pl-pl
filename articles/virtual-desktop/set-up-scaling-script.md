---
title: Automatyczne skalowanie hostów sesji pulpitu wirtualnego systemu Windows — Azure
description: Opisuje sposób konfigurowania skryptu automatycznego skalowania dla hostów sesji usług pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 932fbe6814df8ec324dd3360bcacfcbcf1c19b62
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71842766"
---
# <a name="scale-session-hosts-dynamically"></a>Dynamiczne skalowanie hostów sesji

W przypadku wielu wdrożeń pulpitów wirtualnych systemu Windows na platformie Azure koszty maszyny wirtualnej reprezentują znaczną część całkowitego kosztu wdrożenia pulpitu wirtualnego systemu Windows. Aby obniżyć koszty, najlepszym rozwiązaniem jest zamknięcie i cofnięcie alokacji maszyn wirtualnych hosta sesji w godzinach użycia poza szczytem, a następnie ich ponowne uruchomienie w godzinach szczytowego użycia.

W tym artykule jest używany prosty skrypt skalowania do automatycznego skalowania maszyn wirtualnych hosta sesji w środowisku pulpitu wirtualnego systemu Windows. Aby dowiedzieć się więcej o tym, jak działa skrypt skalowania, zobacz sekcję [jak działa skrypt skalowania](#how-the-scaling-script-works) .

## <a name="prerequisites"></a>Wymagania wstępne

Środowisko, w którym uruchamiany jest skrypt, musi mieć następujące elementy:

- Dzierżawa pulpitu wirtualnego systemu Windows i konto lub jednostka usługi z uprawnieniami do wykonywania zapytań dotyczących tej dzierżawy (takich jak współautor usług pulpitu zdalnego).
- Maszyny wirtualne puli hostów sesji skonfigurowane i zarejestrowane w usłudze pulpitu wirtualnego systemu Windows.
- Dodatkowa maszyna wirtualna, która uruchamia zaplanowane zadanie za pośrednictwem Harmonogram zadań i ma dostęp do sieci na hostach sesji. Ta funkcja zostanie określona w dalszej części dokumentu jako maszyny wirtualnej skalowania.
- [Moduł Microsoft Azure Menedżer zasobów PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) zainstalowany na maszynie wirtualnej, na której działa zaplanowane zadanie.
- [Moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) jest zainstalowany na maszynie wirtualnej z uruchomionym zaplanowanym zadaniem.

## <a name="recommendations-and-limitations"></a>Zalecenia i ograniczenia

Podczas uruchamiania skryptu skalowania należy pamiętać o następujących kwestiach:

- Ten skrypt skalowania może obsłużyć tylko jedną pulę hostów na wystąpienie zaplanowanego zadania, w którym działa skrypt skalowania.
- Zaplanowane zadania, które uruchamiają skrypty skalowania, muszą znajdować się na maszynie wirtualnej, która jest zawsze włączona.
- Utwórz oddzielny folder dla każdego wystąpienia skryptu skalowania i jego konfiguracji.
- Ten skrypt nie obsługuje logowania się jako administrator do pulpitu wirtualnego systemu Windows przy użyciu kont użytkowników usługi Azure AD, które wymagają uwierzytelniania wieloskładnikowego. Zalecamy używanie jednostek usługi do uzyskiwania dostępu do usługi pulpitu wirtualnego systemu Windows i platformy Azure. Postępuj zgodnie z [tym samouczkiem](create-service-principal-role-powershell.md) , aby utworzyć jednostkę usługi i przypisanie roli przy użyciu programu PowerShell.
- Gwarancja SLA platformy Azure ma zastosowanie tylko do maszyn wirtualnych w zestawie dostępności. Bieżąca wersja dokumentu zawiera opis środowiska z jedną maszyną wirtualną wykonującą skalowanie, co może nie spełniać wymagań dotyczących dostępności.

## <a name="deploy-the-scaling-script"></a>Wdróż skrypt skalowania

Poniższe procedury zawierają informacje dotyczące sposobu wdrażania skryptu skalowania.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Przygotuj środowisko do skalowania skryptu

Najpierw Przygotuj środowisko do skalowania skryptu:

1. Zaloguj się do maszyny wirtualnej (maszyny wirtualnej skalowania), która uruchomi zaplanowane zadanie przy użyciu konta administratora domeny.
2. Utwórz folder na maszynie wirtualnej skalowania, aby przechowywać skrypt skalowania i jego konfigurację (na przykład **C: \\scaling-HostPool1**).
3. Pobierz pliki **basicScale. ps1**, **config. XML**i **Functions-PSStoredCredentials. ps1** oraz folder **PowershellModules** z [repozytorium skryptów skalowania](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) i skopiuj je do folderu utworzonego w kroku 2. Istnieją dwa podstawowe sposoby uzyskiwania plików przed skopiowaniem ich do maszyny wirtualnej skalowania:
    - Sklonuj repozytorium Git na komputerze lokalnym.
    - Wyświetl **nieprzetworzoną** wersję każdego pliku, skopiuj i wklej zawartość każdego pliku do edytora tekstów, a następnie Zapisz pliki z odpowiadającą jej nazwą i typem pliku. 

### <a name="create-securely-stored-credentials"></a>Twórz bezpiecznie przechowywane poświadczenia

Następnie musisz utworzyć bezpieczne przechowywane poświadczenia:

1. Otwórz program PowerShell ISE jako administrator.
2. Zaimportuj moduł programu PowerShell dla usług pulpitu zdalnego, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Otwórz okienko Edycja i załaduj plik **Function-PSStoredCredentials. ps1** , a następnie uruchom cały skrypt (F5).
4. Uruchom następujące polecenie cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Na przykład **@no__t: Set-Variable-Name Path-Scope Global-Value**
5. Uruchom polecenie cmdlet **New-StoredCredential-path \$KeyPath** . Po wyświetleniu monitu wprowadź poświadczenia pulpitu wirtualnego systemu Windows z uprawnieniami do wysyłania zapytań do puli hostów (Pula hostów jest określona w **pliku config. XML**).
    - W przypadku korzystania z różnych jednostek usługi lub konta standardowego Uruchom polecenie cmdlet **New-StoredCredential \$KeyPath** dla każdego konta, aby utworzyć lokalne poświadczenia przechowywane.
6. Uruchom **Get-StoredCredential-list** , aby potwierdzić, że poświadczenia zostały pomyślnie utworzone.

### <a name="configure-the-configxml-file"></a>Konfiguruj plik config. XML

Wprowadź odpowiednie wartości w poniższych polach, aby zaktualizować ustawienia skalowania skryptu w pliku config. XML:

| Pole                     | Opis                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Identyfikator dzierżawy usługi Azure AD, który kojarzy subskrypcję, w której działają maszyny wirtualne hosta sesji     |
| AADApplicationId              | Identyfikator aplikacji głównej usługi                                                       |
| AADServicePrincipalSecret     | Ta wartość może zostać wprowadzona w fazie testowania, ale ma być pusta po utworzeniu poświadczeń za pomocą **Functions-PSStoredCredentials. ps1**    |
| currentAzureSubscriptionId    | Identyfikator subskrypcji platformy Azure, w której działają maszyny wirtualne hosta sesji                        |
| tenantName                    | Nazwa dzierżawy pulpitu wirtualnego systemu Windows                                                    |
| hostPoolName                  | Nazwa puli hostów usług pulpitu wirtualnego systemu Windows                                                 |
| RDBroker                      | Adres URL usługi WVD, wartość domyślna to https: \//rdbroker. WVD. Microsoft. com             |
| Nazwa użytkownika                      | Identyfikator aplikacji głównej usługi (może mieć taką samą nazwę główną usługi jak AADApplicationId) lub użytkownika standardowego bez uwierzytelniania wieloskładnikowego |
| isServicePrincipal            | Akceptowane wartości to **true** i **false**. Wskazuje, czy drugi używany zestaw poświadczeń jest jednostką usługi lub kontem standardowym. |
| BeginPeakTime                 | Gdy rozpoczyna się czas szczytowego użycia                                                            |
| EndPeakTime                   | Po zakończeniu szczytowego czasu użycia                                                              |
| TimeDifferenceInHours         | Różnica czasu między czasem lokalnym i czasem UTC (w godzinach)                                   |
| SessionThresholdPerCPU        | Maksymalna liczba sesji na próg procesora CPU używana do określania, kiedy należy uruchomić nową maszynę wirtualną hosta sesji w godzinach szczytu.  |
| MinimumNumberOfRDSH           | Minimalna liczba maszyn wirtualnych puli hostów, które mają być uruchomione w czasie poza szczytem użycia             |
| LimitSecondsToForceLogOffUser | Liczba sekund oczekiwania przed wymuszeniem wylogowania użytkowników. W przypadku ustawienia wartości 0 użytkownicy nie będą zmuszeni do wylogowania.  |
| LogOffMessageTitle            | Tytuł wiadomości wysyłanej do użytkownika przed wymuszeniem wylogowania                  |
| LogOffMessageBody             | Treść komunikatu ostrzegawczego wysłanego do użytkowników przed ich wylogowaniem. Na przykład "ten komputer zostanie zamknięty w ciągu X minut. Zapisz swoją służbę i wyloguj się ". |

### <a name="configure-the-task-scheduler"></a>Skonfiguruj Harmonogram zadań

Po skonfigurowaniu pliku Configuration. XML należy skonfigurować Harmonogram zadań do uruchamiania pliku basicScaler. ps1 w regularnych odstępach czasu.

1. Rozpocznij **harmonogram zadań**.
2. W oknie **harmonogram zadań** wybierz pozycję **Utwórz zadanie...**
3. W oknie dialogowym **Tworzenie zadania** wybierz kartę **Ogólne** , wprowadź **nazwę** (na przykład "dynamiczny Host hosta"), wybierz pozycję Uruchom, **niezależnie od tego, czy użytkownik jest zalogowany** , czy nie, i **Uruchom z najwyższymi uprawnieniami**.
4. Przejdź do karty **wyzwalacze** , a następnie wybierz pozycję **Nowy...**
5. W oknie dialogowym **Nowy wyzwalacz** w obszarze **Ustawienia zaawansowane**zaznacz opcję **Powtarzaj zadanie co** i wybierz odpowiedni okres i czas trwania (na przykład **15 minut** lub **nieskończoność**).
6. Wybierz kartę **Akcje** i **Nowy...**
7. W oknie dialogowym **Nowa akcja** wpisz **PowerShell. exe** w polu **Program/skrypt** , a następnie wprowadź **C: \\Scaling @ no__t-5basicScale. ps1** w polu **Dodaj argumenty (opcjonalnie)** .
8. Przejdź do karty **warunki** i **Ustawienia** , a następnie wybierz **przycisk OK** , aby zaakceptować ustawienia domyślne dla każdego z nich.
9. Wprowadź hasło dla konta administracyjnego, w którym planujesz uruchomić skrypt skalowania.

## <a name="how-the-scaling-script-works"></a>Jak działa skrypt skalowania

Ten skrypt skalowania odczytuje ustawienia z pliku config. XML, włącznie z początkiem i końcem okresu szczytowego użycia w ciągu dnia.

W czasie szczytowego użycia skrypt sprawdza bieżącą liczbę sesji i bieżącą uruchomioną wydajność hosta usług pulpitu zdalnego dla każdej puli hostów. Obliczana jest, czy uruchomione maszyny wirtualne hosta sesji mają wystarczającą pojemność do obsługi istniejących sesji na podstawie parametru SessionThresholdPerCPU zdefiniowanego w pliku config. XML. W przeciwnym razie skrypt uruchamia dodatkowe maszyny wirtualne hosta sesji w puli hostów.

W czasie użycia poza szczytem skrypt określa, które maszyny wirtualne hosta sesji powinny zostać zamknięte na podstawie parametru MinimumNumberOfRDSH w pliku config. XML. Skrypt skonfiguruje maszyny wirtualne hosta sesji do trybu opróżniania, aby zapobiec nawiązywaniu połączeń z hostami przez nowe sesje. Jeśli ustawisz parametr **LimitSecondsToForceLogOffUser** w pliku config. XML na wartość różną od zera, skrypt wyśle wszystkie aktualnie zalogowanych użytkowników w celu zapisania pracy, zaczekaj na skonfigurowanie czasu, a następnie wymusić wylogowanie się użytkowników. Po wylogowaniu wszystkich sesji użytkownika na maszynie wirtualnej hosta sesji skrypt zostanie zamknięty.

Jeśli parametr **LimitSecondsToForceLogOffUser** w pliku config. XML zostanie ustawiony na wartość zero, skrypt zezwoli na ustawienie konfiguracja sesji we właściwościach puli hostów do obsługi wylogowywania sesji użytkowników. Jeśli na maszynie wirtualnej hosta sesji znajdują się jakieś sesje, spowoduje to pozostawienie uruchomionej maszyny wirtualnej hosta sesji. Jeśli nie ma żadnych sesji, skrypt wyłączy maszynę wirtualną hosta sesji.

Skrypt jest przeznaczony do okresowego uruchamiania na serwerze maszyn wirtualnych skalowania przy użyciu Harmonogram zadań. Wybierz odpowiedni przedział czasu na podstawie rozmiaru środowiska Usługi pulpitu zdalnego i pamiętaj, że uruchamianie i zamykanie maszyn wirtualnych może zająć trochę czasu. Zalecamy uruchamianie skryptu skalowania co 15 minut.

## <a name="log-files"></a>Pliki dziennika

Skrypt skalowania tworzy dwa pliki dziennika, **WVDTenantScale. log** i **WVDTenantUsage. log**. Plik **WVDTenantScale. log** będzie rejestrował zdarzenia i błędy (jeśli istnieją) podczas każdego wykonywania skryptu skalowania.

Plik **WVDTenantUsage. log** będzie rejestrował aktywną liczbę rdzeni i aktywną liczbę maszyn wirtualnych przy każdym uruchomieniu skryptu skalowania. Korzystając z tych informacji, można oszacować rzeczywiste użycie Microsoft Azure maszyn wirtualnych i kosztu. Plik jest sformatowany jako wartości rozdzielane przecinkami, z każdym elementem zawierającym następujące informacje:

>czas, Pula hostów, rdzenie, maszyny wirtualne

Nazwę pliku można także zmodyfikować tak, aby zawierała rozszerzenie CSV, które zostało załadowane do programu Microsoft Excel i przeanalizowane.
