---
title: Wdrażanie narzędzia diagnostycznego dla pulpitu wirtualnego systemu Windows — Azure
description: Jak wdrożyć narzędzie diagnostyki ux dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123402"
---
# <a name="deploy-the-diagnostics-tool"></a>Wdrażanie narzędzia diagnostycznego

>[!IMPORTANT]
>Od 16 marca 2020 r. tymczasowo wyłączyliśmy zapytania diagnostyczne, które wpłynęły na środowisko użytkownika ze względu na zwiększone zapotrzebowanie na usługę. Spowoduje to, że narzędzie przestanie działać, ponieważ opiera się na tych kwerend do działania. Zaktualizujemy ten artykuł, gdy kwerendy diagnostyczne są ponownie dostępne.
>
>Do tego czasu zdecydowanie zaleca się [używanie usługi Log Analytics](diagnostics-log-analytics.md) do ciągłego monitorowania.

Oto, co narzędzie diagnostyczne dla pulpitu wirtualnego systemu Windows może zrobić dla Ciebie:

- Szukaj działań diagnostycznych (zarządzania, połączenia lub kanału informacyjnego) dla pojedynczego użytkownika w okresie jednego tygodnia.
- Zbieranie informacji o hoście sesji dla działań związanych z połączeniem z obszaru roboczego usługi Log Analytics.
- Przejrzyj szczegóły dotyczące wydajności maszyny wirtualnej dla określonego hosta.
- Zobacz, którzy użytkownicy są zalogowane do hosta sesji.
- Wyślij wiadomość do aktywnych użytkowników na określonym hoście sesji.
- Wyloguj użytkowników z hosta sesji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem szablonu usługi Azure Resource Manager dla tego narzędzia należy utworzyć rejestrację aplikacji usługi Azure Active Directory i obszar roboczy usługi Log Analytics. Ty lub administrator potrzebujesz tych uprawnień, aby to zrobić:

- Właściciel subskrypcji platformy Azure
- Uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Uprawnienie do tworzenia aplikacji usługi Azure AD
- Prawa właściciela lub współautora usług RDS

Przed rozpoczęciem pracy należy również zainstalować te dwa moduły programu PowerShell:

