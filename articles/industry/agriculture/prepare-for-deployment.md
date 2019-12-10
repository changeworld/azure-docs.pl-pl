---
title: Wdrażanie usługi Azure FarmBeats
description: W tym artykule opisano sposób wdrażania usługi Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941580"
---
# <a name="deploy-azure-farmbeats"></a>Wdrażanie usługi Azure FarmBeats

W tym artykule opisano sposób konfigurowania usługi Azure FarmBeats.

Usługa Azure FarmBeats to charakterystyczne dla branży rozwiązanie dla rolnictwa opartego na danych, które umożliwia bezproblemową obsługę administracyjną i łączność z urządzeniem czujników do chmury platformy Azure, zbierania danych telemetrii i agregacji. Usługa Azure FarmBeats obejmuje różne czujniki, takie jak aparaty fotograficzne, dronom i czujniki glebowe. Za pomocą usługi Azure FarmBeats można zarządzać urządzeniami z chmury, w tym zarządzania infrastrukturą i usługami na platformie Azure dla urządzeń gotowych do IoT oraz przy użyciu rozszerzalnej aplikacji sieci Web i mobilnej w celu zapewnienia wizualizacji, alertów i szczegółowych informacji.

> [!NOTE]
> Usługa Azure FarmBeats jest obsługiwana tylko w środowiskach chmury publicznej. Aby uzyskać więcej informacji, zobacz [co to jest chmura publiczna?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Usługa Azure FarmBeats ma dwa następujące składniki:

- **Datahub**: warstwa platformy usługi Azure FarmBeats, która umożliwia tworzenie, przechowywanie, przetwarzanie danych i uzyskiwanie szczegółowych informacji z istniejących lub nowych potoków danych. Ta warstwa platformy jest przydatna do uruchamiania i kompilowania potoków danych i modeli w rolnictwie.

- **Akcelerator**: warstwa rozwiązania platformy Azure FarmBeats, która ma wbudowaną aplikację do zilustrowania możliwości platformy Azure FarmBeats przy użyciu wstępnie utworzonych modeli rolnictwa. Ta warstwa rozwiązania umożliwia tworzenie granic farmy i uzyskiwanie szczegółowych informacji z danych rolnictwa w kontekście granicy farmy.

Szybkie wdrożenie usługi Azure FarmBeats powinno trwać krócej niż godzinę. Koszty Datahub i akceleratora różnią się w zależności od użycia.

## <a name="deployed-resources"></a>Wdrożone zasoby

Wdrożenie usługi Azure FarmBeats tworzy następujące zasoby w ramach subskrypcji:

| Kolejną. nr  | Nazwa zasobu  | Składnik FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/akcelerator     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/akcelerator        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Przestrzeń nazw centrum zdarzeń platformy Azure    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/akcelerator      |
|8  |Konto usługi Batch    |Datahub   |
|9  |       Konto magazynu     |  Datahub/akcelerator        |
|10  |    Aplikacja logiki        |     Datahub      |
|11  |    Połączenie interfejsu API        |     Datahub      |
|12|      Azure App Service      |  Datahub/akcelerator       |
|13 |    Plan usługi App Service        |     Datahub/akcelerator      |
|14 |Konto Azure Maps     |Skrót    |

Usługa Azure FarmBeats jest dostępna do pobrania w witrynie Azure Marketplace, do której można uzyskać dostęp bezpośrednio z Azure Portal.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Tworzenie oferty platformy Azure FarmBeats w portalu Azure Marketplace

Aby utworzyć ofertę Azure FarmBeats w portalu Azure Marketplace, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal, wybierz swoje konto w prawym górnym rogu, a następnie przejdź do dzierżawy usługi Azure Active Directory (Azure AD), w której chcesz wdrożyć usługę Azure FarmBeats.
1. Usługa Azure FarmBeats jest dostępna w witrynie Azure Marketplace. Na stronie **Azure Marketplace** wybierz pozycję **Pobierz teraz**.
1. Wybierz pozycję **Utwórz**.

> [!NOTE]
> Ukończenie oferty w portalu Azure Marketplace jest częścią instalacji. Aby ukończyć wdrażanie usługi Azure FarmBeats w ramach subskrypcji platformy Azure, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach.

## <a name="prepare"></a>Przygotowanie

Aby można było wdrożyć usługę Azure FarmBeats, potrzebne są następujące uprawnienia:

- **Dzierżawca**: dostęp do odczytu
- **Subskrypcja**: współautor lub właściciel
- **Grupa zasobów**: właściciel

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem wdrażania należy się upewnić, że są spełnione następujące wymagania wstępne:

- Konto wskaźnikiem
- Rejestracja aplikacji usługi Azure AD

## <a name="create-a-sentinel-account"></a>Tworzenie konta wskaźnikowego    

Konto z wskaźnikiem kontrolnym pomaga w pobieraniu obrazów satelitarnych ze wskaźnikami z oficjalnej witryny sieci Web do urządzenia. Aby utworzyć bezpłatne konto, wykonaj następujące czynności:

1. Przejdź do [strony rejestracji konta wskaźnikowego](https://scihub.copernicus.eu/dhus/#/self-registration).
1. W formularzu rejestracji podaj swoje imię i nazwisko, nazwisko, nazwę użytkownika, hasło i adres e-mail.

Do potwierdzenia zostanie wysłana wiadomość e-mail z weryfikacją. Wybierz link, aby potwierdzić swój adres e-mail. Proces rejestracji został ukończony.

## <a name="create-an-azure-ad-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure AD

Do uwierzytelniania i autoryzacji w usłudze Azure FarmBeats należy mieć rejestrację aplikacji usługi Azure AD. Można go utworzyć na dwa sposoby:

* **Opcja 1**. Instalator może automatycznie utworzyć rejestrację, pod warunkiem, że masz wymagane uprawnienia dzierżawy, subskrypcji i grupy zasobów. Jeśli te uprawnienia są stosowane, przejdź do sekcji ["przygotowanie pliku Input. JSON"](#prepare-the-inputjson-file) .

* **Opcja 2**: możesz utworzyć i skonfigurować rejestrację ręcznie przed wdrożeniem usługi Azure FarmBeats. Ta metoda jest zalecana, gdy nie masz wymaganych uprawnień do tworzenia i konfigurowania rejestracji aplikacji usługi Azure AD w ramach subskrypcji. Poproszenie administratora o użycie [niestandardowego skryptu](https://aka.ms/FarmBeatsAADScript), który pomoże administratorowi IT automatycznie wygenerować i skonfigurować rejestrację aplikacji usługi Azure AD na Azure Portal. Jako dane wyjściowe do uruchamiania tego skryptu niestandardowego przy użyciu środowiska PowerShell administrator IT musi udostępnić Ci identyfikator klienta aplikacji usługi Azure AD i hasło. Zanotuj te wartości.

    Aby uruchomić skrypt rejestracji aplikacji usługi Azure AD, wykonaj następujące czynności:

    1. Pobierz [skrypt](https://aka.ms/FarmBeatsAADScript).
    1. Zaloguj się do Azure Portal i wybierz subskrypcję i dzierżawę usługi Azure AD.
    1. Uruchom Cloud Shell na pasku narzędzi w górnej części Azure Portal.

        ![FarmBeats projektu](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Jeśli jesteś użytkownikiem po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udostępnienia plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn**.
    1. Użytkownicy korzystający z pierwszego czasu są również w wyborze środowiska powłoki, bash lub PowerShell. Wybierz pozycję PowerShell.
    1. Przekaż skrypt (z kroku 1) do Cloud Shell i zanotuj lokalizację przekazanego pliku.

        > [!NOTE]
        > Domyślnie plik jest przekazywany do katalogu macierzystego.

        Użyj następującego skryptu:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Zanotuj identyfikator aplikacji usługi Azure AD i klucz tajny klienta, aby udostępnić go osobie, która wdraża usługę Azure FarmBeats.

### <a name="prepare-the-inputjson-file"></a>Przygotuj plik INPUT. JSON

W ramach instalacji Utwórz plik *Input. JSON* , jak pokazano poniżej:

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

Ten plik jest plikiem wejściowym do Azure Cloud Shell. Zawiera parametry, których wartości są używane podczas instalacji. Wszystkie parametry w pliku JSON muszą zostać zastąpione odpowiednimi wartościami lub usunięte. Po ich usunięciu Instalator wyświetli monit z pytaniem o instalację.

Przed przystąpieniem do przygotowywania pliku Przejrzyj parametry w poniższej tabeli:

|Polecenie | Opis|
|--- | ---|
|sku  | Zapewnia możliwość pobrania jednego lub obu składników usługi Azure FarmBeats. Określa składniki do pobrania. Aby zainstalować tylko Datahub, użyj "onlydatabhub". Aby zainstalować Datahub i akcelerator, użyj "obu".|
|subscriptionId | Określa subskrypcję do zainstalowania usługi Azure FarmBeats.|
|datahubResourceGroup| Nazwa grupy zasobów dla zasobów Datahub.|
|location |Lokalizacja, w której chcesz utworzyć zasoby.|
|acceleratorWebsiteName |Unikatowy prefiks adresu URL, aby nawiązać nazwę wystąpienia Datahub.|
|acceleratorResourceGroup  | Unikatowy prefiks adresu URL, aby nawiązać nazwę witryny sieci Web akceleratora.|
|datahubWebsiteName  | Unikatowy prefiks adresu URL, aby nawiązać nazwę witryny sieci Web Datahub. |
|sentinelUsername | Nazwa użytkownika służąca do logowania (na przykład `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | Adres e-mail, na który będą wysyłane powiadomienia dotyczące alertów skonfigurowanych w ramach wystąpienia usługi Datahub.|
|updateIfExists| Obowiązkowe Parametr do uwzględnienia w pliku *Input. JSON* tylko wtedy, gdy chcesz uaktualnić istniejące wystąpienie usługi Azure FarmBeats. W przypadku uaktualnienia inne szczegóły, takie jak nazwy i lokalizacje grup zasobów, muszą być takie same.|
|aadAppClientId | Obowiązkowe Parametr do uwzględnienia w pliku *Input. JSON* tylko wtedy, gdy aplikacja usługi Azure AD już istnieje.  |
|aadAppClientSecret  | Obowiązkowe Parametr do uwzględnienia w pliku *Input. JSON* tylko wtedy, gdy aplikacja usługi Azure AD już istnieje.|

## <a name="deploy-the-app-within-cloud-shell"></a>Wdróż aplikację w Cloud Shell

Jako część wcześniej omówionego przepływu pracy portalu Marketplace musisz utworzyć jedną grupę zasobów i podpisała umowę "Postanowienia licencyjne", którą można ponownie przejrzeć w ramach rzeczywistego wdrożenia. Aplikację można wdrożyć za pomocą interfejsu wiersza polecenia opartego na przeglądarce Cloud Shell za pomocą środowiska bash. Aby wdrożyć za pośrednictwem Cloud Shell, przejdź do następnej sekcji.

> [!NOTE]
> Nieaktywne sesje Cloud Shell wygasną po upływie 20 minut. Spróbuj wykonać wdrożenie w tym czasie.

1. Zaloguj się do Azure Portal i wybierz subskrypcję i dzierżawcę platformy Azure, której chcesz użyć.
1. Uruchom Cloud Shell na pasku narzędzi w górnej części Azure Portal.
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału Azure Files.
1. Wybierz pozycję **Utwórz magazyn**.  
1. Dla środowiska SELECT **bash** (nie PowerShell).

## <a name="deployment-scenario-1"></a>Scenariusz wdrażania 1

W tym scenariuszu, który został opisany wcześniej w artykule "opcja 1," spowoduje automatyczne utworzenie rejestracji aplikacji usługi Azure AD. Wdrażasz FarmBeats, wykonując następujące czynności:

1. Skopiuj następujący przykładowy szablon JSON i Zapisz go jako *Input. JSON*. Pamiętaj, aby zanotować ścieżkę pliku na komputerze lokalnym.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Otwórz Azure Cloud Shell. Po pomyślnym uwierzytelnieniu wybierz przycisk **Przekaż** (wyróżniony na poniższej ilustracji), a następnie Przekaż plik *Input. json* do magazynu Cloud Shell.  

    ![FarmBeats projektu](./media/prepare-for-deployment/bash-2-1.png)

1. Przejdź do katalogu macierzystego w Cloud Shell. Domyślnie nazwa katalogu to */home/\<username >* .
1. W Cloud Shell wprowadź następujące polecenie. Pamiętaj, aby zmodyfikować ścieżkę do pliku *Input. JSON* , a następnie wybierz klawisz ENTER.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Instalator automatycznie pobiera wszystkie zależności i kompiluje wdrożenie. Zostanie wyświetlony monit o zgodę na postanowienia licencyjne dotyczące platformy Azure FarmBeats.

     - Jeśli zgadzasz się, wprowadź wartość **Y** , aby przejść do następnego kroku.
     - Jeśli użytkownik nie zgadza się, wprowadź **N**, a wdrożenie zostanie zakończone.

1. W wierszu polecenia wprowadź token dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się na [stronie logowania do urządzenia](https://microsoft.com/devicelogin) przy użyciu poświadczeń platformy Azure.

    > [!NOTE]
    > Token wygasa po 60 minutach. Jeśli wygaśnie, możesz ponownie uruchomić program, wpisując polecenie Deployment.

1. W wierszu polecenia wprowadź hasło do konta wskaźnikowego.

   Instalator weryfikuje i uruchamia wdrożenie, co może potrwać około 20 minut.

   Po pomyślnym zakończeniu wdrożenia otrzymasz następujące linki wyjściowe:

    - **Adres URL Datahub**: Link Swagger do wypróbowania interfejsów API usługi Azure FarmBeats.
    - **Adres URL akceleratora**: interfejs użytkownika do eksplorowania usługi Azure FarmBeats Smart Farm Accelerator.
    - **Plik dziennika programu Deployer**: plik dziennika, który został utworzony podczas wdrażania. W razie potrzeby można go użyć do rozwiązywania problemów.

## <a name="deployment-scenario-2"></a>Scenariusz wdrażania 2

W tym scenariuszu, który został opisany wcześniej w "opcji 2", Użyj istniejącej rejestracji aplikacji Azure Active Directory do wdrożenia FarmBeats, wykonując następujące czynności:

1. Skopiuj następujący plik JSON, który zawiera identyfikator klienta aplikacji platformy Azure i hasło w pliku *Input. JSON* , i Zapisz go. Pamiętaj, aby zanotować ścieżkę pliku na komputerze lokalnym.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. Przejdź do Azure Cloud Shell ponownie, a pomyślnie nastąpi uwierzytelnienie.
1. Wybierz przycisk **Przekaż** (wyróżniony na poniższej ilustracji), a następnie Przekaż plik *Input. json* do magazynu Cloud Shell Storage.

    ![FarmBeats projektu](./media/prepare-for-deployment/bash-2-1.png)

1. Przejdź do katalogu macierzystego w Cloud Shell. Domyślnie nazwa katalogu to */home/\<username >* .
1. W Cloud Shell wprowadź następujące polecenie. Pamiętaj, aby zmodyfikować ścieżkę do pliku *Input. JSON* , a następnie wybierz klawisz ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     Instalator automatycznie pobiera wszystkie zależności i kompiluje wdrożenie. Zostanie wyświetlony monit o zgodę na postanowienia licencyjne dotyczące platformy Azure FarmBeats.

     - Jeśli zgadzasz się, wprowadź wartość **Y** , aby przejść do następnego kroku.
     - Jeśli użytkownik nie zgadza się, wprowadź **N**, a wdrożenie zostanie zakończone.

1. W wierszu polecenia wprowadź token dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się na [stronie logowania do urządzenia](https://microsoft.com/devicelogin) przy użyciu poświadczeń platformy Azure.

   Instalator weryfikuje i uruchamia wdrożenie, co może potrwać około 20 minut.

   Po pomyślnym zakończeniu wdrożenia otrzymasz następujące linki wyjściowe:

    - **Adres URL Datahub**: Link Swagger do wypróbowania interfejsów API usługi Azure FarmBeats.
    - **Adres URL akceleratora**: interfejs użytkownika do eksplorowania usługi Azure FarmBeats Smart Farm Accelerator.
    - **Plik dziennika programu Deployer**: plik dziennika, który został utworzony podczas wdrażania. W razie potrzeby można go użyć do rozwiązywania problemów.

Jeśli napotkasz jakiekolwiek problemy, przejrzyj [problemy](troubleshoot-project-farmbeats.md).


## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="datahub"></a>Datahub

Po zakończeniu instalacji Datahub otrzymasz adres URL, aby uzyskać dostęp do interfejsów API usługi Azure FarmBeats za pośrednictwem interfejsu Swagger w formacie https://\<yourdatahub-Website-Name >. azurewebsites. NET/Swagger.

1. Aby zalogować się za pomocą struktury Swagger, skopiuj i wklej adres URL w przeglądarce.
2. Zaloguj się przy użyciu poświadczeń Azure Portal.
3. Można zobaczyć strukturę Swagger i wykonać wszystkie operacje REST w interfejsach API usługi Azure FarmBeats. Wskazuje to na pomyślne wdrożenie usługi Azure FarmBeats.

### <a name="accelerator"></a>Skrót

Po zakończeniu instalacji akceleratora otrzymasz adres URL, aby uzyskać dostęp do interfejsu użytkownika usługi Azure FarmBeats w formacie https://\<akcelerator-Website-Name >. azurewebsites. NET.

1. Aby zalogować się z akceleratora, skopiuj i wklej adres URL w przeglądarce.
1. Zaloguj się przy użyciu poświadczeń Azure Portal.

## <a name="upgrade"></a>Uaktualnienie

Instrukcje dotyczące uaktualniania są podobne do tych podczas instalacji po raz pierwszy. Wykonaj następujące czynności:

1. Zaloguj się do Azure Portal, a następnie wybierz żądaną subskrypcję i dzierżawcę platformy Azure.
1. Otwórz Cloud Shell z górnego obszaru nawigacyjnego Azure Portal.

   ![FarmBeats projektu](./media/prepare-for-deployment/navigation-bar-1.png)

1. Z listy rozwijanej **środowisko** po lewej stronie wybierz pozycję **bash**.
1. W razie potrzeby wprowadź zmiany w pliku *Input. JSON* , a następnie Przekaż go do Cloud Shell. Na przykład możesz zaktualizować adres e-mail powiadomienia, które chcesz otrzymywać.
1. Wpisz lub wklej poniższe dwa polecenia do Cloud Shell. Zmień ścieżkę pliku *Input. JSON* , a następnie wybierz klawisz ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   Instalator automatycznie wyświetli prośbę o wprowadzenie wymaganych danych wejściowych w czasie wykonywania.

1. Wprowadź token dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się na [stronie logowania urządzenia](https://microsoft.com/devicelogin) przy użyciu poświadczeń platformy Azure.
1. Wprowadź hasło do kontrolki.

   Instalator sprawdzi teraz i rozpocznie tworzenie zasobów, co może potrwać około 20 minut.

   Po pomyślnym zakończeniu wdrożenia otrzymasz następujące linki wyjściowe:
    - **Adres URL Datahub**: Link Swagger do wypróbowania interfejsów API usługi Azure FarmBeats.
    - **Adres URL akceleratora**: interfejs użytkownika do eksplorowania usługi Azure FarmBeats Smart Farm Accelerator.
    - **Plik dziennika programu Deployer**: plik dziennika, który został utworzony podczas wdrażania. W razie potrzeby można go użyć do rozwiązywania problemów.

> [!NOTE]
> Zanotuj poprzednie wartości do użycia w przyszłości.


## <a name="uninstall"></a>Dezinstalacja

Obecnie nie jest obsługiwana automatyczna Dezinstalacja usługi Azure FarmBeats za pomocą Instalatora. Aby usunąć Datahub lub akcelerator, w Azure Portal Usuń grupę zasobów, w której są zainstalowane te składniki, lub ręcznie usuń zasoby.

Na przykład w przypadku wdrożenia Datahub i akceleratora w dwóch różnych grupach zasobów można usunąć te grupy zasobów w następujący sposób:

1. Zaloguj się do Portalu Azure.
1. Wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD, w której chcesz wdrożyć usługę Azure FarmBeats.

   > [!NOTE]
   > Datahub jest niezbędny do poprawnego działania akceleratora. Nie zalecamy odinstalowywania Datahub bez również odinstalowywania akceleratora.

1. Wybierz pozycję **grupy zasobów**, a następnie wprowadź nazwę grupy zasobów Datahub lub akceleratora, którą chcesz usunąć.
1. Wybierz nazwę grupy zasobów. Wpisz ponownie nazwę, aby ją potwierdzić, a następnie wybierz pozycję **Usuń** , aby usunąć grupę zasobów i wszystkie jej zasoby bazowe.
   Alternatywnie możesz usunąć każdy zasób ręcznie, niezalecaną metodę.
1. Aby usunąć lub odinstalować Datahub, przejdź do grupy zasobów bezpośrednio na platformie Azure, a następnie usuń z niej grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak wdrożyć usługę Azure FarmBeats. Następnie Dowiedz się, jak [tworzyć farmy FarmBeats](manage-farms.md#create-farms).
