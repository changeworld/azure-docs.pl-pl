---
title: Wprowadzenie do integracji dziennika Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługę Azure dziennika integracji oraz integrowanie dzienniki z usługi Azure Storage, dzienników inspekcji platformy Azure i alerty Centrum zabezpieczeń Azure.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8ceffb666eb18ee7b087ad1e1dbc27b57388ee49
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641200"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integracja dziennika Azure z rejestrowania diagnostyki Azure i funkcji przekazywania zdarzeń systemu Windows

Integracja z dzienników Azure należy używać tylko, jeśli [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) łącznik nie jest dostępna z dostawcą zdarzenia zabezpieczeń i zarządzania zdarzenia (SIEM).

Integracja z usługą Azure dziennika udostępnia Azure dzienniki do rozwiązania SIEM, można utworzyć pulpitu nawigacyjnego ujednoliconego zabezpieczeń dla wszystkich zasobów.
Aby uzyskać więcej informacji o stanie łącznika usługi Azure monitora skontaktuj się z dostawcą SIEM.

> [!IMPORTANT]
> Większość dostawców rozwiązań SIEM zainteresowanie podstawowy jest zbierania dzienników maszyny wirtualnej, dołączyć tę opcję w ich rozwiązania. Przy użyciu SIEM dostawcy łącznik jest zawsze preferowanych alternatywnej.

Ten artykuł ułatwia rozpoczęcie pracy z integracją dziennika Azure. Go koncentruje się na instalowanie usługi integracji dziennika Azure i Integrowanie usługi z diagnostyki Azure. Usługa Azure dziennika integracji następnie zbiera informacje dziennika zdarzeń systemu Windows z kanału zdarzeń zabezpieczeń systemu Windows z maszyn wirtualnych wdrożonych w Azure infrastruktura jako usługa. Jest to podobne do *funkcji przekazywania zdarzeń* używanego w systemie lokalnym.