- [Moduł programu Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Moduł usługi Azure AD](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Upewnij się, że masz gotowy identyfikator subskrypcji po zalogowaniu się.

Po wszystko w porządku, można utworzyć rejestrację aplikacji usługi Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Tworzenie rejestracji aplikacji usługi Azure Active Directory

W tej sekcji pokazano, jak używać programu PowerShell do tworzenia aplikacji usługi Azure Active Directory z jednostką usługi i uzyskania uprawnień interfejsu API.

>[!NOTE]
>Uprawnienia interfejsu API to pulpit wirtualny systemu Windows, analiza dzienników i uprawnienia interfejsu API programu Microsoft Graph są dodawane do aplikacji usługi Azure Active Directory.

1. Uruchom program Windows PowerShell jako administrator.
2. Zaloguj się na platformę Azure przy użyciu konta, które ma uprawnienia właściciela lub współautora w ramach subskrypcji platformy Azure, której chcesz użyć dla narzędzia diagnostycznego:
   ```powershell
   Login-AzAccount
   ```
3. Zaloguj się do usługi Azure AD przy tym samym koncie:
   ```powershell
   Connect-AzureAD
   ```
4. Przejdź do [repozytorium GitHub szablonów usług PULPITU RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) i uruchom skrypt **CreateADAppRegistrationforDiagnostics.ps1** w programie PowerShell.
5.  Gdy skrypt poprosi o nadawanie nazwy aplikacji, wprowadź unikatową nazwę aplikacji.


Po pomyślnym uruchomieniu skryptu powinien on wyświetlić następujące elementy w jego danych wyjściowych:

-  Komunikat, który potwierdza, że aplikacja ma teraz przypisanie roli głównej usługi.
-  Identyfikator klienta i klucz tajny klienta, które będą potrzebne podczas wdrażania narzędzia diagnostycznego.

Po zarejestrowaniu aplikacji nadszedł czas, aby skonfigurować obszar roboczy usługi Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego usługi Log Analytics

Aby uzyskać najlepsze możliwe środowisko, zaleca się skonfigurowanie obszaru roboczego usługi Log Analytics za pomocą następujących liczników wydajności, które umożliwiają uzyskiwanie instrukcji środowiska użytkownika w sesji zdalnej. Aby uzyskać listę zalecanych liczników z sugerowanymi progami, zobacz [Progi liczników wydajności systemu Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Tworzenie obszaru roboczego usługi Azure Log Analytics przy użyciu programu PowerShell

Można uruchomić skrypt programu PowerShell, aby utworzyć obszar roboczy usługi Log Analytics i skonfigurować zalecane liczniki wydajności systemu Windows, aby monitorować środowisko użytkownika i wydajność aplikacji.

>[!NOTE]
>Jeśli masz już istniejący obszar roboczy usługi Log Analytics, który został wykonany bez skryptu programu PowerShell, którego chcesz użyć, przejdź do [sprawdzania poprawności wyników skryptu w witrynie Azure portal](#validate-the-script-results-in-the-azure-portal).

Aby uruchomić skrypt programu PowerShell:

1.  Otwórz program PowerShell jako administrator.
2.  Przejdź do [repozytorium GitHub szablonów usług pulpitu zdalnego](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) i uruchom skrypt **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** w programie PowerShell.
3. Wprowadź następujące wartości parametrów:

    - W przypadku **aplikacji ResourceGroupName**wprowadź nazwę grupy zasobów.
    - W przypadku **aplikacji LogAnalyticsWorkspaceName**wprowadź unikatową nazwę obszaru roboczego usługi Log Analytics.
    - W obszarze **Lokalizacja**wprowadź używany region platformy Azure.
    - Wprowadź **identyfikator subskrypcji platformy Azure,** który można znaleźć w witrynie Azure portal w obszarze **Subskrypcje**.

4. Wprowadź poświadczenia użytkownika z delegowanym dostępem administratora.
5. Zaloguj się do witryny Azure portal przy użyciu poświadczeń tego samego użytkownika.
6. Zapisz lub zapamiętaj identyfikator LogAnalyticsWorkspace na później.
7. Jeśli skonfigurowano obszar roboczy usługi Log Analytics za pomocą skryptu programu PowerShell, liczniki wydajności powinny być już skonfigurowane i można przejść do przodu, aby [sprawdzić poprawność wyników skryptu w witrynie Azure portal](#validate-the-script-results-in-the-azure-portal). W przeciwnym razie przejdź do następnej sekcji.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Konfigurowanie liczników wydajności systemu Windows w istniejącym obszarze roboczym usługi Log Analytics

Ta sekcja jest dla użytkowników, którzy chcą korzystać z istniejącego obszaru roboczego usługi Azure Log Analytics utworzonego bez skryptu programu PowerShell w poprzedniej sekcji. Jeśli skrypt nie został użyty, należy ręcznie skonfigurować zalecane liczniki wydajności systemu Windows.

Aby ręcznie skonfigurować zalecane liczniki wydajności:

1. Otwórz przeglądarkę internetową i zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą konta administracyjnego.
2. Następnie przejdź do **obszarów roboczych usługi Log Analytics,** aby przejrzeć skonfigurowane liczniki wydajności systemu Windows.
3. W sekcji **Ustawienia** wybierz pozycję **Ustawienia zaawansowane**.
4. Następnie przejdź do**liczników wydajności systemu** **Data** > Windows i dodaj następujące liczniki:

    -   Logiczny\*dysk(\\) %wolne miejsce
    -   Logiczny(C:)\\Średnia długość kolejki dysku
    -   Pamięć(\*\\) Dostępne bajty
    -   Informacje o\*\\procesorze( ) Czas procesora
    -   Opóźnienie wejścia użytkownika\*na\\sesję( ) Maksymalne opóźnienie wejścia

Dowiedz się więcej o licznikach wydajności w [źródłach danych wydajności systemu Windows i Linux w usłudze Azure Monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Wszystkie dodatkowe liczniki, które konfigurujesz, nie będą wyświetlane w samym narzędziu diagnostycznym. Aby był wyświetlany w narzędziu diagnostycznym, należy skonfigurować plik konfiguracyjny narzędzia. Instrukcje, jak to zrobić za pomocą zaawansowanej administracji będą dostępne w usłudze GitHub w późniejszym terminie.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Sprawdzanie poprawności wyników skryptu w witrynie Azure portal

Przed kontynuowaniem wdrażania narzędzia diagnostycznego zaleca się sprawdzenie, czy aplikacja usługi Azure Active Directory ma uprawnienia interfejsu API, a obszar roboczy usługi Log Analytics ma wstępnie skonfigurowane liczniki wydajności systemu Windows.

### <a name="review-your-app-registration"></a>Sprawdź rejestrację aplikacji

Aby upewnić się, że rejestracja aplikacji ma uprawnienia interfejsu API:

1. Otwórz przeglądarkę i połącz się z [witryną Azure portal](https://portal.azure.com/) za pomocą konta administracyjnego.
2. Przejdź do **usługi Azure Active Directory**.
3. Przejdź do **rejestracji aplikacji** i wybierz pozycję **Wszystkie aplikacje**.
4. Poszukaj rejestracji aplikacji usługi Azure AD o tej samej nazwie aplikacji wprowadzonej w kroku 5 [tworzenia rejestracji aplikacji usługi Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Przeglądanie obszaru roboczego usługi Log Analytics

Aby upewnić się, że w obszarze roboczym usługi Log Analytics są wstępnie skonfigurowane liczniki wydajności systemu Windows:

1. W [witrynie Azure portal](https://portal.azure.com/)przejdź do **obszarów roboczych usługi Log Analytics,** aby przejrzeć skonfigurowane liczniki wydajności systemu Windows.
2. W obszarze **Ustawienia**wybierz pozycję **Ustawienia zaawansowane**.
3. Następnie przejdź do**liczników wydajności systemu** **Data** > Windows .
4. Upewnij się, że następujące liczniki są wstępnie skonfigurowane:

   - LogicalDisk(\*\\) %Free Space: Wyświetla ilość wolnego miejsca w całkowitej ilości miejsca użytkowego na dysku jako wartość procentową.
   - Logiczny dysk(C:)\\Średnia długość kolejki dysku: Długość żądania transferu dysku dla dysku C. Wartość nie powinna przekraczać 2 przez dłuższy niż krótki okres czasu.
   - Pamięć(\*\\) Dostępne bajty: Dostępna pamięć dla systemu w megabajtach.
   - Informacje o\*\\procesorze( ) Czas procesora: procent czasu, który procesor spędza na wykonanie wątku nieuległego bezczynności.
   - Opóźnienie wejścia użytkownika\*na\\sesję( ) Maksymalne opóźnienie wejścia

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Łączenie się z maszynami wirtualnymi w obszarze roboczym usługi Log Analytics

Aby móc wyświetlać kondycję maszyn wirtualnych, musisz włączyć połączenie usługi Log Analytics. Wykonaj następujące kroki, aby połączyć maszyny wirtualne:

1. Otwórz przeglądarkę i zaloguj się do [witryny Azure portal](https://portal.azure.com/) za pomocą konta administracyjnego.
2. Przejdź do obszaru roboczego analizy dzienników.
3. W lewym panelu w obszarze Źródła danych obszaru roboczego wybierz pozycję **Maszyny wirtualne**.
4. Wybierz nazwę maszyny Wirtualnej, z którą chcesz się połączyć.
5. Wybierz przycisk **Połącz**.

## <a name="deploy-the-diagnostics-tool"></a>Wdrażanie narzędzia diagnostycznego

Aby wdrożyć szablon zarządzania zasobami platformy Azure dla narzędzia diagnostycznego:

1.  Przejdź do [strony Szablony usług pulpitu zdalnego platformy GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Wdrażanie szablonu na platformie Azure i postępuj zgodnie z instrukcjami w szablonie. Upewnij się, że dostępne są następujące informacje:

    -   Identyfikator klienta
    -   Klucz tajny klienta
    -   Identyfikator obszaru roboczego usługi Log Analytics

3.  Po podaniu parametrów wejściowych zaakceptuj warunki, a następnie wybierz **pozycję Kup**.

Wdrożenie potrwa od 2 do 3 minut. Po pomyślnym wdrożeniu przejdź do grupy zasobów i upewnij się, że zasoby aplikacji sieci web i planu usługi aplikacji są tam dostępne.

Następnie należy ustawić identyfikator URI przekierowania.

### <a name="set-the-redirect-uri"></a>Ustawianie identyfikatora URI przekierowania

Aby ustawić identyfikator URI przekierowania:

1.  W [witrynie Azure portal](https://portal.azure.com/)przejdź do **usługi App Services** i znajdź utworzoną aplikację.
2.  Przejdź do strony przeglądu i skopiuj adres URL, który tam znajdziesz.
3.  Przejdź do **rejestracji aplikacji** i wybierz aplikację, którą chcesz wdrożyć.
4.  W lewym panelu w sekcji Zarządzaj wybierz pozycję **Uwierzytelnianie**.
5.  Wprowadź żądany identyfikator URI przekierowania do pola tekstowego **Przekierowanie identyfikatora URI,** a następnie wybierz pozycję **Zapisz** w lewym górnym rogu menu.
6. Wybierz **pozycję Web** w menu rozwijanym w obszarze Typ.
7. Wprowadź adres URL ze strony przeglądu aplikacji i dodaj **/security/signin-callback** na końcu. Na przykład: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Strona przekierowania identyfikatora URI](media/redirect-uri-page.png)

8. Teraz przejdź do zasobów platformy Azure, wybierz zasób usługi Azure App Services o nazwie podanej w szablonie i przejdź do skojarzonego z nim adresu URL. (Na przykład, jeśli nazwa aplikacji użyta `contosoapp45`w szablonie była <https://contosoapp45.azurewebsites.net>, to skojarzony adres URL jest ).
9. Zaloguj się przy użyciu odpowiedniego konta użytkownika usługi Azure Active Directory.
10.   Wybierz pozycję **Zaakceptuj**.

## <a name="distribute-the-diagnostics-tool"></a>Dystrybucja narzędzia diagnostycznego

Przed udostępnieniem narzędzia diagnostycznego użytkownikom upewnij się, że mają następujące uprawnienia:

- Użytkownicy potrzebują dostępu do odczytu do analizy dzienników. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do ról, uprawnień i zabezpieczeń w usłudze Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security).
-  Użytkownicy potrzebują również dostępu do odczytu dla dzierżawy pulpitu wirtualnego systemu Windows (rola czytnika RDS). Aby uzyskać więcej informacji, zobacz [Dostęp delegowany na pulpicie wirtualnym systemu Windows](delegated-access-virtual-desktop.md).

Należy również podać użytkownikom następujące informacje:

- Adres URL aplikacji
- Nazwy grupy dzierżawy poszczególnych dzierżawców, do których mają dostęp.

## <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego

Po zalogowaniu się na konto przy użyciu informacji otrzymanych od organizacji należy przygotować numer UPN dla użytkownika, dla którego chcesz wysyłać zapytania. Wyszukiwanie zapewni wszystkie działania w ramach określonego typu działania, które miały miejsce w ciągu ostatniego tygodnia.

### <a name="how-to-read-activity-search-results"></a>Jak czytać wyniki wyszukiwania aktywności

Działania są sortowane według sygnatury czasowej, a najpierw najnowsze działanie. Jeśli wyniki zwracają błąd, najpierw sprawdź, czy jest to błąd usługi. W przypadku błędów usługi utwórz bilet pomocy technicznej z informacjami o działaniu, aby pomóc nam debugować problem. Wszystkie inne typy błędów mogą być zwykle rozwiązane przez użytkownika lub administratora. Aby uzyskać listę najczęstszych scenariuszy błędów i sposobów ich rozwiązywania, zobacz [Identyfikowanie i diagnozowanie problemów](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Błędy usługi są nazywane "błędami zewnętrznymi" w połączonej dokumentacji. Zostanie to zmienione podczas aktualizowania odwołania programu PowerShell.

Działania połączenia może mieć więcej niż jeden błąd. Można rozwinąć typ działania, aby zobaczyć inne błędy, które użytkownik natknęli. Wybierz nazwę kodu błędu, aby otworzyć okno dialogowe, aby wyświetlić więcej informacji na jego temat.

### <a name="investigate-the-session-host"></a>Badanie hosta sesji 

W wynikach wyszukiwania znajdź i wybierz hosta sesji, o którego chcesz uzyskać informacje.

Można analizować kondycję hosta sesji:

- Na podstawie wstępnie zdefiniowanego progu można pobrać informacje o kondycji hosta sesji, które są kwerendami usługi Log Analytics.
- Jeśli nie ma żadnej aktywności lub host sesji nie jest połączony z usługą Log Analytics, informacje nie będą dostępne.

Można również wchodzić w interakcje z użytkownikami na hoście sesji:

- Możesz się wylogować lub wysłać wiadomość do zalogowanym użytkownikom.
- Użytkownik, którego pierwotnie wyszukiwano jest zaznaczone domyślnie, ale można również wybrać dodatkowych użytkowników do wysyłania wiadomości lub wylogować wielu użytkowników naraz.

### <a name="windows-performance-counter-thresholds"></a>Progi liczników wydajności systemu Windows

- Logiczny\*dysk(\\) %wolne miejsce:

    - Wyświetla procent całkowitego miejsca użytkowego na wolnym dysku logicznym.
    - Próg: Mniej niż 20% jest oznaczony jako niezdrowy.

- Logiczny(C:)\\Średnia długość kolejki dysku:

    - Reprezentuje warunki systemu magazynu.
    - Próg: Wyższy niż 5 jest oznaczony jako niezdrowy.

- Pamięć(\*\\) Dostępne bajty:

    - Dostępna pamięć dla systemu.
    - Próg: mniej niż 500 megabajtów oznaczonych jako w złej kondycji.

- Informacje o\*\\procesorze( ) Czas procesora:

    - Próg: Wyższy niż 80% jest oznaczony jako niezdrowy.

- [Opóźnienie wejścia użytkownika\*na\\sesję( ) Maksymalne opóźnienie wejścia:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Próg: Wyższy niż 2000 ms jest oznaczony jako w złej kondycji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak monitorować dzienniki aktywności w [aplikacji Użyj diagnostyki za pomocą usługi Log Analytics.](diagnostics-log-analytics.md)
- Przeczytaj o typowych scenariuszach błędów i o tym, jak je rozwiązać, aby znaleźć informacje o [identyfikowaniu i diagnozowaniu problemów](diagnostics-role-service.md).
