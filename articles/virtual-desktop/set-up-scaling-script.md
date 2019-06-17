---
title: Automatyczne skalowanie hosty sesji Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: W tym artykule opisano, jak skonfigurować automatyczne skalowanie skryptu dla hostów sesji Windows wirtualnego Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: e9f500e3ab965b9dbfc5e395a6572497c85f6f8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755134"
---
# <a name="automatically-scale-session-hosts"></a>Automatyczne skalowanie hostów sesji

W przypadku wielu wdrożeń Windows wirtualnego Desktop w wersji zapoznawczej na platformie Azure koszty maszyny wirtualnej reprezentują znaczną część całkowitych kosztów wdrożenia pulpitu wirtualnego Windows. Aby zmniejszyć koszty, najlepiej jest zamknięta i Cofnij Przydział maszyny wirtualne hosta sesji (VM) poza godzinami szczytu użycia, a następnie uruchom je ponownie w godzinach szczytu niska użycia.

W tym artykule używa prosty skrypt skalowania automatycznego skalowania maszyn wirtualnych na hoście sesji w środowisku Windows pulpitu wirtualnego. Aby dowiedzieć się więcej na temat sposobu działania skryptu skalowania, zobacz [sposób działania skryptu skalowania](#how-the-scaling-script-works) sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

Środowisko, w której uruchomiono skrypt musi mieć następujące elementy:

- Dzierżawy pulpitu wirtualnego Windows i konta lub jednostki usługi przy użyciu uprawnień do wykonywania zapytań w tej dzierżawie (np. Współautor usług pulpitu zdalnego).
- Sesja hosta puli maszyn wirtualnych skonfigurowane i zarejestrowane w usłudze Windows pulpitu wirtualnego.
- Dodatkowe maszyny wirtualnej, która uruchamia zaplanowane zadanie za pomocą harmonogramu zadań i ma dostęp do sieci na hostach sesji. To będzie można nazywane w dalszej części dokumentu programu scaler maszyny Wirtualnej.
- [Modułu programu PowerShell usługi Microsoft Azure Resource Manager](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) zainstalowane na maszynie Wirtualnej, uruchamiając zaplanowane zadanie.
- [Modułu programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) zainstalowane na maszynie Wirtualnej, uruchamiając zaplanowane zadanie.

## <a name="recommendations-and-limitations"></a>Zalecenia i ograniczenia

Po uruchomieniu skryptu skalowania pamiętać o następujących czynności na uwadze:

- Ten skrypt skalowania może obsługiwać tylko jedną pulę hosta dla każdego wystąpienia zaplanowane zadanie, które jest uruchomienie skryptu skalowania.
- Zaplanowane zadania, które są uruchamiane skrypty skalowania musi być na maszynie Wirtualnej, który jest zawsze włączona.
- Utworzenie osobnego folderu dla każdego wystąpienia elementu skalowania skryptu i jego konfiguracji.
- Ten skrypt nie obsługuje logowania się jako administrator do Windows pulpitu wirtualnego przy użyciu konta użytkownika usługi Azure AD, które wymagają uwierzytelniania wieloskładnikowego. Zalecane jest użycie jednostki usługi dostęp do usług pulpitu wirtualnego Windows i platformy Azure. Postępuj zgodnie z [w tym samouczku](create-service-principal-role-powershell.md) do tworzenia nazwy głównej usługi i przypisanie roli przy użyciu programu PowerShell.
- Żadnej gwarancji umowy SLA platformy Azure dotyczą tylko maszyn wirtualnych w zestawie dostępności. Bieżącą wersję dokumentu opisano środowisko z jedną maszynę Wirtualną podczas skalowania, która może nie spełniać wymagań dotyczących dostępności.

## <a name="deploy-the-scaling-script"></a>Wdróż skrypt skalowania

Poniższych procedur temu wiadomo, jak wdrożyć skrypt skalowania.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Przygotowywanie środowiska dla skalowania skryptu

Najpierw należy przygotować środowisko na potrzeby skalowania skryptu:

1. Zaloguj się do maszyny Wirtualnej (maszyny Wirtualnej programu scaler) uruchamianego zaplanowanego zadania przy użyciu konta administracyjnego domeny.
2. Utwórz folder na scaler maszyny Wirtualnej do przechowywania skryptu skalowania i jego konfiguracji (na przykład **C:\\skalowanie HostPool1**).
3. Pobierz **basicScale.ps1**, **pliku Config.xml**, i **PSStoredCredentials.ps1 funkcje** plików, a **PowershellModules** folder z [skalowanie repozytorium skryptów](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) i skopiować je do folderu utworzonego w kroku 2. Istnieją dwa podstawowe sposoby uzyskania plików przed skopiowaniem ich do programu scaler maszyny Wirtualnej:
    - Sklonuj repozytorium git na komputerze lokalnym.
    - Widok **Raw** wersja każdego pliku skopiuj i Wklej zawartość każdego pliku do edytora tekstów, a następnie zapisz pliki z nazwą pliku i typu pliku. 

### <a name="create-securely-stored-credentials"></a>Utwórz bezpieczne przechowywanych poświadczeń

Następnie należy utworzyć bezpiecznie przechowywane poświadczenia:

1. Otwórz program PowerShell ISE z uprawnieniami administratora.
2. Zaimportuj moduł programu PowerShell usług pulpitu zdalnego, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Otwieranie okienka edycji i obciążenia **PSStoredCredentials.ps1 funkcja** pliku.
4. Uruchom następujące polecenie cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Na przykład **Set-Variable - KeyPath nazwa-zakresu globalnego — wartość "c:\\skalowanie HostPool1"**
5. Uruchom **New StoredCredential - KeyPath \$KeyPath** polecenia cmdlet. Po wyświetleniu monitu wprowadź swoje poświadczenia pulpitu wirtualnego Windows z uprawnieniami do wykonywania zapytań w puli hosta (puli hosta jest określona w **pliku config.xml**).
    - Jeśli używasz standardowego konta lub jednostek innej usługi, uruchom **New StoredCredential - KeyPath \$KeyPath** polecenia cmdlet, gdy dla każdego konta utworzyć lokalne przechowywane poświadczenia.
6. Uruchom **Get StoredCredentials-lista** aby upewnić się, poświadczenia zostały pomyślnie utworzone.

### <a name="configure-the-configxml-file"></a>Konfigurowanie pliku config.xml

Wprowadź odpowiednie wartości do następujących pól, aby zaktualizować ustawienia skalowania skryptu w pliku config.xml:

| Pole                     | Opis                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Identyfikator dzierżawy usługi AD platformy Azure, które kojarzy subskrypcji, w którym są uruchamiane maszyny wirtualne hosta sesji     |
| AADApplicationId              | Identyfikator aplikacji nazwy głównej usługi                                                       |
| AADServicePrincipalSecret     | To może zostać podane podczas fazy testowania, ale musi pozostać pusty, po utworzeniu poświadczenia za pomocą **PSStoredCredentials.ps1 funkcji**    |
| currentAzureSubscriptionId    | Identyfikator subskrypcji platformy Azure, w którym są uruchamiane maszyny wirtualne hosta sesji                        |
| tenantName                    | Nazwa dzierżawy Windows pulpitu wirtualnego                                                    |
| hostPoolName                  | Nazwa puli hosta Windows pulpitu wirtualnego                                                 |
| RDBroker                      | Adres URL usługi WVD, domyślna wartość https:\//rdbroker.wvd.microsoft.com             |
| Nazwa użytkownika                      | Identyfikator aplikacji nazwy głównej usługi (użytkownik może mieć tej samej nazwy głównej usługi jako AADApplicationId) lub użytkownik standardowy pominięciem usługi Multi-Factor authentication |
| isServicePrincipal            | Akceptowane wartości to **true** lub **false**. Wskazuje, czy drugi zestaw poświadczeń, używane jest jednostki usługi lub konto standardowe. |
| BeginPeakTime                 | Kiedy rozpoczyna się godziny użycia szczytu                                                            |
| EndPeakTime                   | Kiedy kończy się godziny użycia szczytu                                                              |
| TimeDifferenceInHours         | Różnica czasu między czasem lokalnym i UTC, w godzinach                                   |
| SessionThresholdPerCPU        | Maksymalna liczba sesji na progu procesora CPU, używany do określenia, kiedy nowa sesja hosta maszyny Wirtualnej musi być uruchomiona w godzinach szczytu.  |
| MinimumNumberOfRDSH           | Minimalna liczba hostów puli maszyn wirtualnych, aby działały w czasie poza godzinami użycia             |
| LimitSecondsToForceLogOffUser | Liczba sekund oczekiwania przed wymuszeniem użytkowników, aby się wylogować. Jeśli ustawiona na 0, użytkownicy nie są wymuszone się wylogować.  |
| LogOffMessageTitle            | Tytuł wiadomości wysyłane do użytkownika przed ich musieli Wyloguj                  |
| LogOffMessageBody             | Treść komunikat ostrzegawczy wysyłanych do użytkowników, zanim one wylogowany. Na przykład "ten komputer będzie wyłączone w dół w X minut. Zapisz swoją pracę i Wyloguj." |

### <a name="configure-the-task-scheduler"></a>Skonfiguruj harmonogram zadań

Po skonfigurowaniu pliku XML konfiguracji, należy skonfigurować harmonogram zadań do uruchomienia pliku basicScaler.ps1 w regularnych odstępach czasu.

1. Rozpocznij **harmonogram zadań**.
2. W **harmonogram zadań** wybierz **Tworzenie zadania...**
3. W **Utwórz zadanie** okno dialogowe, wybierz opcję **ogólne** wprowadź **nazwa** (na przykład "RDSH dynamiczna"), wybierz **uruchamiania, czy użytkownik jest zalogowany** i **Uruchom z najwyższymi uprawnieniami**.
4. Przejdź do **wyzwalaczy** kartę, a następnie wybierz **nowy...**
5. W **nowy wyzwalacz** okna dialogowego, w obszarze **Zaawansowane ustawienia**, sprawdź **Powtarzaj zadanie co** i wybrać odpowiedni okres i czas trwania (na przykład **15 minut** lub **przez czas nieokreślony**).
6. Wybierz **akcje** kartę i **nowy...**
7. W **nowa akcja** okno dialogowe, wprowadź **powershell.exe** do **programu/skryptu** pola, a następnie wprowadź **C:\\skalowanie\\ RDSScaler.ps1** do **Dodaj argumenty (opcjonalne)** pola.
8. Przejdź do **warunki** i **ustawienia** karty, a następnie wybierz pozycję **OK** aby zaakceptować ustawienia domyślne dla każdego.
9. Wprowadź hasło dla konta administracyjnego, w którym zamierzasz uruchomić skrypt skalowania.

## <a name="how-the-scaling-script-works"></a>Sposób działania skryptu skalowania

Ten skrypt skalowania odczytuje ustawienia z pliku config.xml, w tym początek i koniec okresu szczytowe użycie w ciągu dnia.

W czasie szczytowego użycia skrypt sprawdza bieżącą liczbę sesji i bieżącą pojemność RDSH uruchomione dla każdej puli hosta. Jeśli uruchomione maszyny wirtualne hosta sesji mieć możliwości wystarczające do obsługi istniejących sesji na podstawie parametru SessionThresholdPerCPU zdefiniowane w pliku config.xml jest obliczana. Jeśli nie, skrypt jest uruchamiany host sesji dodatkowe maszyny wirtualne w puli hosta.

W czasie poza godzinami użycia skryptu określa, które hostów sesji maszyn wirtualnych należy wyłączyć na podstawie parametru MinimumNumberOfRDSH w pliku config.xml. Skrypt ustawi sesji hosta maszyn wirtualnych, aby opróżnić trybie, aby uniemożliwić nowych sesji nawiązywania połączenia z hostów. Jeśli ustawisz **LimitSecondsToForceLogOffUser** parametru w pliku config.xml, aby wartość dodatnią różna od zera, skrypt powiadomi żadnego obecnie zalogowany użytkowników, aby zapisać pracę, poczekaj na skonfigurowanym czasie, a następnie wymusić Użytkownicy, aby się wylogować. Gdy wszystkie sesje użytkowników zostały podpisane na hoście sesji maszyny Wirtualnej, skrypt spowoduje wyłączenie serwera.

Jeśli ustawisz **LimitSecondsToForceLogOffUser** parametru w pliku config.xml zero, skrypt ten umożliwia ustawienie konfiguracji sesji na hoście właściwości puli do obsługi podpisywania off sesji użytkownika. W przypadku innych sesji na hoście sesji maszyny Wirtualnej, spowoduje to zamknięcie maszyny Wirtualnej hosta sesji uruchomione. Jeśli nie ma żadnych sesji, skrypt spowoduje wyłączenie maszyny Wirtualnej hosta sesji.

Skrypt jest przeznaczony do działania okresowo, na serwerze maszyny Wirtualnej programu scaler przy użyciu harmonogramu zadań. Wybierz przedział czasu odpowiednie, w oparciu o rozmiar środowiska usług pulpitu zdalnego i należy pamiętać, że uruchamianie i zamykanie maszyn wirtualnych może zająć trochę czasu. Zaleca się uruchomienie skryptu skalowania co 15 minut.

## <a name="log-files"></a>Pliki dziennika

Skalowanie skrypt tworzy dwa pliki dziennika **WVDTenantScale.log** i **WVDTenantUsage.log**. **WVDTenantScale.log** pliku będą rejestrowane zdarzenia i błędy (jeśli istnieją) podczas każdego wykonania skryptu skalowania.

**WVDTenantUsage.log** rejestrowania active liczby rdzeni i liczbę aktywnych maszyn wirtualnych każdym wykonaniu skryptu skalowania. Można użyć tych informacji do oszacowania rzeczywistego użycia maszyn wirtualnych platformy Azure firmy Microsoft i koszt. Plik jest w formacie wartości rozdzielanych przecinkami, z każdym elementem zawierający następujące informacje:

>czas, puli hosta, liczbę rdzeni maszyn wirtualnych

Nazwa pliku można zmodyfikować w taki sposób, aby mieć rozszerzenie CSV załadowane do programu Microsoft Excel i analizowane.
