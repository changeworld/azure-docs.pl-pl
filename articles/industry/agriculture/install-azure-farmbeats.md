---
title: Instalowanie usługi Azure FarmBeats
description: W tym artykule opisano sposób instalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482454"
---
# <a name="install-azure-farmbeats"></a>Instalowanie usługi Azure FarmBeats

W tym artykule opisano sposób instalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure.

Azure FarmBeats to oferta biznesowa dostępna w witrynie Azure Marketplace. Umożliwia ona agregację zestawów danych rolnictwa między dostawcami i generowanie szczegółowych informacji umożliwiających podjęcie odpowiednich działań. Usługa Azure FarmBeats pozwala na tworzenie modeli sztucznej inteligencją (AI) lub uczenia maszynowego (ML) w oparciu o odrzucane zestawy danych. Dwa główne składniki usługi Azure FarmBeats:

- **Datahub**: Warstwa interfejsu API, która umożliwia agregację, normalizację i contextualization różnych zestawów danych w rolnictwie różnych dostawców.

- **Akcelerator**: przykładowa aplikacja internetowa, która jest oparta na Datahub. Przeskoczy — umożliwia rozpoczęcie tworzenia modeli i wizualizacji. Akcelerator używa interfejsów API usługi Azure FarmBeats, aby przedstawić wizualizację pozyskiwanych danych z czujnika jako wykresów i wizualizacji wyników dla modelu jako mapy.

## <a name="before-you-start"></a>Przed rozpoczęciem
### <a name="components-installed"></a>Zainstalowane składniki

Podczas instalowania usługi Azure FarmBeats są obsługiwane następujące zasoby w ramach subskrypcji platformy Azure:

| Zainstalowane zasoby platformy Azure  | Składnik Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Akcelerator & Datahub      |
| App Service     |     Akcelerator & Datahub     |
| Plan usługi App Service   | Akcelerator & Datahub  |
| Połączenie interfejsu API    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Akcelerator & Datahub      |
| Konto usługi Azure Batch    | Datahub   |
| Azure Key Vault |  Akcelerator & Datahub        |
| Konto Azure Maps       |     Skrót    |
| Przestrzeń nazw centrum zdarzeń    |     Datahub      |
| Aplikacja logiki      |  Datahub       |
| Konto magazynu      |     Akcelerator & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Poniesione koszty

