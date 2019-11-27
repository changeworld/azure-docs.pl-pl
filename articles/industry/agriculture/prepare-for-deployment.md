---
title: Wdrażanie usługi Azure FarmBeats
description: Opisuje sposób wdrażania FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0fc7eb6c3b582c4fc95d78397c4cb2820ebb4ea8
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534239"
---
# <a name="deploy-farmbeats"></a>Wdrażanie projektu FarmBeats

W tym artykule opisano sposób konfigurowania usługi Azure FarmBeats.

Usługa Azure FarmBeats to charakterystyczne dla branży rozwiązanie dla rolnictwa opartego na danych, które umożliwia bezproblemową obsługę administracyjną i urządzenia czujnika łączności z chmurą platformy Azure, zbieraniem danych telemetrii i agregacją. Usługa Azure FarmBeats ma różne czujniki, takie jak aparaty fotograficzne, dronom, czujniki gleby i zarządzanie urządzeniami z chmury, w tym infrastruktury i usług na platformie Azure dla urządzeń z systemem IoT-Read (Internet rzeczy) do extendible aplikacji sieci Web i mobilnej, aby zapewnić Wizualizacja, alerty i szczegółowe informacje.

> [!NOTE]
> Usługa Azure FarmBeats jest obsługiwana tylko w środowiskach chmury publicznej. Aby uzyskać więcej informacji na temat środowiska chmury, zobacz [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Usługa Azure FarmBeats ma dwa następujące składniki:

- **Centrum danych** — centrum danych to warstwa platformy platformy Azure FarmBeats, która umożliwia tworzenie, przechowywanie, przetwarzanie danych i uzyskiwanie szczegółowych informacji z istniejących lub nowych potoków danych. Ta warstwa platformy jest przydatna do uruchamiania i kompilowania potoków danych i modeli w rolnictwie.

- **Akcelerator** -akcelerator to warstwa rozwiązania platformy Azure FarmBeats, która ma wbudowaną aplikację do zilustrowania możliwości usługi Azure FarmBeats przy użyciu wstępnie utworzonych modeli rolnictwa. Ta warstwa rozwiązania umożliwia tworzenie granic farmy i uzyskiwanie szczegółowych informacji z danych rolnictwa w kontekście granicy farmy.

Szybkie wdrożenie usługi Azure FarmBeats powinno trwać krócej niż godzinę. Koszty dla centrum danych i akceleratora różnią się w zależności od użycia.

## <a name="deployed-resources"></a>Wdrożone zasoby

Wdrożenie usługi Azure FarmBeats powoduje utworzenie wymienionych poniżej zasobów w ramach subskrypcji:

|L.p.  |Nazwa zasobu  |Składnik Azure FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Centrum danych       |
|2  |    Application Insights      |     Centrum danych/akcelerator     |
|3  |Azure Cache for Redis   |Centrum danych   |
|4  |       Magazyn kluczy platformy Azure    |  Centrum danych/akcelerator        |
|5  |    Time Series Insights       |     Centrum danych      |
|6 |      Przestrzeń nazw EventHub    |  Centrum danych       |
|7  |    Azure Data Factory V2       |     Centrum danych/akcelerator      |
|8  |Konto usługi Batch    |Centrum danych   |
|9  |       Konto magazynu     |  Centrum danych/akcelerator        |
|10  |    Aplikacja logiki        |     Centrum danych      |
|11  |    Połączenie interfejsu API        |     Centrum danych      |
|12|      Usługa App Service      |  Centrum danych/akcelerator       |
|13 |    plan usługi App Service        |     Centrum danych/akcelerator      |
|14 |Konto Azure Maps     |Skrót    |
|15 |       Time Series Insights      |  Centrum danych     |

Usługa Azure FarmBeats jest dostępna do pobrania z witryny Azure Marketplace. Możesz uzyskać do niego dostęp bezpośrednio z Azure Portal.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Tworzenie oferty Azure FarmBeats na rynku Marketplace

Wykonaj następujące kroki, aby utworzyć ofertę platformy Azure FarmBeats w portalu Marketplace:

1. Zaloguj się do Azure Portal i wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD, w której chcesz wdrożyć Microsoft Azure FarmBeats.
2. Usługa Azure FarmBeats jest dostępna w witrynie Azure Marketplace. Na stronie Marketplace wybierz pozycję "Pobierz teraz".
3. Wybierz pozycję Utwórz i wprowadź następujące informacje:
    - Nazwa subskrypcji.
    - istniejąca nazwa grupy zasobów (tylko pusta Grupa zasobów) lub Utwórz nową grupę zasobów na potrzeby wdrażania usługi Azure FarmBeats. Zanotuj tę grupę zasobów w kolejnych sekcjach.
4. Region, w którym chcesz zainstalować usługę Azure FarmBeats. Obecnie usługa Azure FarmBeats jest obsługiwana w następujących regionach: środkowe stany USA, Europa Zachodnia, Wschodnie stany USA 2, Europa Północna, zachodnie stany USA, Azja Południowo-Wschodnia, Wschodnie stany USA, Australia Wschodnia, zachodnie stany USA 2.
5. Kliknij przycisk **OK**.
Zostanie wyświetlona strona Warunki użytkowania. Przejrzyj standardowe warunki witryny Marketplace lub wybierz hiperlink, aby przejrzeć warunki użytkowania.
6. Wybierz pozycję **Zamknij**, a następnie pole wyboru "Zgadzam się", a następnie wybierz pozycję **Utwórz**.
7. Pomyślnie podpisano umowę licencyjną użytkownika końcowego (EULA) usługi Azure FarmBeats w portalu Marketplace.  
7. Aby kontynuować wdrażanie, wykonaj kolejne kroki opisane w tym przewodniku.

> [!NOTE]
> Ukończenie oferty w portalu Azure Marketplace kończy się tylko częścią instalacji. Postępuj zgodnie z poniższymi instrukcjami, aby ukończyć wdrażanie usługi Azure FarmBeats w ramach subskrypcji platformy Azure.

## <a name="prepare"></a>Przygotowanie

Do wdrożenia usługi Azure FarmBeats potrzebne są następujące uprawnienia:

- Dzierżawca: dostęp do odczytu
- Subskrypcja: współautor lub właściciel
- Grupa zasobów: właściciel

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed zainicjowaniem wdrożenia programu upewnij się, że zostały wymienione następujące elementy:

- Konto wskaźnikiem
- Rejestracja aplikacji Azure Active Directory (AD)

## <a name="create-a-sentinel-account"></a>Tworzenie konta wskaźnikowego    

Konto z wskaźnikiem kontrolnym pomaga w pobieraniu obrazów satelitarnych ze wskaźnikami z oficjalnej witryny sieci Web do urządzenia. Wykonaj następujące kroki, aby utworzyć bezpłatne konto:

Przejdź do pozycji https://scihub.copernicus.eu/dhus/#/self-registration (Plik > Nowy > Inny). Na stronie Rejestracja Podaj imię, nazwisko, nazwę użytkownika, hasło i adres e-mail.
Do potwierdzenia zostanie wysłana wiadomość e-mail z weryfikacją. Wybierz łącze i potwierdź. Proces rejestracji został ukończony.

## <a name="create-azure-ad-app-registration"></a>Utwórz rejestrację aplikacji usługi Azure AD

W celu uwierzytelniania i autoryzacji w usłudze Azure FarmBeats należy mieć rejestrację aplikacji usługi Azure Active Directory, która:

- Przypadek 1: Instalator może utworzyć automatycznie (pod warunkiem, że masz wymagane uprawnienia dostępu dzierżawców, subskrypcji i grupy zasobów).
- Przypadek 2: możesz utworzyć i skonfigurować przed wdrożeniem usługi Azure FarmBeats (wymaga ręcznego wykonywania kroków).

**Przypadek 1**:: Jeśli masz dostęp do tworzenia rejestracji aplikacji usługi AAD, możesz pominąć ten krok i pozwolić Instalatorowi na utworzenie rejestracji aplikacji. Przejdź do następnej sekcji: [Przygotuj plik INPUT. JSON](#prepare-input-json-file)

Jeśli masz już subskrypcję, możesz bezpośrednio przenieść ją do następnej procedury.

**Przypadek 2**: Ta metoda jest preferowanym krokiem, gdy nie masz wystarczających uprawnień, aby utworzyć i skonfigurować rejestrację aplikacji usługi Azure AD w ramach subskrypcji. Poproś administratora o użycie [niestandardowego skryptu](https://aka.ms/FarmBeatsAADScript), co pomoże administratorowi IT automatycznie generować i konfigurować rejestrację aplikacji usługi Azure AD na Azure Portal. Jako dane wyjściowe do uruchamiania tego skryptu niestandardowego przy użyciu środowiska PowerShell administrator IT musi udostępnić Ci Azure Active Directory identyfikator klienta aplikacji i klucz tajny hasła. Zanotuj te wartości.

Wykonaj następujące kroki, aby uruchomić skrypt rejestracji aplikacji usługi Azure AD:

1. Pobierz [skrypt](https://aka.ms/FarmBeatsAADScript).
2. Zaloguj się do Azure Portal i wybierz swoją subskrypcję i dzierżawcę usługi AD.
3. Uruchom usługę Cloud Shell z górnego obszaru nawigacyjnego witryny Azure Portal.

    ![Farmy projektów](./media/prepare-for-deployment/navigation-bar-1.png)


4. Użytkownicy po raz pierwszy będą monitowani o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn**.
5. Gdy użytkownicy będą monitowani o wybranie preferowanego środowiska powłoki — bash lub PowerShell. Wybierz pozycję PowerShell.
6. Przekaż skrypt (z kroku 1) do Cloud Shell i zanotuj lokalizację przekazanego pliku.

    > [!NOTE]
    > Domyślnie jest on przekazywany do katalogu macierzystego.

    Użyj następującego skryptu:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Zanotuj identyfikator aplikacji usługi Azure AD i klucz tajny klienta, aby udostępnić uczestnikowi wdrażanie usługi Azure FarmBeats.

### <a name="prepare-input-json-file"></a>Przygotuj wejściowy plik JSON

W ramach instalacji Utwórz plik INPUT. JSON w następujący sposób:

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

Ten plik jest plikiem wejściowym do Azure Cloud Shell i parametrów, których wartości są używane podczas instalacji. Wszystkie parametry w formacie JSON muszą zostać zastąpione odpowiednimi wartościami lub usunięte; w przypadku usunięcia Instalator wyświetli monit z pytaniem podczas instalacji


Przejrzyj parametry przed przygotowaniem pliku.

|Polecenie | Opis|
|--- | ---|
|sku  | Zapewnia możliwość pobrania albo obu składników usługi Azure FarmBeats. Określa składniki do pobrania. Aby zainstalować tylko centrum danych, użyj "onlydatabhub". Aby zainstalować centrum danych i akcelerator, użyj "obu"|
|subscriptionId | Określa subskrypcję do zainstalowania usługi Azure FarmBeats|
|datahubResourceGroup| Nazwa grupy zasobów dla zasobów centrum danych|
|location |Lokalizacja, w której chcesz utworzyć zasoby|
|acceleratorWebsiteName |Unikatowy prefiks adresu URL do nazwy centrum danych|
|acceleratorResourceGroup  | Unikatowy prefiks adresu URL, aby nawiązać nazwę witryny sieci Web akceleratora.|
|datahubWebsiteName  | UUnique prefiks adresu URL, aby nazwać witrynę sieci Web centrum danych. |
|sentinelUsername | Nazwa użytkownika do zalogowania: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | Adres e-mail, na który będą wysyłane powiadomienia dotyczące alertów skonfigurowanych w ramach centrum danych.|
|updateIfExists|Obowiązkowe Parametr, który ma być uwzględniony w pliku Input. JSON tylko wtedy, gdy chcesz uaktualnić istniejące wystąpienie usługi Azure FarmBeats. W przypadku uaktualniania inne szczegóły na przykład. nazwy grup zasobów, lokalizacje itp. muszą być takie same.|
|aadAppClientId | [**Opcjonalne**] Parametr, który ma zostać uwzględniony w pliku Input. JSON tylko wtedy, gdy już istnieje aplikacja usługi Azure AD.  |
|aadAppClientSecret  | [**Opcjonalne**] Parametr, który ma zostać uwzględniony w pliku Input. JSON tylko wtedy, gdy już istnieje aplikacja usługi Azure AD.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Wdrażanie w ramach wiersza polecenia opartego na przeglądarce Cloud Shell

W ramach przepływu pracy w portalu Marketplace należy utworzyć jedną grupę zasobów i podpisać umowę licencyjną użytkownika końcowego, która może zostać ponownie zweryfikowana w ramach rzeczywistego wdrożenia. Wdrożenie można przeprowadzić za pośrednictwem Azure Cloud Shell (wiersz polecenia oparty na przeglądarce) przy użyciu środowiska bash. Przejdź do następnych sekcji, aby przeprowadzić wdrożenie za pośrednictwem Cloud Shell.

> [!NOTE]
> Nieaktywne sesje Cloud Shell wygasną po upływie 20 minut. Spróbuj wykonać wdrożenie w tym czasie.

1. Zaloguj się do Azure Portal i wybierz żądaną subskrypcję i dzierżawcę usługi AD.
2. Uruchom usługę Cloud Shell z górnego obszaru nawigacyjnego witryny Azure Portal.
3. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure.
4. Wybierz pozycję **Utwórz magazyn**.  

Wybierz środowisko jako bash (a nie PowerShell).

## <a name="deployment-scenario-1"></a>Scenariusz wdrażania 1

Instalator tworzy aplikacja usługi Azure AD rejestracji (przypadek 1 powyżej)

1. Skopiuj następujący szablon i nadaj mu nazwę input. JSON.  
Przykładowe dane wejściowe JSON:

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

2. Zapisz plik i Zanotuj ścieżkę (na komputerze lokalnym).
3. Przejdź do Azure Cloud Shell i po pomyślnym uwierzytelnieniu wybierz pozycję Przekaż (zobacz wyróżnioną ikonę poniżej obrazu) i Przekaż plik INPUT. JSON do magazynu Cloud Shell Storage.  

    ![Farmy projektów](./media/prepare-for-deployment/bash-2-1.png)

4. Przejdź do katalogu macierzystego w usłudze Cloud Shell. Domyślnie jest to/Home/<username>
5. Wpisz lub Wklej następujące polecenie do Cloud Shell. Upewnij się, że ścieżka została zmodyfikowana. plik JSON i naciśnij klawisz ENTER.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Instalator automatycznie pobiera wszystkie zależności i kompiluje wdrożenie. Zostanie wyświetlony monit o wyrażenie zgody na umowę licencyjną użytkownika oprogramowania platformy Azure FarmBeats (EULA).

     - Wprowadź wartość "Y", jeśli zgadzasz się i przejdziesz do następnego kroku.
     - Wprowadź wartość "N", jeśli nie akceptujesz warunków, a wdrożenie zakończy się.

6. Następnie zostanie wyświetlony monit o wprowadzenie tokenu dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się, aby https://microsoft.com/devicelogin przy użyciu poświadczeń platformy Azure.

    > [!NOTE]
    > Token wygasa po 60 minutach. Po jego wygaśnięciu można uruchomić ponownie, wpisując polecenie wdrożenia ponownie.

7. Po wyświetleniu monitu wprowadź hasło do konta wskaźnikowego.
8. Instalator sprawdzi teraz i rozpocznie wdrażanie, co może potrwać około 20 minut.
9. Po pomyślnym wdrożeniu otrzymasz poniższe linki wyjściowe:

 - **Adres URL centrum danych**: Link Swagger umożliwiający wypróbowanie interfejsów API usługi Azure FarmBeats.
 - **Adres URL akceleratora**: interfejs użytkownika umożliwiający Eksplorowanie akceleratora usługi Azure FarmBeats Smart Farm.
 - Plik **dziennika wdrażania**— plik dziennika utworzony podczas wdrażania. Może służyć do rozwiązywania problemów, jeśli jest to wymagane.

## <a name="deployment-scenario-2"></a>Scenariusz wdrażania 2

Istniejąca Rejestracja aplikacji Azure Active Directory jest używana do wdrożenia (przypadek 2 powyżej)

1. Skopiuj poniższy plik JSON, który zawiera identyfikator i hasło klienta aplikacji platformy Azure w pliku Input. JSON, i Zapisz go.

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

Wykonaj pozostałe kroki:

2. Zanotuj ścieżkę do pliku Input. JSON (na komputerze lokalnym).
3. Przejdź do Azure Cloud Shell ponownie, a po pomyślnym uwierzytelnieniu wybierz przycisk Przekaż (zobacz wyróżnioną ikonę poniżej obrazu) i Przekaż plik INPUT. JSON do Cloud Shell Storage.

    ![Farmy projektów](./media/prepare-for-deployment/bash-2-1.png)

4. Przejdź do katalogu macierzystego w usłudze Cloud Shell. Domyślnie jest to/Home/<username>
5. Wpisz lub Wklej następujące polecenie do Cloud Shell. Upewnij się, że ścieżka została zmodyfikowana. plik JSON i naciśnij klawisz ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

6. Skrypt automatycznie pobiera wszystkie zależności i kompiluje wdrożenie.
7. Zostanie wyświetlony monit o odczytanie i zaakceptowanie umowy licencyjnej użytkownika oprogramowania platformy Azure FarmBeats.

    - Wprowadź wartość "Y", jeśli zgadzasz się i przejdziesz do kolejnego kroku.
    - Wprowadź wartość "N", jeśli nie akceptujesz warunków, a wdrożenie zakończy się.

8. Zostanie wyświetlony monit o wprowadzenie tokenu dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się do https://microsoft.com/devicelogin przy użyciu poświadczeń platformy Azure.
9. Instalator sprawdzi teraz i rozpocznie tworzenie zasobów, co może potrwać około 20 minut. W tym czasie sesja powinna być aktywna na Cloud Shell.
10. Po pomyślnym przejściu wdrożenia zostaną wyświetlone poniższe linki wyjściowe:

 - **Adres URL centrum danych**: Link Swagger umożliwiający wypróbowanie interfejsów API usługi Azure FarmBeats.
 - **Adres URL akceleratora**: interfejs użytkownika do eksplorowania usługi Azure FarmBeats Accelerator.
 - **Plik dziennika wdrażania**: plik dziennika utworzony podczas wdrażania. Może służyć do rozwiązywania problemów, jeśli jest to wymagane.

Jeśli napotkasz jakiekolwiek problemy, przejrzyj [problemy](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Weryfikuj wdrożenie

### <a name="data-hub"></a>Centrum danych

Po zakończeniu instalacji centrum danych otrzymasz adres URL umożliwiający dostęp do interfejsów API usługi Azure FarmBeats za pośrednictwem interfejsu Swagger w formacie: https://\<yourdatahub-Website-Name >. azurewebsites. NET/Swagger

1. Aby zalogować się za pomocą struktury Swagger, skopiuj i wklej adres URL w przeglądarce.
2. Zaloguj się przy użyciu poświadczeń Azure Portal.
3. Test Sanity (opcjonalnie)

     - Można pomyślnie zalogować się do portalu Swagger przy użyciu linku centrum danych, który został odebrany jako dane wyjściowe do pomyślnego wdrożenia.
     - Rozszerzone typy pobieranie interfejsu API — wybierz pozycję "Wypróbuj to/Execute"
     - Powinien zostać wyświetlony kod odpowiedzi serwera 200, a nie wyjątek, taki jak 403 "nieautoryzowany użytkownik".

### <a name="accelerator"></a>Skrót

Po zakończeniu instalacji akceleratora otrzymasz adres URL, aby uzyskać dostęp do interfejsu użytkownika usługi Azure FarmBeats w formacie: https://\<akcelerator-Website-Name >. azurewebsites. NET

1. Aby zalogować się z akceleratora, skopiuj i wklej adres URL w przeglądarce.
2. Zaloguj się przy użyciu poświadczeń Azure Portal.

## <a name="upgrade"></a>Uaktualnienie

Kroki uaktualniania są podobne do instalacji po raz pierwszy. Wykonaj następujące kroki:

1. Zaloguj się do Azure Portal i wybierz żądaną subskrypcję i dzierżawcę usługi AD.
2. Uruchom usługę Cloud Shell z górnego obszaru nawigacyjnego witryny Azure Portal.

   ![Farmy projektów](./media/prepare-for-deployment/navigation-bar-1.png)

3. Wybierz środowisko jako "bash" z listy rozwijanej z lewej strony powłoki.
4. Wprowadź zmiany w pliku Input. JSON tylko wtedy, gdy jest to konieczne, i Przekaż do Azure Cloud Shell. Na przykład możesz zaktualizować adres e-mail powiadomienia, które chcesz otrzymywać.
5. Przekaż plik INPUT. JSON do Azure Cloud Shell.
6. Wpisz lub wklej poniższe dwa polecenia do Cloud Shell. Upewnij się, że ścieżka została zmodyfikowana do pliku Input. JSON, a następnie naciśnij klawisz ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:

7. Instalator automatycznie wyświetli wymagane dane wejściowe w czasie wykonywania:
8. Wprowadź token dostępu dla wdrożenia. Skopiuj wygenerowany kod i zaloguj się do https://microsoft.com/devicelogin przy użyciu poświadczeń platformy Azure.
9. Hasło do kontrolki
10. Instalator sprawdzi teraz i rozpocznie tworzenie zasobów, co może potrwać około 20 minut.
11. Po pomyślnym wdrożeniu otrzymasz poniższe linki wyjściowe:
 - **Adres URL centrum danych**: Link Swagger umożliwiający wypróbowanie interfejsów API usługi Azure FarmBeats.
 - **Adres URL akceleratora**: interfejs użytkownika do eksplorowania usługi Azure FarmBeats Accelerator.
 - **Plik dziennika wdrażania**: zapisuje dzienniki podczas wdrażania. Może służyć do rozwiązywania problemów.

> [!NOTE]
> Zanotuj powyższe wartości do użycia w przyszłości.


## <a name="uninstall"></a>Dezinstalacja

Obecnie nie jest obsługiwana automatyczna Dezinstalacja usługi Azure FarmBeats za pomocą Instalatora. Aby usunąć centrum danych lub akcelerator, w Azure Portal Usuń grupę zasobów, w której te składniki są zainstalowane, lub ręcznie usuń zasoby.

Przykładowo w przypadku wdrożenia centrum danych i akceleratora w dwóch różnych grupach zasobów te grupy zasobów można usunąć w następujący sposób:

1. Zaloguj się do witryny Azure Portal.
2. Wybierz swoje konto w prawym górnym rogu i przejdź do odpowiedniej dzierżawy usługi Azure AD, w której chcesz wdrożyć usługę Azure FarmBeats.

   > [!NOTE]
   > Aby akcelerator działał prawidłowo, wymagany jest centrum danych. Nie zalecamy odinstalowywania centrum danych bez odinstalowywania akceleratora.

3. Wybierz pozycję grupy zasobów, a następnie wpisz nazwę grupy zasobów, która ma zostać usunięta.
4. Wybierz nazwę grupy zasobów. Wpisz ponownie nazwę, aby zaznaczyć pole wyboru podwójne, a następnie wybierz pozycję Usuń, aby usunąć grupę zasobów i wszystkie jej zasoby bazowe.
5. Alternatywnie możesz usunąć poszczególne zasoby ręcznie, co nie jest zalecane.
7. Aby usunąć/odinstalować centrum danych, przejdź do grupy zasobów bezpośrednio na platformie Azure i Usuń z niej grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Wdrożono usługę Azure FarmBeats. Teraz Dowiedz się, jak [tworzyć farmy](manage-farms.md#create-farms).