> [!NOTE]
> Integrowanie danych wyjściowych Azure dziennika integracji z rozwiązania SIEM odbywa się przez sam SIEM. Aby uzyskać więcej informacji, zobacz [integracji Azure dziennika integracji z lokalnymi system SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Usługa integracji dziennika Azure działa na fizycznego lub wirtualnego komputerze z systemem Windows Server 2008 R2 lub nowszy (z systemem Windows Server 2016 lub Windows Server 2012 R2 jest preferowana).

Komputer fizyczny można uruchomić lokalnie lub w witrynie hostingu. Jeśli chcesz uruchomić usługę Azure dziennika integracji na maszynie wirtualnej, maszyna wirtualna może być znajdujących się lokalnie lub w chmurze publicznej, takich jak Microsoft Azure.

Maszyny fizycznej lub wirtualnej uruchomiona usługa integracji dziennika Azure wymaga połączenia sieciowego chmurze publicznej Azure. Ten artykuł zawiera szczegółowe informacje o wymaganej konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Co najmniej instalowanie integracji dziennika Azure wymaga następujących elementów:

* **Subskrypcji platformy Azure**. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* A **konta magazynu** które mogą być używane dla systemu Windows Azure Diagnostics (WAD) rejestrowania. Można użyć konta magazynu wstępnie skonfigurowane lub utworzyć nowe konto magazynu. W dalszej części tego artykułu firma Microsoft opisano sposób konfigurowania konta magazynu.

  > [!NOTE]
  > W zależności od scenariusza konto magazynu nie może być wymagane. Diagnostyka Azure scenariusza omówione w tym artykule wymagane jest konto magazynu.

* **Dwa systemy**: 
  * Komputerze, na którym działa usługa Azure dziennika integracji. Ta maszyna zbiera wszystkie informacje dziennika, który później został zaimportowany do rozwiązania SIEM. Ten system:
    * Może być lokalnej lub hostowanej na platformie Microsoft Azure.  
    * Musi być uruchomiona x64 wersji systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszej, i program Microsoft .NET 4.5.1 zainstalowane. Aby określić wersji platformy .NET, zobacz [ustalić, które wersje programu .NET Framework są zainstalowane](https://msdn.microsoft.com/library/hh925568).  
    * Musi mieć łączność z konta magazynu Azure, który został użyty do rejestrowania diagnostyki Azure. W dalszej części tego artykułu możemy opisują sposób potwierdzić łączność.
  * Na komputerze, który chcesz monitorować. To jest uruchomione jako maszyny Wirtualnej [maszyny wirtualnej platformy Azure](../virtual-machines/virtual-machines-windows-overview.md). Rejestrowanie informacji z tego komputera są wysyłane do komputera Usługa integracji dziennika Azure.

Pokaz szybkiego tworzenia maszyny wirtualnej za pomocą portalu Azure, aby uzyskać Spójrz na poniższe wideo:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Podczas testowania, można użyć dowolnego systemu, który spełnia minimalne wymagania systemu operacyjnego. W środowisku produkcyjnym, obciążenie może być konieczna planowanie skalowanie w i skalowania.

Można uruchomić wiele wystąpień usługi integracji dziennika Azure. Można jednak uruchomić tylko jedno wystąpienie usługi na maszynie fizycznej lub wirtualnej. Ponadto można kont magazynu diagnostyki Azure Równoważenie obciążenia dla WAD. Liczba subskrypcji zapewnienie wystąpienia jest oparta na wydajność.

> [!NOTE]
> Obecnie nie mamy szczegółowe zalecenia o tym, kiedy do skalowania w poziomie wystąpień maszyn integracji dziennika Azure (czyli maszyny uruchomiona usługa integracji dziennika Azure) lub dla konta magazynu lub subskrypcji. Decyzje skalowania na podstawie obserwacji Twojej wydajności w każdym z tych obszarów.

W celu zwiększenia wydajności, również mieć możliwość skalować usługę Azure dziennika integracji. Następujące metryki wydajności mogą pomóc rozmiaru maszyny, które chcesz uruchomić usługę Azure dziennika integracji:

* Na maszynie procesor 8 (podstawowe) jedno wystąpienie integracji dziennika Azure może przetworzyć około 24 miliony zdarzeń na dzień (około 1 miliona zdarzeń na godzinę).
* Na komputerze 4-procesora (podstawowe) jedno wystąpienie integracji dziennika Azure może przetworzyć około 1,5 miliona zdarzeń na dzień (około 62,500 zdarzeń na godzinę).

## <a name="install-azure-log-integration"></a>Zainstaluj integracji dzienników Azure

Aby zainstalować Azure dziennika integracji, Pobierz [integracji dziennika Azure](https://www.microsoft.com/download/details.aspx?id=53324) pliku instalacyjnego. Zakończenie procesu instalacji. Określ, czy podać informacje w danych telemetrycznych do firmy Microsoft.

Usługa Azure dziennika integracji zbiera dane telemetryczne z komputera, na którym jest zainstalowany.  

Dane telemetryczne zbierane obejmuje następujące funkcje:

* Wyjątki występujących podczas wykonywania integracji dziennika Azure.
* Metryki dotyczące liczby zapytań i przetwarzania zdarzeń.
* Statystyka, o które Azlog.exe służą opcje wiersza polecenia. 

> [!NOTE]
> Firma Microsoft zaleca, aby zezwolić firmie Microsoft na zbieranie danych telemetrycznych. Zbieranie danych telemetrycznych można wyłączyć, usuwając zaznaczenie **Zezwalaj firmie Microsoft na zbieranie danych telemetrycznych** pole wyboru.
>

![Zrzut ekranu okienka instalacji z zaznaczonym polem wyboru telemetrii](./media/security-azure-log-integration-get-started/telemetry.png)


Proces instalacji jest uwzględnione w poniższym klipie wideo:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Czynności po instalacji i sprawdzania poprawności

Po zakończeniu konfiguracji podstawowej, możesz przystąpić do wykonania po instalacji i weryfikacja kroków:

1. Otwórz program PowerShell jako administrator. Następnie przejdź do C:\Program Files\Microsoft Azure dziennika integracji.
2. Zaimportuj polecenia cmdlet programu Azure dziennika integracji. Aby zaimportować polecenia cmdlet, uruchom skrypt `LoadAzlogModule.ps1`. Wprowadź `.\LoadAzlogModule.ps1`, a następnie naciśnij klawisz Enter (Zwróć uwagę na użycie **.\\**  w tym poleceniu). Powinien zostać wyświetlony ekran podobny do wyświetlaną na poniższej ilustracji:

  ![Zrzut ekranu przedstawiający dane wyjściowe polecenia LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Skonfiguruj integrację dziennika Azure do użycia w określonym środowisku platformy Azure. *Środowiska platformy Azure* jest typem centrum danych chmury Azure, którego chcesz używać. Mimo że istnieją obecnie kilku środowiskach Azure, odpowiednie opcje są **AzureCloud** lub **AzureUSGovernment**. Uruchamianie programu PowerShell jako administrator, upewnij się, że jesteś w C:\Program Files\Microsoft Azure dziennika Integration\. Następnie uruchom następujące polecenie:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (dla **AzureCloud**)
  
  Aby stosować usługi w chmurze Azure dla instytucji rządowych nam, należy użyć **AzureUSGovernment** dla **— nazwa** zmiennej. Obecnie nie są obsługiwane inne chmury Azure.  

  > [!NOTE]
  > Nie otrzymasz opinię, jeśli polecenie powiedzie się. 

4. Zanim będzie możliwe monitorowanie systemu, potrzebna jest nazwa konta magazynu, które jest używane diagnostyki Azure. W portalu Azure, przejdź do **maszyn wirtualnych**. Poszukaj maszyny wirtualnej systemu Windows, który będzie monitorował. W **właściwości** zaznacz **ustawień diagnostycznych**.  Następnie wybierz opcję **agenta**. Zanotuj nazwę konta magazynu, który jest określony. Nazwa tego konta w późniejszym kroku jest konieczne.

  ![Zrzut ekranu okienka ustawień diagnostyki Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Zrzut ekranu Włącz przycisk monitorowania poziomie gościa](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Jeśli monitorowanie nie zostało włączone, podczas tworzenia maszyny wirtualnej, można włączyć jak pokazano na powyższej ilustracji.

5. Teraz przejdź wstecz do integracji dziennika Azure maszyny. Sprawdź, czy łączności z kontem magazynu z systemu, w którym zainstalowano integracji dziennika Azure. Komputera z uruchomioną usługą integracji dziennika Azure wymaga dostępu do konta magazynu, aby pobrać informacje diagnostyki Azure rejestrowane w każdym monitorowanym systemie. Aby sprawdzić połączenia: 
  1. [Pobierz Eksploratora usługi Azure Storage](http://storageexplorer.com/).
  2. Zakończenie instalacji.
  3. Po zakończeniu instalacji wybierz **dalej**. Pozostaw **Uruchom Eksploratora usługi Microsoft Azure Storage** zaznaczone pole wyboru.  
  4. Zaloguj się do platformy Azure.
  5. Sprawdź, czy widzisz konta magazynu, które skonfigurowano diagnostyki Azure: 

   ![Zrzut ekranu przedstawiający kont magazynu w Eksploratorze usługi Storage](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Kilka opcji są wyświetlane w obszarze kont magazynu. W obszarze **tabel**, powinny pojawić się tabeli o nazwie **WADWindowsEventLogsTable**.

  Jeśli monitorowanie nie zostało włączone, podczas tworzenia maszyny wirtualnej, możesz je włączyć, zgodnie z wcześniejszym opisem.


## <a name="integrate-windows-vm-logs"></a>Integracja dzienniki maszyny Wirtualnej systemu Windows

W tym kroku skonfigurujesz komputerze, na którym działa usługa Azure dziennika integracji do nawiązania połączenia konta magazynu, który zawiera pliki dziennika.

Aby ukończyć ten krok, należy kilka rzeczy:  
* **FriendlyNameForSource**: przyjaznej nazwy, które można zastosować do konta magazynu, czy skonfigurowano dla maszyny wirtualnej do przechowywania informacji z diagnostyki Azure.
* **StorageAccountName**: Nazwa konta magazynu określony podczas konfigurowania diagnostyki Azure.  
* **Atrybutu StorageKey**: Klucz magazynu dla konta magazynu, w którym przechowywane są informacje diagnostyki Azure dla tej maszyny wirtualnej.  

Aby uzyskać klucz magazynu, wykonaj następujące kroki:
1. Przejdź do witryny [Azure Portal](http://portal.azure.com).
2. W okienku nawigacji wybierz **wszystkie usługi**.
3. W **filtru** wprowadź **magazynu**. Następnie wybierz opcję **kont magazynu**.

  ![Zrzut ekranu pokazujący kont magazynu w wszystkie usługi](./media/security-azure-log-integration-get-started/filter.png)

4. Zostanie wyświetlona lista kont magazynu. Kliknij dwukrotnie konto przypisane do logowania magazynu.

  ![Zrzut ekranu pokazujący listę kont magazynu](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.

  ![Zrzut ekranu pokazujący opcji klucze dostępu w menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopiuj **klucz1**, a następnie zapisz go w bezpiecznej lokalizacji, w której będziesz mieć dostęp do następny krok.
7. Na serwerze, w którym zainstalowano integracji dziennika Azure Otwórz okno wiersza polecenia z uprawnieniami administratora. (Pamiętaj otworzyć okno wiersza polecenia jako administrator, a nie programu PowerShell).
8. Przejdź do C:\Program Files\Microsoft Azure dziennika integracji.
9. Uruchom to polecenie: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Przykład:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Jeśli chcesz, aby identyfikator subskrypcji wyświetlani w zdarzeniu XML, Dołącz identyfikator subskrypcji do przyjaznej nazwy:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Przykład:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Poczekaj do 60 minut, a następnie wyświetlać zdarzenia, które są pobierane z konta magazynu. Aby wyświetlić zdarzenia, integracja dziennika Azure, wybierz **Podgląd zdarzeń** > **dzienniki systemu Windows** > **zdarzenia przekazane**.

Poniższe wideo omawia powyższych kroków:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Jeśli dane nie jest wyświetlana w folderze zdarzenia przekazane
Jeśli dane nie jest wyświetlana w folderze zdarzenia przekazane po upływie godziny, wykonaj następujące kroki:

1. Sprawdź maszyny, na którym uruchomiona jest usługa Azure dziennika integracji. Upewnij się, że można uzyskać dostępu do platformy Azure. Aby przetestować połączenie, w przeglądarce, spróbuj przejść do [portalu Azure](http://portal.azure.com).
2. Upewnij się, że konto użytkownika Azlog ma uprawnienie do zapisu dla folderu users\Azlog.
  1. Otwórz Eksploratora plików.
  2. Przejdź do C:\users.
  3. Kliknij prawym przyciskiem myszy C:\users\Azlog.
  4. Wybierz **zabezpieczeń**.
  5. Wybierz **NT Service\Azlog**. Sprawdź uprawnienia dla konta. Jeśli konto nie ma na tej karcie lub odpowiednie uprawnienia, które nie są wyświetlane, można przyznać uprawnień dostępu dla konta na tej karcie.
3. Po uruchomieniu polecenia `Azlog source list`, upewnij się, że magazyn kontem, które dodano w poleceniu `Azlog source add` znajduje się w danych wyjściowych.
4. Aby sprawdzić, czy z usługi Azure dziennika integracji są zgłoszone żadne błędy, przejdź do **Podgląd zdarzeń** > **dzienniki systemu Windows** > **aplikacji**.

Jeśli wystąpiły problemy podczas instalacji i konfiguracji, można utworzyć [żądania obsługi](../azure-supportability/how-to-create-azure-support-request.md). Dla usługi, wybierz **integracji dziennika**.

Inną opcją pomocy technicznej jest [forum MSDN integracji dziennika Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Forum MSDN społeczności może zapewnić obsługę udzielenie odpowiedzi na pytania i udostępniając porady i wskazówki dotyczące sposobu maksymalne wykorzystanie integracji dziennika Azure. Zespół Azure dziennika integracji monitoruje również tym forum. Pomaga on zawsze, gdy to możliwe.

## <a name="integrate-azure-activity-logs"></a>Integracja Dzienniki aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, która zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji na zdarzenia kondycji usługi. Alerty Centrum zabezpieczeń Azure znajdują się również w tym dzienniku.
> [!NOTE]
> Przed podjęciem próby kroki opisane w tym artykule, należy przejrzeć [wprowadzenie](security-azure-log-integration-get-started.md) artykułu i wykonaj kroki istnieje.

### <a name="steps-to-integrate-azure-activity-logs"></a>Kroki integracji Dzienniki aktywności platformy Azure

1. Otwórz wiersz polecenia i uruchom to polecenie:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Uruchom następujące polecenie:  ```azlog createazureid```

    To polecenie wyświetla monit o podanie logowania do systemu Azure. Polecenie następnie tworzy usługi Azure Active Directory nazwy głównej usługi w dzierżaw usługi Azure AD, które subskrypcji platformy Azure, w których zalogowany użytkownik jest administratora, administratora współpracującego lub właściciela hosta. Polecenie zakończy się niepowodzeniem, jeśli tylko użytkownik-Gość w dzierżawie usługi Azure AD jest zalogowanego użytkownika. Uwierzytelnianie na platformie Azure odbywa się za pośrednictwem usługi Azure AD. Tworzenie nazwy głównej usługi integracji dziennika Azure tworzy tożsamości usługi Azure AD, która uzyskuje dostęp do odczytu z subskrypcji platformy Azure.
3.  Uruchom następujące polecenie, aby autoryzować nazwy głównej usługi integracji dziennika Azure, które zostały utworzone w poprzednich krok dostęp do odczytu dziennika aktywności subskrypcji. Musisz być właścicielem subskrypcji o uruchomienie polecenia.

    ```Azlog.exe authorize subscriptionId``` Przykład:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```
4.  Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji usługi Azure Active Directory są tworzone w nich:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje dotyczące przełączania informacji w plikach JSON do informacji o zabezpieczeniach i zdarzeń systemu zarządzania (SIEM) skontaktuj się z dostawcą SIEM.

Społeczność pomoc jest dostępna za pośrednictwem [Forum MSDN integracji dziennika Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Forum to umożliwia użytkownikom w społeczności integracji dziennika Azure do obsługi siebie z pytania, odpowiedzi, porady i wskazówki. Ponadto zespół Azure dziennika integracji monitoruje tym forum i pomaga zawsze, gdy mogą go.

Można również otworzyć [żądania obsługi](../azure-supportability/how-to-create-azure-support-request.md). Wybierz Dziennik integracji usługi żądania pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat integracji dziennika Azure, zobacz następujące artykuły: przed podjęciem próby kroki opisane w tym artykule, należy przejrzeć artykuł Uruchomiono Get i wykonaj kroki.

* [Integracja dziennika Azure dla dzienników Azure](https://www.microsoft.com/download/details.aspx?id=53324). Centrum pobierania zawiera szczegółowe informacje, wymagania systemowe i instrukcje dotyczące instalacji integracji dziennika Azure.
* [Wprowadzenie do integracji dzienników Azure](security-azure-log-integration-overview.md). W tym artykule przedstawiono integracji dziennika Azure, jego kluczowych możliwości i jak działa.
* [Czynności konfiguracyjnych partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ten wpis w blogu pokazano, jak skonfigurować integrację dziennika Azure do pracy z rozwiązań partnerskich Splunk HP ArcSight i IBM QRadar. Opisuje nasze Bieżące wskazówki dotyczące sposobu konfigurowania składników SIEM. Skontaktuj się z dostawcą SIEM, aby uzyskać dodatkowe szczegóły.
* [Integracja dziennika Azure — często zadawane pytania (FAQ)](security-azure-log-integration-faq.md). Często zadawane pytania odpowiedzi na często zadawane pytania dotyczące integracji dziennika Azure.
* [Integrowanie alerty Centrum zabezpieczeń Azure z integracji dziennika Azure](../security-center/security-center-integrating-alerts-with-log-integration.md). W tym artykule przedstawiono sposób synchronizować alerty Centrum zabezpieczeń i zdarzenia zabezpieczeń maszyny wirtualnej, które są zbierane przez diagnostyki Azure i działanie Azure dzienniki. Dzienniki są synchronizowane przy użyciu rozwiązania Azure Log Analytics lub SIEM.
* [Nowe funkcje diagnostyki Azure i Azure dzienniki inspekcji](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ten wpis w blogu stanowi wprowadzenie do dzienników inspekcji platformy Azure i inne funkcje, które mogą pomóc Ci wgląd w działania zasobów platformy Azure.