Koszt usługi Azure FarmBeats to zagregowany koszt podstawowych usług platformy Azure. Informacje o cenach dla usług platformy Azure można obliczyć przy użyciu [kalkulatora cen](https://azure.microsoft.com/pricing/calculator). Rzeczywisty koszt całkowitej instalacji będzie się różnić w zależności od użycia. Koszt stałego stanu dla dwóch składników:

* Datahub-mniej niż $10 dziennie
* Akcelerator-mniejszy niż $2 dziennie

### <a name="regions-supported"></a>Obsługiwane regiony

Obecnie platforma Azure FarmBeats jest obsługiwana w środowiskach chmury publicznej w następujących regionach:
* Australia Wschodnia
* Środkowe stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Zachodnie stany USA 2
* Europa Północna
* Europa Zachodnia
* Azja Południowo-Wschodnia

### <a name="time-taken"></a>Czas trwania

Cała konfiguracja usługi Azure FarmBeats, łącznie z przygotowaniem i instalacją, zajmie mniej niż godzinę.

## <a name="prerequisites"></a>Wymagania wstępne    

Przed rozpoczęciem rzeczywistej instalacji usługi Azure FarmBeats należy wykonać następujące czynności:

### <a name="create-sentinel-account"></a>Utwórz konto wskaźnikiem
Twoja konfiguracja usługi Azure FarmBeats umożliwia uzyskanie bezpłatnych obrazów satelitarnych z satelitarnej agencji kosmicznej [centrum danych firmy](https://scihub.copernicus.eu/) Microsoft dla farmy. Aby skonfigurować tę konfigurację, wymagane jest konto wskaźnikiem.

Postępuj zgodnie z instrukcjami, aby utworzyć bezpłatne konto przy użyciu kontrolki wskaźnikowej:

1. Przejdź do oficjalnej strony [rejestracji](https://scihub.copernicus.eu/dhus/#/self-registration) .
2. Podaj wymagane szczegóły (imię, nazwisko, nazwę użytkownika, hasło i identyfikator poczty e-mail) i wypełnij formularz.
3. Do zarejestrowanego identyfikatora poczty zostanie wysłane łącze weryfikacyjne. Wybierz link podany w wiadomości e-mail i Ukończ weryfikację.

Proces rejestracji zostanie ukończony po zakończeniu weryfikacji. Zanotuj **nazwę użytkownika** i **hasło**do kontrolki.

### <a name="decide-subscription-and-region"></a>Wybieranie subskrypcji i regionu

Będziesz potrzebować identyfikatora subskrypcji platformy Azure i regionu, w którym chcesz zainstalować usługę Azure FarmBeats. Wybierz jeden z regionów wymienionych w sekcji [Obsługiwane regiony](#regions-supported) .

Zanotuj **Identyfikator subskrypcji platformy Azure** i **region świadczenia usługi Azure**.

### <a name="verify-permissions"></a>Weryfikuj uprawnienia

Musisz sprawdzić, czy masz wystarczające uprawnienia i uprawnienia w dzierżawie platformy Azure, w której chcesz zainstalować usługę Azure FarmBeats.

Możesz sprawdzić uprawnienia dostępu w Azure Portal, postępując zgodnie z instrukcjami dotyczącymi [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Do zainstalowania usługi Azure FarmBeats potrzebne są następujące uprawnienia:
- Dzierżawa — dostęp do odczytu
- Subskrypcja — współautor lub właściciel
- Grupa zasobów — właściciel.

Ponadto usługa Azure FarmBeats wymaga Azure Active Directory rejestracji aplikacji. Istnieją dwa sposoby wykonania wymaganej konfiguracji usługi Azure AD:

**Przypadek 1**: masz uprawnienia do **zapisu** w dzierżawie platformy Azure, do której jest instalowana. W takim przypadku masz uprawnienia niezbędne do dynamicznego tworzenia rejestracji aplikacji usługi AAD podczas instalacji.

Możesz przejść bezpośrednio do sekcji [ukończenie rejestracji w portalu Marketplace](#complete-azure-marketplace-sign-up) .


**Przypadek 2**: nie masz uprawnień do **zapisu** w dzierżawie platformy Azure. Ten przypadek jest typowy w przypadku próby zainstalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure w firmie, a dostęp do **zapisu** jest ograniczony tylko do posiadanej grupy zasobów.
W takim przypadku należy poprosić administratora IT o wykonanie poniższych kroków, aby automatycznie wygenerować i ukończyć rejestrację aplikacji usługi Azure AD w Azure Portal.

1. Pobierz i Wyodrębnij [skrypt generatora aplikacji usługi AAD](https://aka.ms/FarmBeatsAADScript) na komputer lokalny.
2. Zaloguj się do Azure Portal i wybierz subskrypcję i dzierżawę usługi Azure AD.
3. Uruchom Cloud Shell na pasku narzędzi w górnej części Azure Portal.

    ![FarmBeats projektu](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Wybierz program PowerShell jako preferowane środowisko powłoki. Po raz pierwszy użytkownik zostanie poproszony o wybranie subskrypcji i utworzenie konta magazynu. Ukończ instalację zgodnie z instrukcjami.
5. Przekaż skrypt (z kroku 1) do Cloud Shell i zanotuj lokalizację przekazanego pliku.

    > [!NOTE]
    > Domyślnie plik jest przekazywany do katalogu macierzystego.

6. Przejdź do katalogu macierzystego przy użyciu polecenia "CD" i uruchom następujący skrypt:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Wprowadź nazwę **witryny sieci Web Datahub** i nazwę **witryny sieci Web akceleratora** . Zanotuj dane wyjściowe skryptu i udostępnij je osobie instalującej usługę Azure FarmBeats.

Gdy administrator IT poda wymagane szczegóły, zanotuj **Identyfikator klienta usługi AAD, wpis tajny klienta usługi AAD, nazwę witryny sieci Web Datahub & Nazwa witryny internetowej akceleratora**.

   > [!NOTE]
   > Jeśli używasz instrukcji z przypadku 2, nie zapomnij dodać identyfikatora klienta usługi AAD & wpisu tajnego klienta usługi AAD jako oddzielnych parametrów w [pliku parametrów](#prepare-parameters-file)

Teraz masz wszystkie informacje wymagane do przechodzenia do następnej sekcji.

### <a name="complete-azure-marketplace-sign-up"></a>Ukończ rejestrację w portalu Azure Marketplace

Aby móc rozpocząć proces instalacji opartej na chmurze chmurowej, musisz ukończyć rejestrację do oferty Azure FarmBeats w portalu Azure Marketplace. Postępuj zgodnie z poniższymi instrukcjami, aby ukończyć rejestrowanie:

1.  Zaloguj się do Portalu Azure. Wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD, w której chcesz zainstalować usługę Azure FarmBeats.

2.  Przejdź do witryny Azure Marketplace w portalu i Wyszukaj **usługę Azure FarmBeats** w witrynie Marketplace

3.  Zostanie wyświetlone nowe okno z omówieniem usługi Azure FarmBeats. Wybierz pozycję **Utwórz**.

4.  Zostanie wyświetlone nowe okno. Ukończ proces rejestracji, wybierając poprawną subskrypcję, grupę zasobów i lokalizację, w której chcesz zainstalować usługę Azure FarmBeats.

5.  Po sprawdzeniu poprawności wprowadzonych szczegółów wybierz **przycisk OK**. Zostanie wyświetlona strona Warunki użytkowania. Przejrzyj warunki i wybierz pozycję **Utwórz** , aby zakończyć proces rejestracji.

Ten krok umożliwia zakończenie procesu tworzenia konta w witrynie Azure Marketplace. Teraz można przystąpić do rozpoczęcia przygotowywania plików parametrów.

### <a name="prepare-parameters-file"></a>Przygotuj plik parametrów
Ostatnim krokiem w fazie wymagań wstępnych jest utworzenie pliku JSON, który będzie używany jako dane wejściowe podczas instalacji Cloud Shell. Parametry w pliku JSON muszą zostać zastąpione odpowiednimi wartościami.

Poniżej przedstawiono przykładowy plik JSON. Pobierz przykład i zaktualizuj wymagane szczegóły.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Można odwołać się do poniższej tabeli parametrów, aby dowiedzieć się więcej na temat każdego z tych parametrów.

| Parametr | Wartość|
|--- | ---|
|sku  | Umożliwia użytkownikowi wybranie instalacji zarówno Datahub, jak i akceleratora lub Datahub. Aby zainstalować tylko Datahub, użyj "Datahub". Aby zainstalować Datahub i akcelerator, użyj "obu"|
|subscriptionId | Określa subskrypcję platformy Azure do zainstalowania usługi Azure FarmBeats|
|datahubResourceGroup| Określa nazwę grupy zasobów dla zasobów Datahub. Wprowadź nazwę grupy zasobów utworzoną w witrynie Azure Marketplace tutaj|
|location |Region lokalizacji/platformy Azure, w którym chcesz zainstalować usługę Azure FarmBeats|
|datahubWebsiteName  | Unikatowy prefiks adresu URL dla aplikacji sieci Web Datahub |
|acceleratorResourceGroup  | Określa nazwę grupy zasobów dla zasobów akceleratora|
|acceleratorWebsiteName |Unikatowy prefiks adresu URL aplikacji internetowej akceleratora|
|sentinelUsername | Nazwa użytkownika umożliwiająca pobieranie obrazów satelitarnych ze wskaźnikiem|
|notificationEmailAddress  | Adres e-mail, na który będą wysyłane powiadomienia dotyczące alertów skonfigurowanych w ramach usługi Datahub|
|updateIfExists| Obowiązkowe Parametr, który ma zostać uwzględniony w pliku parametrów tylko wtedy, gdy chcesz uaktualnić istniejące wystąpienie usługi Azure FarmBeats. W przypadku uaktualnienia inne szczegóły, takie jak nazwy i lokalizacje grup zasobów, muszą być takie same.|
|aadAppClientId | Obowiązkowe Parametr, który ma zostać uwzględniony w pliku parametrów tylko wtedy, gdy jest używana wstępnie utworzona aplikacja usługi AAD. Aby uzyskać więcej informacji, zobacz przypadek 2 w sekcji [Weryfikowanie uprawnień](#verify-permissions) . |
|aadAppClientSecret  | Obowiązkowe Obowiązkowe Parametr, który ma zostać uwzględniony w pliku parametrów tylko wtedy, gdy jest używana wstępnie utworzona aplikacja usługi AAD. Aby uzyskać więcej informacji, zobacz przypadek 2 w sekcji [Weryfikowanie uprawnień](#verify-permissions) .|

Na podstawie powyższej tabeli i przykładowego pliku JSON Utwórz plik JSON parametrów i Zapisz go na komputerze lokalnym.

## <a name="install"></a>Instalacja

Rzeczywista instalacja zasobów usługi Azure FarmBeats odbywa się w Cloud Shell interfejs wiersza polecenia oparty na przeglądarce przy użyciu środowiska bash. Postępuj zgodnie z poniższymi instrukcjami, aby zainstalować usługę Azure FarmBeats:

1. Zaloguj się do Portalu Azure. Wybierz subskrypcję platformy Azure i dzierżawcę, dla której chcesz zainstalować usługę Azure FarmBeats.
2. Uruchom **Cloud Shell** na pasku narzędzi w prawym górnym rogu Azure Portal.
3. Wybierz bash jako preferowane środowisko powłoki. Wybierz przycisk **Przekaż** (wyróżniony na poniższym obrazie) i przekaż przygotowany plik JSON parametrów.

      ![FarmBeats projektu](./media/install-azure-farmbeats/bash-2-1.png)

4. **Skopiuj** poniższe polecenie i **Zastąp \<nazwę użytkownika >** poprawną wartością, tak aby wskazywała na poprawną ścieżkę przekazanego pliku.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Uruchom zmodyfikowane polecenie, aby rozpocząć proces instalacji. Zostanie wyświetlony monit o:
 - Zaakceptuj postanowienia **licencyjne dotyczące platformy Azure FarmBeats** . Wprowadź wartość "Y", aby przejść do następnego kroku, jeśli akceptujesz Warunki użytkowania. Wprowadź "N", aby zakończyć instalację, jeśli nie akceptujesz warunków użytkowania.

 - Następnie zostanie wyświetlony monit o wprowadzenie tokenu dostępu dla instalacji. Skopiuj wygenerowany kod i zaloguj się na [stronie logowania do urządzenia](https://microsoft.com/devicelogin) przy użyciu **poświadczeń platformy Azure**.

 - Po pomyślnym zakończeniu logowania Instalator wyświetli monit o hasło do konta "wskaźnikiem". Wprowadź **hasło do konta wskaźnikowego**.

6. Plik parametrów zostanie sprawdzony i rozpocznie się instalacja zasobów platformy Azure. Instalacja trwa około **25 minut** .    
> [!NOTE]
> Nieaktywne sesje Cloud Shell wygasną po upływie **20 minut**. Przed wdrożeniem zasobów platformy Azure należy zachować aktywną sesję Cloud Shell. Jeśli sesja przekroczy limit czasu, konieczne będzie ponowne uruchomienie procesu instalacji.

Po zakończeniu instalacji zostaną wyświetlone następujące linki wyjściowe:
* **Adres URL Datahub**: Link Swagger do uzyskiwania dostępu do interfejsów API Datahub.
* **Adres URL akceleratora**: aplikacja sieci Web do eksplorowania akceleratora usługi Azure FarmBeats.
* **Plik dziennika Instalatora**: plik dziennika, który zawiera szczegółowe informacje o instalacji. Ten plik dziennika może służyć do rozwiązywania problemów z instalacją, w razie potrzeby.

Aby sprawdzić zakończenie instalacji usługi Azure FarmBeats, wykonaj następujące czynności:

**Datahub**
1. Zaloguj się do podanego adresu URL akceleratora (w formacie **https://\<yourdatahub-Website-name >. azurewebsites. NET/Swagger**) przy użyciu poświadczeń platformy Azure.
2. Powinny być widoczne różne obiekty interfejsu API FarmBeats i wykonywanie operacji REST w interfejsach API.

**Skrót**
1. Zaloguj się do podanego adresu URL akceleratora (w formacie **https://\<youraccelerator-Website-name >. azurewebsites. NET/Swagger**) przy użyciu poświadczeń platformy Azure.
2. Powinien być widoczny interfejs użytkownika akceleratora z opcją tworzenia Farm w przeglądarce.

Możliwość wykonywania powyższych operacji wskazuje na pomyślne zainstalowanie usługi Azure FarmBeats.

## <a name="upgrade"></a>Uaktualnienie
W publicznej wersji zapoznawczej w celu uaktualnienia istniejącej instalacji usługi Azure FarmBeats należy ponownie uruchomić polecenie instalacji w Cloud Shell z dodatkowym parametrem "**updateIfExists**" w pliku parametrów ustawionym na**wartość "true**". Zapoznaj się z ostatnim wierszem poniższego przykładu JSON dla parametru Update.

```json
{
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```
Polecenie aktualizuje istniejącą instalację usługi Azure FarmBeats do najnowszej wersji i zapewnia linki wyjściowe.

## <a name="uninstall"></a>Dezinstalacja

Aby odinstalować usługę Azure FarmBeats Datahub lub akcelerator, wykonaj następujące czynności:

1.  Zaloguj się do Azure Portal i **Usuń grupy zasobów** , w których są zainstalowane te składniki.

2.  Przejdź do Azure Active Directory & **usunąć aplikacji usługi Azure AD** połączonej z instalacją Azure FarmBeats. Spowoduje to usunięcie instalacji usługi Azure FarmBeats z subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki
Wiesz już, jak zainstalować usługę Azure FarmBeats w ramach subskrypcji platformy Azure. Teraz Dowiedz się, jak [dodać użytkowników](manage-users-in-azure-farmbeats.md#manage-users) do wystąpienia usługi Azure FarmBeats.
