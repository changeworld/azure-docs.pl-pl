---
title: Wprowadzenie do Azure Log Integration | Microsoft Docs
description: Dowiedz się, jak zainstalować usługę Azure Log Integration i zintegrować dzienniki z usługi Azure Storage, dzienników inspekcji platformy Azure i alertów Azure Security Center.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 861a72b6abdc3d06cc4737630c92d34eee2e8545
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727685"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration z rejestrowaniem Diagnostyka Azure i przekazywaniem zdarzeń systemu Windows


>[!IMPORTANT]
> Funkcja integracja z usługą Azure log zostanie zaniechana przez 06/15/2019. Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dzienników platformy Azure powinna być używana tylko wtedy, gdy łącznik [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-get-started) nie jest dostępny z poziomu dostawcy zdarzeń zabezpieczeń i zarządzania zdarzeniami (Siem).

Azure Log Integration udostępnienie dzienników platformy Azure SIEM, aby można było utworzyć ujednolicony pulpit nawigacyjny zabezpieczeń dla wszystkich zasobów.
Aby uzyskać więcej informacji o stanie łącznika Azure Monitor, skontaktuj się z dostawcą SIEM.

> [!IMPORTANT]
> Jeśli podstawowe zainteresowanie zbiera dzienniki maszyn wirtualnych, większość dostawców SIEM obejmuje tę opcję w rozwiązaniu. Użycie łącznika dostawcy SIEM jest zawsze preferowaną alternatywą.

Ten artykuł pomoże Ci rozpocząć pracę z Azure Log Integration. Koncentruje się na instalowaniu usługi Azure Log Integration i integracji usługi z programem Diagnostyka Azure. Usługa Azure Log Integration następnie zbiera informacje dziennika zdarzeń systemu Windows z kanału zdarzeń zabezpieczeń systemu Windows z maszyn wirtualnych wdrożonych w infrastrukturze platformy Azure jako usługa. Jest to podobne do *przekazywania zdarzeń* , które może być używane w systemie lokalnym.

