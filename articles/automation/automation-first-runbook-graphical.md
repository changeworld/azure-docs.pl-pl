---
title: Mój pierwszy graficzny element Runbook w Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego graficznego elementu Runbook.
keywords: runbook, szablon runbook, automatyzacja runbook, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: b891c8a7bbb33e3a3f18adbbc723d4bc9aa99a3a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246458"
---
# <a name="my-first-graphical-runbook"></a>Mój pierwszy graficzny element Runbook

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ten samouczek przeprowadzi Cię przez proces tworzenia [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation. Zacznij od prostego elementu Runbook, który testuje i publikuje, i uczenie się, jak śledzić stan zadania elementu Runbook. Następnie zmodyfikuj element Runbook, aby faktycznie zarządzać zasobami platformy Azure, w tym przypadku uruchamiając maszynę wirtualną platformy Azure. Ukończ samouczek, aby zwiększyć niezawodność elementu Runbook poprzez dodanie parametrów elementu Runbook i łączy warunkowych.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna ona być produkcyjną maszyną wirtualną.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Zacznij od utworzenia prostego elementu Runbook, który wyświetla tekst "Hello world".

1. W witrynie Azure Portal otwórz konto usługi Automation. 

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z tych elementów zawartości to moduły automatycznie dołączone do nowego konta usługi Automation. Należy również mieć zasób poświadczeń skojarzony z subskrypcją.
2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Utwórz nowy element Runbook, wybierając pozycję **Utwórz element Runbook**.
4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Graphical**.
5. W tym przypadku utworzysz [graficzny element Runbook](automation-graphical-authoring-intro.md). Wybierz pozycję **graficzny** dla **typu elementu Runbook**.<br> ![Nowy element Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor graficzny.

## <a name="step-2---add-activities"></a>Krok 2. Dodawanie działań

Kontrolka Biblioteka po lewej stronie edytora umożliwia wybranie działań do dodania do elementu Runbook. Teraz dodasz polecenie cmdlet **Write-Output**, aby element runbook wyprowadzał tekst.

1. W kontrolce Biblioteka kliknij pole wyszukiwania i wpisz **Write-Output**. Wyniki wyszukiwania są wyświetlane na poniższej ilustracji. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Przewiń listę w dół. Kliknij prawym przyciskiem myszy pozycję **Write-Output** i wybierz pozycję **Dodaj do kanwy**. Alternatywnie możesz kliknąć przycisk wielokropka (...) obok nazwy polecenia cmdlet, a następnie wybrać pozycję **Dodaj do kanwy**.
1. Kliknij pozycję **Write-Output** na kanwie. Ta akcja powoduje otwarcie strony kontrola konfiguracji, która umożliwia skonfigurowanie działania.
1. Nazwa pola **etykieta** jest domyślnie ustawiona na nazwę polecenia cmdlet, ale można ją zmienić na bardziej przyjazną. Zmień nazwę na **Zapisz ciąg Witaj, świecie w danych wyjściowych**.
1. Kliknij pozycję **Parametry**, aby podać wartości parametrów polecenia cmdlet.

   Niektóre polecenia cmdlet mają wiele zestawów parametrów i należy wybrać, który z nich ma być używany. W takim przypadku polecenie **Write-Output** ma tylko jeden zestaw parametrów.

1. Wybierz parametr *InputObject*. Jest to parametr używany do określania tekstu do wysłania do strumienia wyjściowego.
1. Menu rozwijane **Źródło danych** zawiera źródła, których można użyć do wypełniania wartości parametru. W tym menu wybierz pozycję **wyrażenie programu PowerShell**. 

   Możesz użyć danych wyjściowych z takich źródeł jako innego działania, zasobu usługi Automation lub wyrażenia programu PowerShell. W tym przypadku dane wyjściowe to po prostu tekst **Witaj, świecie**. Możesz użyć wyrażenia programu PowerShell i wprowadzić ciąg.<br>

1. W polu **wyrażenie** wpisz **Hello World** a następnie kliknij dwukrotnie przycisk **OK** , aby powrócić do kanwy.
1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu Runbook w celu udostępnienia go w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook uruchamia jego wersję roboczą i umożliwia interakcyjne wyświetlanie danych wyjściowych.

1. Wybierz **okienko testowania** , aby otworzyć okienko testowania.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Należy pamiętać, że [zadanie elementu Runbook](automation-runbook-execution.md) jest tworzone i jego stan jest wyświetlany w okienku.

   Zadanie jest uruchamiane jako **kolejkowane**, wskazując, że zadanie oczekuje na dostępność procesu roboczego elementu Runbook w chmurze. Stan zmieni się na **rozpoczęty** , gdy proces roboczy pozyskuje zadanie. Na koniec stan zostanie **uruchomiony** , gdy element Runbook faktycznie zacznie działać.

1. Po zakończeniu zadania elementu Runbook na stronie testowej są wyświetlane dane wyjściowe. W tym przypadku zostanie wyświetlony tekst **Witaj, świecie**.<br> ![Witaj, świecie](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony element Runbook jest nadal w trybie wersji roboczej. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Wybierz pozycję **Publikuj** , aby opublikować element Runbook, a następnie przycisk **tak** po wyświetleniu monitu.
1. Przewiń w lewo, aby wyświetlić element Runbook na stronie elementy Runbook, i pamiętaj, że wartość **Stan tworzenia** jest ustawiona na **Publikowanie**.
1. Przewiń z powrotem w prawo, aby wyświetlić stronę **MyFirstRunbook-Graphic**.

   Opcje w górnej części umożliwiają rozpoczęcie elementu Runbook, zaplanowanie przyszłego czasu rozpoczęcia lub utworzenie [elementu webhook](automation-webhooks.md) , aby można było uruchomić element Runbook za pośrednictwem wywołania http.

1. Wybierz przycisk **Start** , a następnie pozycję **tak** po wyświetleniu monitu o uruchomienie elementu Runbook.
1. Zostanie otwarte okienko zadań dla zadania elementu Runbook, które zostało utworzone. Sprawdź, czy pole **stan zadania** zawiera wartość **ukończono**.
1. Kliknij pozycję **dane wyjściowe** , aby otworzyć stronę wyjściową, na której można zobaczyć **Hello World** wyświetlone.
1. Zamknij stronę wyjściową.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. W strumieniu danych wyjściowych powinien być widoczny tylko **Hello World** . 

    Należy pamiętać, że w okienku strumienie mogą być wyświetlane inne strumienie zadania elementu Runbook, takie jak pełne strumienie i błędy, jeśli element Runbook zapisze w nich.
1. Zamknij okienko strumieni i okienko zadania, aby powrócić do strony **MyFirstRunbook-Graphic** .
1. Aby wyświetlić wszystkie zadania dla elementu Runbook, wybierz pozycję **zadania** w obszarze **zasoby**. Na stronie zadania są wyświetlane wszystkie zadania utworzone przez element Runbook. Powinno zostać wyświetlone tylko jedno zadanie, ponieważ zostało uruchomione tylko raz.
1. Kliknij nazwę zadania, aby otworzyć okienko zadania, które zostało wyświetlone po uruchomieniu elementu Runbook. To okienko służy do wyświetlania szczegółów wszystkich zadań utworzonych dla elementu Runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5. Tworzenie zmiennych elementów zawartości

Element Runbook został przetestowany i opublikowany, ale dotąd nie jest to przydatne do zarządzania zasobami platformy Azure. Przed skonfigurowaniem elementu Runbook do uwierzytelniania należy utworzyć zmienną do przechowywania identyfikatora subskrypcji, skonfigurować działanie do uwierzytelniania, a następnie odwołać się do zmiennej. Dołączenie odwołania do kontekstu subskrypcji pozwala łatwo współpracować z wieloma subskrypcjami.

1. Skopiuj identyfikator subskrypcji z opcji **subskrypcje** w okienku nawigacji.
1. Na stronie konta usługi Automation wybierz **zmienne** w obszarze **zasoby udostępnione**.
1. Wybierz pozycję **Dodaj zmienną**.
1. Na stronie Nowa zmienna wprowadź następujące ustawienia w udostępnionych polach.

    * **Nazwa** — wprowadź **AzureSubscriptionId**.
    * **Wartość** — wprowadź swój identyfikator subskrypcji. 
    * **Typ** — Zachowaj wybraną ciąg.
    * **Szyfrowanie** — Użyj wartości domyślnej.
1. Kliknij pozycję **Utwórz**, aby utworzyć zmienną.

## <a name="step-6---add-authentication"></a>Krok 6. Dodawanie uwierzytelniania

Teraz, gdy masz zmienną do przechowywania identyfikatora subskrypcji, możesz skonfigurować element Runbook do uwierzytelniania przy użyciu poświadczeń Uruchom jako dla subskrypcji. W tym celu Dodaj jako element zawartości połączenie Uruchom jako platformy Azure. Należy również dodać polecenie cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) i polecenie cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) do kanwy.

>[!NOTE]
>W przypadku elementów Runbook programu PowerShell polecenia **Add-AzAccount** i **Add-AzureRMAccount** są aliasami dla polecenia **Connect-AzAccount**. Należy zauważyć, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może korzystać tylko **z programu Connect-AzAccount** .

1. Przejdź do elementu Runbook i wybierz pozycję **Edytuj** na stronie **MyFirstRunbook-graficzna** .
1. Nie potrzebujesz więcej niż **Hello World zapisu do danych wyjściowych** . Po prostu kliknij wielokropek i wybierz pozycję **Usuń**.
1. W kontrolce Biblioteka rozwiń węzeł **zasoby**, a następnie pozycję **połączenia**. Dodaj **AzureRunAsConnection** do kanwy, wybierając pozycję **Dodaj do kanwy**.
1. W kontrolce Biblioteka wpisz **ciąg Connect-AzAccount** w polu wyszukiwania.
1. Dodaj do kanwy **Connect-AzAccount** .
1. Umieść kursor nad pozycją **Uzyskaj połączenie Uruchom jako** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę, aby **połączyć się z AzAccount** w celu utworzenia łącza. Element Runbook rozpoczyna się od **pobrania połączenia Uruchom jako** , a następnie uruchamia polecenie **Connect-AzAccount**.<br> ![Tworzenie połączenia między działaniami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na kanwie wybierz pozycję **Connect-AzAccount**. W okienku kontrola konfiguracji wpisz **Logowanie do platformy Azure** w polu **etykieta** .
1. Kliknij pozycję **Parametry**, a zostanie wyświetlona strona Konfiguracja parametru działania.
1. Polecenie cmdlet **Connect-AzAccount** ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij pozycję **Zestaw parametrów**, a następnie wybierz zestaw parametrów **ServicePrincipalCertificate**.
1. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij pozycję **APPLICATIONID**.<br> ![dodać parametry konta platformy Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na stronie wartość parametru wprowadź następujące ustawienia, a następnie kliknij przycisk **OK**.

   * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**.
   * Lista źródeł danych — wybierz pozycję **Pobierz połączenie Uruchom jako**.
   * **Ścieżka pola** — wpisz identyfikator **aplikacji**. Określasz nazwę właściwości dla ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami.
1. Kliknij pozycję **CERTIFICATETHUMBPRINT**i na stronie wartość parametru wprowadź następujące ustawienia, a następnie kliknij przycisk **OK**.

    * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**.
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie Uruchom jako**.
    * **Ścieżka pola** — wpisz **CertificateThumbprint**.
1. Kliknij pozycję **serviceprincipal**i na stronie wartość parametru wybierz pozycję **ConstantValue** dla pola **Źródło danych** . Kliknij opcję **prawda**; a następnie kliknij przycisk **OK**.
1. Kliknij pozycję **TENANTID**i wprowadź następujące ustawienia na stronie wartości parametru. Po zakończeniu kliknij przycisk **OK** dwa razy.

    * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**. 
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie Uruchom jako**.
    * **Ścieżka pola** — wpisz **TenantId**. 
1. W kontrolce Biblioteka wpisz **ciąg Set-AzContext** w polu wyszukiwania.
1. Dodaj do kanwy **Set-AzContext** .
1. Na kanwie wybierz pozycję **Set-AzContext** . W okienku kontrola konfiguracji wprowadź w polu **etykieta** pozycję **Określ Identyfikator subskrypcji** .
1. Kliknij przycisk **Parametry** i zostanie wyświetlona strona Konfiguracja parametru działania.
1. Polecenie cmdlet **Set-AzContext** ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij pozycję **Zestaw parametrów**, a następnie wybierz zestaw parametrów **SubscriptionId**.
1. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij pozycję Identyfikator **subskrypcji**.
1. Na stronie wartość parametru wybierz opcję **zasób zmiennej** dla pola **Źródło danych** i wybierz pozycję **AzureSubscriptionId** z listy źródło. Po zakończeniu kliknij przycisk **OK** dwa razy.
1. Umieść kursor nad pozycją **Zaloguj się do platformy Azure** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji **Określ identyfikator subskrypcji**. 

Element Runbook powinien na tym etapie wyglądać następująco: <br>![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7. Dodawanie działania w celu uruchomienia maszyny wirtualnej

Teraz należy dodać działanie **Start-AzVM** , aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz zakodowana jej nazwę w poleceniu cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. W kontrolce Biblioteka wpisz polecenie **Start-AZ** w polu wyszukiwania.
2. Dodaj polecenie **Start-AzVM** do kanwy, a następnie kliknij i przeciągnij je poniżej **Określ Identyfikator subskrypcji**.
1. Umieść kursor nad pozycją **Określ identyfikator subskrypcji** i zaczekaj, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do **menu Start-AzVM**.
1. Wybierz pozycję **Start-AzVM**. Kliknij opcję **Parametry** , a następnie pozycję **zestaw parametrów** , aby wyświetlić zestawy dla działania.
1. Wybierz zestaw parametrów **ResourceGroupNameParameterSetName**. Pola **ResourceGroupName** i **name** mają wykrzykniki obok nich, aby wskazać, że są one wymaganymi parametrami. Należy zauważyć, że oba pola oczekują wartości ciągu.
1. Wybierz pozycję **Nazwa**. Wybierz **wyrażenie programu PowerShell** dla pola **Źródło danych** . W przypadku maszyny wirtualnej, która jest używana do uruchamiania tego elementu Runbook, wpisz nazwę maszyny ujętą w podwójne cudzysłowy. Kliknij przycisk **OK**.
1. Wybierz pozycję **ResourceGroupName**. Użyj **wyrażenia programu PowerShell** wartości dla pola **Źródło danych** i wpisz nazwę grupy zasobów ujętej w podwójne cudzysłowy. Kliknij przycisk **OK**.
1. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.
1. Kliknij przycisk **Rozpocznij** , aby rozpocząć test. Po zakończeniu upewnij się, że maszyna wirtualna została uruchomiona. 

Element Runbook powinien na tym etapie wyglądać następująco: <br>![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8. Dodawanie dodatkowych parametrów wejściowych

Element Runbook aktualnie uruchamia maszynę wirtualną w grupie zasobów określonej dla polecenia cmdlet **Start-AzVM** . Element Runbook będzie bardziej użyteczny, jeśli podczas uruchamiania elementu Runbook określisz nazwę i grupę zasobów. Dodajmy do elementu Runbook parametry wejściowe w celu zapewnienia tej funkcjonalności.

1. Otwórz edytor graficzny, klikając pozycję **Edytuj** w okienku **MyFirstRunbook-Graphic (** Edycja).
1. Wybierz pozycję **dane wejściowe i wyjściowe** , a następnie **Dodaj dane wejściowe** , aby otworzyć okienko parametru wejściowego elementu Runbook.
1. Wprowadź następujące ustawienia w podanych polach, a następnie kliknij przycisk **OK**.
   * **Nazwa** — Określ **VMName**.
   * **Wpisz** --Zachowaj ustawienie ciągu.
   * **Obowiązkowe** — Zmień wartość na **tak**.
1. Utwórz drugi obowiązkowy parametr wejściowy o nazwie *ResourceGroupName* , a następnie kliknij przycisk **OK** , aby zamknąć okienko dane wejściowe i wyjściowe.<br> ![Parametry wejściowe elementu Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Wybierz działanie **Start-AzVM** , a następnie kliknij pozycję **Parametry**.
1. Zmień pole **źródła danych** **na** **dane wejściowe elementu Runbook**. Następnie wybierz pozycję **VMName**.
1. Zmień pole **źródła danych** dla **ResourceGroupName** na **dane wejściowe elementu Runbook** , a następnie wybierz pozycję **ResourceGroupName**.<br> ![parametry Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
1. Zamknij okienko testowania.
1. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
1. Zatrzymaj maszynę wirtualną, która została wcześniej uruchomiona.
1. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości dla **VMName** i **ResourceGroupName** dla maszyny wirtualnej, która ma zostać uruchomiona.
1. Po zakończeniu działania elementu Runbook upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-9---create-a-conditional-link"></a>Krok 9. Tworzenie połączenia warunkowego

Teraz można zmodyfikować element Runbook, aby próbował uruchomić maszynę wirtualną, jeśli nie została jeszcze uruchomiona. W tym celu dodaj polecenie cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) , które pobiera stan wystąpienia maszyny wirtualnej. Następnie można dodać moduł kodu przepływu pracy programu PowerShell o nazwie **Get status** z fragmentem kodu programu PowerShell, aby określić, czy stan maszyny wirtualnej jest uruchomiony lub zatrzymany. W przypadku połączenia warunkowego z modułu **Get status** jest uruchamiany tylko **Start-AzVM** , jeśli bieżący stan uruchomienia jest zatrzymany. Na końcu tej procedury element Runbook używa polecenia cmdlet **Write-Output** do wyprowadzania komunikatu, aby poinformować użytkownika o tym, czy maszyna wirtualna została pomyślnie uruchomiona.

1. Otwórz **MyFirstRunbook-graficznej** w edytorze graficznym.
1. Usuń link między **Określ Identyfikator subskrypcji** i poleceniem **Start-AzVM** , klikając go, a następnie naciskając klawisz **delete**.
1. W kontrolce Biblioteka wpisz **ciąg Get-AZ** w polu wyszukiwania.
1. Dodaj do kanwy **Get-AzVM** .
1. Wybierz polecenie **Get-AzVM** , a następnie **Ustaw parametr set** , aby wyświetlić zestawy dla tego polecenia cmdlet. 
1. Wybierz zestaw parametrów **GetVirtualMachineInResourceGroupNameParamSet**. Pola **ResourceGroupName** i **name** mają wykrzykniki obok nich, wskazując, że określają wymagane parametry. Należy zauważyć, że oba pola oczekują wartości ciągu.
1. W obszarze **Źródło danych** dla pozycji **Nazwa**wybierz pozycję **dane wejściowe elementu Runbook**, a następnie **VMName**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla **ResourceGroupName**wybierz pozycję **dane wejściowe elementu Runbook**, a następnie **ResourceGroupName**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla pozycji **stan**wybierz opcję **stała wartość**, a następnie **wartość true**. Kliknij przycisk **OK**.
1. Utwórz łącze z **Określ Identyfikator subskrypcji** do **Get-AzVM**.
1. W kontrolce Biblioteka rozwiń pozycję **Kontrola elementu Runbook** i Dodaj **kod** do kanwy.  
1. Utwórz łącze z elementu **Get-AzVM** do **kodu**.  
1. Kliknij pozycję **kod** , a następnie w okienku Konfiguracja Zmień etykietę, aby **uzyskać stan**.
1. Wybierz opcję **kod** i zostanie wyświetlona strona Edytor kodu.  
1. Wklej poniższy fragment kodu do strony edytora.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Utwórz link ze **stanu Get** na **Start-AzVM**.<br> ![Element Runbook z modułem kodu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Wybierz łącze i w okienku Konfiguracja Zmień **warunek Zastosuj** na **wartość tak**. Należy pamiętać, że łącze stanie się linią kreskowaną, co oznacza, że działanie docelowe jest uruchamiane tylko wtedy, gdy warunek zostanie spełniony.  
1. Dla **wyrażenia warunku**wpisz `$ActivityOutput['Get Status'] -eq "Stopped"`. **Start-AzVM** jest teraz uruchamiany tylko wtedy, gdy maszyna wirtualna jest zatrzymana.
1. W kontrolce Biblioteka rozwiń pozycję **Polecenia cmdlet**, a następnie pozycję **Microsoft.PowerShell.Utility**.
1. Dwukrotnie dodaj pozycję **Write-Output** do kanwy.
1. W pierwszej kontrolce **Write-Output** kliknij pozycję **Parametry** , a następnie zmień wartość **etykieta** na **powiadamianie o uruchomieniu maszyny wirtualnej**.
1. Dla elementu **inputobject**Zmień **Źródło danych** na **wyrażenie programu PowerShell**i wpisz wyrażenie **$VMName powiodło się.**
1. W drugiej kontrolce **Write-Output** kliknij pozycję **Parametry** i zmień wartość **etykiety** , aby **powiadomienie nie powiodło się**.
1. Dla elementu **inputobject**Zmień **Źródło danych** na **wyrażenie programu PowerShell**i wpisz wyrażenie $VMName nie **można uruchomić wyrażenia.** .
1. Utwórz linki z **menu Start-AzVM** w celu **powiadomienia o uruchomieniu maszyny wirtualnej** i **Powiadom o uruchomieniu maszyny wirtualnej**.
1. Wybierz łącze do **powiadomienia o uruchomieniu maszyny wirtualnej** i Zmień **warunek zastosowania** na wartość true.
1. Dla **wyrażenia warunku**wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Ta kontrolka **Write-Output** będzie teraz uruchamiana tylko wtedy, gdy maszyna wirtualna zostanie uruchomiona pomyślnie.
1. Wybierz łącze do **powiadomienia o nieudanym uruchomieniu maszyny wirtualnej** i Zmień **warunek zastosowania** na wartość true.
1. Dla pola **wyrażenie warunku** wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Ta kontrolka **Write-Output** będzie teraz uruchamiana tylko wtedy, gdy maszyna wirtualna nie została pomyślnie uruchomiona. Element Runbook powinien wyglądać jak na poniższej ilustracji: <br> ![Element Runbook z pozycją Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania.
1. Uruchom element Runbook z zatrzymaną maszyną wirtualną i uruchom maszynę.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu graficznym, zobacz [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz [mój pierwszy element Runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md).