---
title: Zainstaluj projekt Azure FarmBeats
description: W tym artykule opisano sposób instalowania subskrypcji FarmBeats platformy Azure w ramach subskrypcji platformy Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479563"
---
# <a name="install-azure-farmbeats"></a>Zainstaluj projekt Azure FarmBeats

W tym artykule opisano sposób instalowania usługi Azure FarmBeats w ramach subskrypcji platformy Azure.

Azure FarmBeats to oferta biznesowa dostępna w portalu Azure Marketplace. Umożliwia agregację zestawów danych dotyczących rolnictwa między dostawcami i generowanie użytecznych informacji. Azure FarmBeats robi to, umożliwiając tworzenie modeli sztucznej inteligencji (AI) lub uczenia maszynowego (ML) na podstawie połączonych zestawów danych. Dwa główne składniki usługi Azure FarmBeats to:

- **Datahub**: warstwa interfejsu API, która umożliwia agregację, normalizację i kontekstowanie różnych zestawów danych rolnictwa w różnych dostawcach.

- **Akcelerator:** Aplikacja sieci Web, która jest zbudowana na podstawie datahub. To jump-uruchamia rozwój modelu i wizualizacji. Akcelerator używa interfejsów API Azure FarmBeats do zademonstrowania wizualizacji pozyskiwania danych z czujników jako wykresów i wizualizacji danych wyjściowych modelu jako map.

## <a name="general-information"></a>Informacje ogólne

### <a name="components-installed"></a>Zainstalowane komponenty

Po zainstalowaniu usługi Azure FarmBeats następujące zasoby są aprowidzone w ramach subskrypcji platformy Azure:

| Zainstalowane zasoby platformy Azure  | Składnik Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Akcelerator & Datahub      |
| App Service     |     Akcelerator & Datahub     |
| Plan usługi App Service   | Akcelerator & Datahub  |
| Połączenie interfejsu API    |  Datahub (polski)       |
| Azure Cache for Redis       | Datahub (polski)      |
| Azure Cosmos DB   |  Datahub (polski)       |
| Azure Data Factory V2       |     Akcelerator & Datahub      |
| Konto usługi Azure Batch    | Datahub (polski)   |
| W usłudze Azure Key Vault |  Akcelerator & Datahub        |
| Konto usługi Azure Maps       |     Akcelerator    |
| Obszar nazw Centrum zdarzeń    |     Datahub (polski)      |
| Aplikacja logiki      |  Datahub (polski)       |
| Konto magazynu      |     Akcelerator & Datahub      |
| Time Series Insights     |    Datahub (polski)    |

### <a name="costs-incurred"></a>Poniesione koszty

Koszt usługi Azure FarmBeats jest łączny koszt podstawowych usług platformy Azure. Informacje o cenach usług platformy Azure można obliczyć za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator). Rzeczywisty koszt całkowitej instalacji będzie się różnić w zależności od użycia. Koszt stanu ustalonego dla dwóch składników wynosi:

- Datahub - mniej niż $10 dziennie
- Akcelerator - mniej niż $2 dziennie

### <a name="regions-supported"></a>Regiony obsługiwane

Obecnie usługa Azure FarmBeats jest obsługiwana w środowiskach chmury publicznej w następujących regionach:

- Australia Wschodnia
- Środkowe stany USA
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- Europa Zachodnia
- Azja Wschodnia
- Azja Południowa

### <a name="time-taken"></a>Czas przejmuje

Cała konfiguracja usługi Azure FarmBeats, w tym przygotowanie i instalacja zajmie mniej niż godzinę.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem rzeczywistej instalacji usługi Azure FarmBeats należy wykonać następujące kroki:

### <a name="verify-permissions"></a>Weryfikowanie uprawnień

Aby zainstalować usługę Azure FarmBeats, musisz mieć następujące uprawnienia w dzierżawie platformy Azure:

- Dzierżawa — kreator aplikacji AAD
- Subskrypcja - Właściciel
- Grupa zasobów, w której jest instalowana Grupa FarmBeats — Właściciel