> [!NOTE]
> Integrowanie danych wyjściowych Azure Log Integration z SIEM jest wykonywane przez sam SIEM. Aby uzyskać więcej informacji, zobacz [integrowanie Azure log Integration z lokalnym Siem](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Usługa Azure Log Integration działa na fizycznym lub wirtualnym komputerze z systemem Windows Server 2008 R2 lub nowszym (preferowany jest system Windows Server 2016 lub Windows Server 2012 R2).

Komputer fizyczny może działać lokalnie lub w lokacji hostingu. Jeśli zdecydujesz się na uruchomienie usługi Azure Log Integration na maszynie wirtualnej, maszyna wirtualna może znajdować się lokalnie lub w chmurze publicznej, np. w Microsoft Azure.

Maszyna fizyczna lub wirtualna z uruchomioną usługą Azure Log Integration wymaga łączności sieciowej z chmurą publiczną platformy Azure. Ten artykuł zawiera szczegółowe informacje o wymaganej konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Zainstalowanie Azure Log Integration wymaga co najmniej następujących elementów:

* **Subskrypcji platformy Azure**. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* **Konto magazynu** , które może być używane na potrzeby rejestrowania w systemie Windows Diagnostyka Azure (funkcji wad). Możesz użyć wstępnie skonfigurowanego konta magazynu lub utworzyć nowe konto magazynu. W dalszej części tego artykułu opisano sposób konfigurowania konta magazynu.

  > [!NOTE]
  > W zależności od danego scenariusza konto magazynu może nie być wymagane. W przypadku scenariusza Diagnostyka Azure omówionego w tym artykule wymagane jest konto magazynu.

* **Dwa systemy**: 
  * Maszyna, na której działa usługa Azure Log Integration. Ta maszyna zbiera wszystkie informacje dziennika, które później zostały zaimportowane do SIEM. Ten system:
    * Może być lokalna lub hostowana w Microsoft Azure.  
    * Musi mieć uruchomioną wersję x64 systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszą i mieć zainstalowane Microsoft .NET 4.5.1. Aby określić zainstalowaną wersję programu .NET, zobacz [Określanie, które wersje .NET Framework są zainstalowane](https://msdn.microsoft.com/library/hh925568).  
    * Musi mieć połączenie z kontem usługi Azure Storage, które jest używane do rejestrowania Diagnostyka Azure. W dalszej części tego artykułu opisano sposób potwierdzania łączności.
  * Komputer, który chcesz monitorować. Jest to maszyna wirtualna działająca jako [maszyna wirtualna platformy Azure](/azure/virtual-machines/virtual-machines-windows-overview). Informacje o rejestrowaniu z tej maszyny są wysyłane do maszyny usługi Azure Log Integration.

Aby uzyskać szybką demonstrację sposobu tworzenia maszyny wirtualnej przy użyciu Azure Portal, zapoznaj się z następującym wideo:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Podczas testowania można użyć dowolnego systemu spełniającego minimalne wymagania dotyczące systemu operacyjnego. W środowisku produkcyjnym obciążenie może wymagać zaplanowania skalowania w górę lub w dół.

Można uruchomić wiele wystąpień usługi Azure Log Integration. Można jednak uruchomić tylko jedno wystąpienie usługi na maszynę fizyczną lub wirtualną. Ponadto można równoważyć obciążenie Diagnostyka Azure kont magazynu dla funkcji wad. Liczba subskrypcji do uwzględnienia w wystąpieniach zależy od pojemności.

> [!NOTE]
> Obecnie nie mamy specjalnych zaleceń dotyczących skalowania w poziomie wystąpień Azure Log Integration maszyn (czyli maszyn z uruchomioną usługą Azure Log Integration) lub kont magazynu lub subskrypcji. Podejmuj decyzje dotyczące skalowania na podstawie obserwacji wydajności w każdym z tych obszarów.

Aby pomóc w ulepszaniu wydajności, można także skalować w górę usługę Azure Log Integration. Następujące metryki wydajności mogą pomóc w zmianie rozmiaru wybranych maszyn w celu uruchomienia usługi Azure Log Integration:

* Na maszynie 8-procesorowej (rdzeń) pojedyncze wystąpienie Azure Log Integration może przetwarzać około 24 000 000 zdarzeń dziennie (około 1 000 000 zdarzeń na godzinę).
* Na maszynie z 4 procesorami (rdzeń) pojedyncze wystąpienie Azure Log Integration może przetwarzać około 1 500 000 zdarzeń dziennie (około 62 500 zdarzeń na godzinę).

## <a name="install-azure-log-integration"></a>Zainstaluj Azure Log Integration

Uruchom za pomocą procedury konfigurowania. Zdecyduj, czy chcesz podawać informacje telemetryczne do firmy Microsoft.

Usługa Azure Log Integration zbiera dane telemetryczne z komputera, na którym jest zainstalowana.  

Zebrane dane telemetryczne obejmują następujące elementy:

* Wyjątki występujące podczas wykonywania Azure Log Integration.
* Metryki dotyczące liczby przetworzonych zapytań i zdarzeń.
* Statystyki dotyczące opcji wiersza polecenia Azlog. exe. 

> [!NOTE]
> Zalecamy, aby firma Microsoft mogła zbierać dane telemetryczne. Zbieranie danych telemetrycznych można wyłączyć, usuwając zaznaczenie pola wyboru **Zezwalaj firmie Microsoft na zbieranie danych** telemetrycznych.
>

![Zrzut ekranu okienka instalacji z zaznaczonym polem wyboru Telemetria](./media/azure-log-integration-get-started/telemetry.png)


Proces instalacji jest objęty następującym wideo:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Kroki po instalacji i walidacji

Po zakończeniu instalacji podstawowej możesz wykonać czynności po instalacji i weryfikacji:

1. Otwórz program PowerShell jako administrator. Następnie przejdź do folderu C:\Program Files\Microsoft Azure Log Integration.
2. Zaimportuj polecenia cmdlet Azure Log Integration. Aby zaimportować polecenia cmdlet, uruchom skrypt `LoadAzlogModule.ps1`. Wprowadź `.\LoadAzlogModule.ps1`wartość, a następnie naciśnij klawisz ENTER (Zwróć uwagę na użycie **.\\**  w tym poleceniu). Zobaczysz coś, co jest wyświetlane na poniższej ilustracji:

   ![Zrzut ekranu przedstawiający dane wyjściowe polecenia LoadAzlogModule. ps1](./media/azure-log-integration-get-started/loaded-modules.png)
3. Następnie skonfiguruj Azure Log Integration, aby używać określonego środowiska platformy Azure. *Środowisko platformy Azure* to typ centrum danych w chmurze platformy Azure, z którym chcesz współpracować. Chociaż istnieje kilka środowisk platformy Azure, obecnie odpowiednie opcje to **AzureCloud** lub **AzureUSGovernment**. W przypadku uruchamiania programu PowerShell jako administrator upewnij się, że jesteś w folderze C:\Program Files\Microsoft Azure log Integration\. Następnie uruchom następujące polecenie:

   `Set-AzlogAzureEnvironment -Name AzureCloud`(dla **AzureCloud**)
  
   Jeśli chcesz korzystać z chmury platformy Azure dla instytucji rządowych USA, użyj **AzureUSGovernment** dla zmiennej **-name** . Obecnie inne chmury platformy Azure nie są obsługiwane.  

   > [!NOTE]
   > Nie otrzymujesz opinii, gdy polecenie zakończy się pomyślnie. 

4. Aby można było monitorować system, potrzebna jest nazwa konta magazynu używanego do Diagnostyka Azure. W Azure Portal przejdź do pozycji **maszyny wirtualne**. Wyszukaj maszynę wirtualną z systemem Windows, która będzie monitorowana. W sekcji **Właściwości** wybierz pozycję **Ustawienia diagnostyczne**.  Następnie wybierz pozycję **Agent**. Zanotuj podaną nazwę konta magazynu. Ta nazwa konta będzie potrzebna w późniejszym kroku.

   ![Zrzut ekranu przedstawiający okienko ustawień Diagnostyka Azure](./media/azure-log-integration-get-started/storage-account-large.png) 

   ![Zrzut ekranu przedstawiający przycisk Włącz monitorowanie na poziomie gościa](./media/azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Jeśli nie włączono monitorowania podczas tworzenia maszyny wirtualnej, możesz włączyć ją jak pokazano na powyższym obrazie.

5. Teraz wróć do maszyny Azure Log Integration. Sprawdź, czy masz połączenie z kontem magazynu z systemu, w którym zainstalowano Azure Log Integration. Komputer z uruchomioną usługą Azure Log Integration musi mieć dostęp do konta magazynu w celu pobrania informacji rejestrowanych przez Diagnostyka Azure w każdym z monitorowanych systemów. Aby sprawdzić łączność: 
   1. [Pobierz Eksplorator usługi Azure Storage](https://storageexplorer.com/).
   2. Ukończ instalację.
   3. Po zakończeniu instalacji wybierz pozycję **dalej**. Pozostaw zaznaczone pole wyboru **Eksplorator usługi Microsoft Azure Storage uruchamiania** .  
   4. Zaloguj się do platformy Azure.
   5. Sprawdź, czy konto magazynu skonfigurowane dla Diagnostyka Azure jest widoczne: 

   ![Zrzut ekranu kont magazynu w Eksplorator usługi Storage](./media/azure-log-integration-get-started/storage-explorer.png)

   1. W obszarze konta magazynu są wyświetlane pewne opcje. W obszarze **tabele**powinna zostać wyświetlona tabela o nazwie **WADWindowsEventLogsTable**.

   Jeśli podczas tworzenia maszyny wirtualnej nie włączono monitorowania, można je włączyć zgodnie z wcześniejszym opisem.


## <a name="integrate-windows-vm-logs"></a>Integrowanie dzienników maszyn wirtualnych z systemem Windows

W tym kroku skonfigurujesz maszynę z uruchomioną usługą Azure Log Integration, aby nawiązać połączenie z kontem magazynu zawierającym pliki dziennika.

Aby ukończyć ten krok, potrzebne są następujące elementy:  
* **FriendlyNameForSource**: Przyjazna nazwa, którą można zastosować do konta magazynu skonfigurowanego dla maszyny wirtualnej w celu przechowywania informacji z Diagnostyka Azure.
* **StorageAccountName**: Nazwa konta magazynu określonego podczas konfigurowania Diagnostyka Azure.  
* **StorageKey**: Klucz magazynu dla konta magazynu, w którym są przechowywane Diagnostyka Azure informacje dla tej maszyny wirtualnej.  

Aby uzyskać klucz magazynu, wykonaj następujące czynności:
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji wybierz pozycję **wszystkie usługi**.
3. W polu **Filtr** wprowadź wartość **Storage**. Następnie wybierz pozycję **konta magazynu**.

   ![Zrzut ekranu przedstawiający konta magazynu w ramach wszystkich usług](./media/azure-log-integration-get-started/filter.png)

4. Zostanie wyświetlona lista kont magazynu. Kliknij dwukrotnie konto przypisane do magazynu dzienników.

   ![Zrzut ekranu przedstawiający listę kont magazynu](./media/azure-log-integration-get-started/storage-accounts.png)

5. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.

   ![Zrzut ekranu pokazujący opcję klucze dostępu w menu](./media/azure-log-integration-get-started/storage-account-access-keys.png)

6. Skopiuj **Klucz1**, a następnie zapisz go w bezpiecznej lokalizacji, do której można uzyskać dostęp w ramach poniższego kroku.
7. Na serwerze, na którym zainstalowano Azure Log Integration, Otwórz okno wiersza polecenia jako administrator. (Pamiętaj, aby otworzyć okno wiersza polecenia jako administrator, a nie program PowerShell).
8. Przejdź do folderu C:\Program Files\Microsoft Azure Log Integration.
9. Uruchom następujące polecenie: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Przykład:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Jeśli chcesz, aby identyfikator subskrypcji był wyświetlany w kodzie XML zdarzenia, Dołącz Identyfikator subskrypcji do przyjaznej nazwy:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Przykład:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Zaczekaj do 60 minut, a następnie Wyświetl zdarzenia, które są pobierane z konta magazynu. Aby wyświetlić zdarzenia, w Azure log Integration wybierz pozycję **Podgląd zdarzeń** >  > **Dzienniki systemu Windows** **przekazane zdarzenia**.

Poniższy film wideo obejmuje poprzednie kroki:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Jeśli dane nie są wyświetlane w folderze zdarzenia przekazane
Jeśli dane nie są wyświetlane w folderze zdarzenia przekazane po godzinie, wykonaj następujące czynności:

1. Sprawdź maszynę, na której działa usługa Azure Log Integration. Potwierdź, że może on uzyskać dostęp do platformy Azure. Aby przetestować łączność, w przeglądarce spróbuj przejść do [Azure Portal](https://portal.azure.com).
2. Upewnij się, że konto użytkownika Azlog ma uprawnienie do zapisu dla folderu users\Azlog.
   1. Otwórz Eksploratora plików.
   2. Przejdź do C:\Users.
   3. Kliknij prawym przyciskiem myszy C:\users\Azlog.
   4. Wybierz pozycję **zabezpieczenia**.
   5. Wybierz pozycję **NT Service\Azlog**. Sprawdź uprawnienia dla konta. Jeśli na tej karcie nie ma konta lub nie są wyświetlane odpowiednie uprawnienia, możesz udzielić uprawnień konta na tej karcie.
3. Po uruchomieniu polecenia `Azlog source list`upewnij się, że konto magazynu dodane w poleceniu `Azlog source add` jest wymienione w danych wyjściowych.
4. Aby sprawdzić, czy występują błędy zgłoszone przez usługę Azure log Integration, przejdź do pozycji **Podgląd zdarzeń** > **Windows Logs** > **aplikacja**.

W przypadku napotkania problemów podczas instalacji i konfiguracji można utworzyć [żądanie obsługi](../../azure-supportability/how-to-create-azure-support-request.md). Dla usługi wybierz pozycję **integracja dzienników**.

Kolejną opcją pomocy technicznej jest [Azure log Integration forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Na forum MSDN społeczność może zapewnić pomoc techniczną, udzielając odpowiedzi na pytania i porady dotyczące udostępniania oraz wskazówki dotyczące optymalnego korzystania z Azure Log Integration. Zespół Azure Log Integration również monitoruje ten forum. Mogą one ułatwić każdemu z nich.

## <a name="integrate-azure-activity-logs"></a>Integrowanie dzienników aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. Obejmuje to szereg danych, od Azure Resource Manager dane operacyjne do aktualizacji na zdarzeniach Service Health. Alerty Azure Security Center są również zawarte w tym dzienniku.
> [!NOTE]
> Przed podjęciem próby wykonania kroków opisanych w tym artykule należy zapoznać się z artykułem [wprowadzenie](azure-log-integration-get-started.md) i wykonać kroki w tym miejscu.

### <a name="steps-to-integrate-azure-activity-logs"></a>Kroki umożliwiające integrację dzienników aktywności platformy Azure

1. Otwórz wiersz polecenia i uruchom następujące polecenie:```cd c:\Program Files\Microsoft Azure Log Integration```
2. Uruchom następujące polecenie:```azlog createazureid```

    To polecenie umożliwia wyświetlanie danych logowania na platformie Azure. Następnie polecenie Azure Active Directory tworzy nazwę główną usługi w dzierżawach usługi Azure AD, która hostuje subskrypcje platformy Azure, w których zalogowany użytkownik jest administratorem, współadministratorem lub właścicielem. Polecenie zakończy się niepowodzeniem, Jeśli zalogowany użytkownik jest tylko użytkownikiem gościa w dzierżawie usługi Azure AD. Uwierzytelnianie na platformie Azure odbywa się za pomocą usługi Azure AD. Utworzenie nazwy głównej usługi dla Azure Log Integration powoduje utworzenie tożsamości usługi Azure AD mającej dostęp do odczytu z subskrypcji platformy Azure.
3. Uruchom następujące polecenie, aby autoryzować nazwę główną usługi Azure Log Integration utworzoną w poprzednim kroku dostęp do dziennika aktywności subskrypcji. Musisz być właścicielem subskrypcji, aby uruchomić polecenie.

   ```Azlog.exe authorize subscriptionId```Przyklad

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji Azure Active Directory są w nich tworzone:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje dotyczące umieszczania informacji w plikach JSON w systemie zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), skontaktuj się z dostawcą usługi SIEM.

Pomoc dla społeczności jest dostępna na [Forum Azure log Integration MSDN](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Na tym forum użytkownicy Azure Log Integration mogą obsługiwać siebie nawzajem przy użyciu pytań, odpowiedzi, porad i lew. Ponadto zespół Azure Log Integration monitoruje ten forum i pomaga w każdej sytuacji.

Możesz również otworzyć [żądanie pomocy technicznej](../../azure-supportability/how-to-create-azure-support-request.md). Wybierz pozycję integracja dzienników jako usługę, dla której chcesz uzyskać pomoc techniczną.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Log Integration, zobacz następujące artykuły: Przed podjęciem próby wykonania kroków opisanych w tym artykule należy zapoznać się z artykułem wprowadzenie i wykonać kroki w tym miejscu.

* [Wprowadzenie do Azure log Integration](azure-log-integration-overview.md). W tym artykule przedstawiono, jak Azure Log Integration, jej najważniejszych możliwości i jak działa.
* [Kroki konfiguracji partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ten wpis w blogu przedstawia sposób konfigurowania Azure Log Integration do pracy z rozwiązaniami partnerskimi Splunk, HP ArcSight i IBM QRadar. Opisuje nasze bieżące wskazówki dotyczące konfigurowania składników SIEM. Aby uzyskać dodatkowe informacje, skontaktuj się z dostawcą usługi SIEM.
* [Azure log Integration często zadawanych pytań](azure-log-integration-faq.md). Często zadawane pytania dotyczące Azure Log Integration.
* [Integracja Azure Security Center alertów z Azure log Integration](/azure/security-center/security-center-integrating-alerts-with-log-integration). W tym artykule przedstawiono sposób synchronizowania alertów Security Center i zdarzeń zabezpieczeń maszyn wirtualnych zbieranych przez Diagnostyka Azure i dzienniki aktywności platformy Azure. Dzienniki są synchronizowane przy użyciu dzienników Azure Monitor lub rozwiązania SIEM.
* [Nowe funkcje dla Diagnostyka Azure i dzienników inspekcji platformy Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ten wpis w blogu zawiera wprowadzenie do dzienników inspekcji platformy Azure i innych funkcji, które mogą pomóc Ci w uzyskaniu wglądu w operacje związane z zasobami platformy Azure.