Pierwsze dwa uprawnienia są potrzebne do tworzenia kroku [aplikacji usługi AAD.](#create-an-aad-application) W razie potrzeby można uzyskać kogoś z odpowiednimi uprawnieniami do tworzenia aplikacji AAD.

Osoba uruchamiana FarmBeats zainstalować z marketplace musi być właścicielem Grupy zasobów, w którym FarmBeats jest instalowany. W przypadku właścicieli subskrypcji dzieje się to automatycznie podczas tworzenia grupy zasobów. W przypadku innych osób należy wstępnie utworzyć grupę zasobów i poprosić właściciela subskrypcji, aby uczynił cię właścicielem grupy zasobów.

Uprawnienia dostępu można zweryfikować w witrynie Azure portal, postępując zgodnie z instrukcjami [dotyczącymi kontroli dostępu opartej](https://docs.microsoft.com/azure/role-based-access-control/check-access)na rolach.

### <a name="decide-subscription-and-region"></a>Zdecyduj o subskrypcji i regionie

Będziesz potrzebować identyfikatora subskrypcji platformy Azure i regionu, w którym chcesz zainstalować usługę Azure FarmBeats. Wybierz jeden z regionów wymienionych w sekcji [Obsługiwane regiony.](#regions-supported)

Zanotuj **identyfikator subskrypcji platformy Azure** i **region platformy Azure.**

### <a name="create-an-aad-application"></a>Tworzenie aplikacji AAD

Usługi Azure FarmBeats wymagają tworzenia i rejestracji aplikacji usługi Azure Active Directory. Aby pomyślnie uruchomić skrypt tworzenia aad, potrzebne są następujące uprawnienia:

- Dzierżawa — kreator aplikacji AAD
- Subskrypcja - Właściciel

Uruchom następujące kroki w wystąpieniu powłoki chmury przy użyciu środowiska programu PowerShell. Użytkownicy po raz pierwszy zostaną poproszeni o wybranie subskrypcji i utworzenie konta magazynu. Zakończ konfigurację zgodnie z instrukcjami.

1. Pobierz [skrypt generatora aplikacji AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Domyślnie plik jest pobierany do katalogu macierzystego. Przejdź do katalogu.

    ```azurepowershell-interactive
        cd
    ```

3. Uruchamianie skryptu AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skrypt prosi o następujące trzy dane wejściowe:

    - **Nazwa strony farmbeats:** Jest to unikalny prefiks adresu URL dla aplikacji internetowej FarmBeats. W przypadku, gdy prefiks jest już podjęta, skrypt będzie błąd. Po zainstalowaniu wdrożenia FarmBeats będzie dostępny z\<https:// FarmBeats-website-name>.azurewebsites.net i interfejsów API swagger będzie\<na https:// FarmBeats-website-name>-api.azurewebsites.net

    - **Identyfikator logowania platformy Azure:** Podaj identyfikator logowania platformy Azure dla użytkownika, którego chcesz dodać jako administrator FarmBeats. Ten użytkownik może następnie udzielić dostępu do aplikacji sieci web FarmBeats innym użytkownikom. Identyfikator logowania jest zazwyczaj formularza john.doe@domain.com. Usługa Azure UPN jest również obsługiwana.

    - **Identyfikator subskrypcji:** Jest to identyfikator subskrypcji, w której chcesz zainstalować usługę Azure FarmBeats

5. Skrypt AAD trwa około 2 minut, aby uruchomić i wyprowadza wartości na ekranie, a także do pliku json w tym samym katalogu. Jeśli ktoś inny uruchomi skrypt, poproś go o udostępnienie ci tego wyjścia.

### <a name="create-sentinel-account"></a>Utwórz konto Sentinel

Konfiguracja usługi Azure FarmBeats umożliwia pobieranie zdjęć satelitarnych z misji satelitarnej [Sentinel-2](https://scihub.copernicus.eu/) Europejskiej Agencji Kosmicznej dla Twojej farmy. Aby skonfigurować tę konfigurację, wymagane jest konto Sentinel.

Wykonaj kroki, aby utworzyć bezpłatne konto w Sentinel:

1. Przejdź do oficjalnej strony [rejestracji.](https://aka.ms/SentinelRegistration)
2. Podaj wymagane dane (imię, nazwisko, nazwę użytkownika, hasło i identyfikator wiadomości e-mail) i wypełnij formularz.
3. Na zarejestrowany identyfikator e-mail zostanie wysłany link weryfikacyjny. Wybierz link podany w wiadomości e-mail i zakończ weryfikację.

Proces rejestracji został zakończony. Zanotuj **swoją nazwę użytkownika sentinel** i **hasło Sentinel**, po zakończeniu weryfikacji.

## <a name="install"></a>Zainstaluj

Teraz możesz zainstalować FarmBeats. Wykonaj poniższe czynności, aby rozpocząć instalację:

1. Zaloguj się do Portalu Azure. Wybierz swoje konto w prawym górnym rogu i przełącz się do dzierżawy usługi Azure AD, w której chcesz zainstalować usługę Azure FarmBeats.

2. Przejdź do portalu Azure Marketplace i wyszukaj w portalu **usługi Azure FarmBeats** w portalu.

3. Pojawi się nowe okno z omówieniem usługi Azure FarmBeats. Wybierz **pozycję Utwórz**.

4. Zostanie wyświetlone nowe okno. Ukończ proces rejestracji, wybierając poprawną subskrypcję, grupę zasobów i lokalizację, do której chcesz zainstalować usługę Azure FarmBeats.

5. Podaj adres e-mail, który powinien otrzymywać alerty usługi związane z azure farmbeats w **farmbeats alerty usługi** sekcji. **Wybierz pozycję Dalej** u dołu strony, aby przejść do karty **Zależności.**

    ![Karta Podstawy](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Skopiuj poszczególne wpisy z danych wyjściowych [skryptu AAD](#create-an-aad-application) do danych wejściowych w sekcji aplikacji usługi AAD.

7. Wprowadź nazwę użytkownika i hasło [konta Sentinel](#create-sentinel-account) w sekcji Konto Sentinel. Wybierz **pozycję Dalej,** aby przejść do karty **Recenzja + Utwórz.**

    ![Karta Zależności](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Po sprawdzeniu poprawności wprowadzonych danych wybierz przycisk **OK**. Zostanie wyświetlona strona Warunki użytkowania. Przejrzyj warunki i wybierz **pozycję Utwórz,** aby rozpocząć instalację. Zostaniesz przekierowany do strony, na której możesz śledzić postęp instalacji.

Po zakończeniu instalacji można zweryfikować instalację i rozpocząć korzystanie z portalu FarmBeats, przechodząc do\<nazwy witryny sieci Web podanej podczas instalacji: https:// FarmBeats-website-name>.azurewebsites.net. Powinien zostać wyświetlony interfejs użytkownika FarmBeats z opcją tworzenia farm.

**Datahub** można znaleźć na stronie\<https:// FarmBeats-website-name>-api.azurewebsites.net/swagger. W tym miejscu zobaczysz różne FarmBeats obiektów interfejsu API i wykonać operacje REST na interfejsach API.

## <a name="upgrade"></a>Uaktualnienie

Aby uaktualnić FarmBeats do najnowszej wersji, uruchom następujące kroki w wystąpieniu powłoki chmury przy użyciu środowiska programu PowerShell. Użytkownik musi być właścicielem subskrypcji, w której jest zainstalowany FarmBeats.

Użytkownicy po raz pierwszy zostaną poproszeni o wybranie subskrypcji i utworzenie konta magazynu. Zakończ konfigurację zgodnie z instrukcjami.

1. Pobierz [skrypt uaktualnienia](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Domyślnie plik jest pobierany do katalogu macierzystego. Przejdź do katalogu.

    ```azurepowershell-interactive
        cd
    ```

3. Uruchamianie skryptu uaktualnienia

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Ścieżka do pliku input.json jest opcjonalna. Jeśli nie zostanie określony, skrypt poprosi o wszystkie wymagane dane wejściowe. Uaktualnienie powinno zakończyć się w około 30 minut.

## <a name="uninstall"></a>Dezinstalacja

Aby odinstalować usługę Azure FarmBeats Datahub lub Accelerator, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure portal i **usuń grupy zasobów,** w których są zainstalowane te składniki.

2. Przejdź do usługi Azure Active Directory & **usunąć aplikację usługi Azure AD** połączony z instalacją Usługi Azure FarmBeats.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak zainstalować usługę Azure FarmBeats w ramach subskrypcji platformy Azure. Teraz dowiedz się, jak [dodać użytkowników](manage-users-in-azure-farmbeats.md#manage-users) do wystąpienia Usługi Azure FarmBeats.
