---
title: Rozpoczynanie pracy z usługą Azure Log Integration | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługę Azure Log Integration i integrowanie dzienników z usługi Azure Storage, dzienniki inspekcji platformy Azure i alertów usługi Azure Security Center.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 244b2d1764f30f790c3e51e23cd2fa0af6375960
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480255"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration rejestrowania diagnostyki Azure i funkcji przekazywania zdarzeń Windows


>[!IMPORTANT]
> Funkcja integracji dziennika Azure zostaną wycofane 06/01/2019 r. Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dzienników platformy Azure należy używać tylko, jeśli [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) łącznik nie jest dostępna z dostawcą zdarzeń naruszenia zabezpieczeń i Event Management (SIEM).

Integracja dzienników platformy Azure udostępnia dzienniki platformy Azure do rozwiązania SIEM aby można było utworzyć pulpit nawigacyjny ujednoliconego zabezpieczeń wszystkich zasobów.
Aby uzyskać więcej informacji na temat stanu łącznika usługi Azure Monitor skontaktuj się z dostawcą rozwiązania SIEM.

> [!IMPORTANT]
> Jeśli zainteresowanie podstawowy jest zbieranie dzienników maszyny wirtualnej, większość dostawców rozwiązania SIEM uwzględnić tę opcję w swoich rozwiązaniach. Za pomocą rozwiązania SIEM dostawcy łącznik jest zawsze preferowana alternatywa.

Ten artykuł ułatwia rozpoczynanie pracy z usługą Azure Log Integration. Koncentruje się ona na instalowanie usługi Azure Log Integration i Integrowanie usługi za pomocą diagnostyki Azure. Usługa Azure Log Integration następnie zbiera informacje w dzienniku zdarzeń Windows z kanału Windows zdarzenie zabezpieczeń z maszyn wirtualnych wdrożonych w infrastrukturze platformy Azure jako usługa. Jest to podobne do *funkcji przekazywania zdarzeń* używanego w systemie w środowisku lokalnym.

> [!NOTE]
> Integracja danych wyjściowych usługi Azure Log Integration rozwiązania SIEM odbywa się przez samego rozwiązania SIEM. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Log Integration z lokalnego systemu SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Usługa Azure Log Integration działa na fizyczny lub wirtualny komputer z systemem Windows Server 2008 R2 lub nowszy (Windows Server 2016 lub Windows Server 2012 R2 jest preferowana).

Komputer fizyczny, można uruchomić w środowisku lokalnym lub w witrynie hostingu. Jeśli użytkownik chce uruchomić usługę Azure Log Integration na maszynie wirtualnej, maszyna wirtualna może być znajdujących się lokalnie lub w chmurze publicznej, takich jak Microsoft Azure.

Fizyczne lub maszyny wirtualnej, w którym działa usługa Azure Log Integration wymaga łączności sieciowej do chmury publicznej platformy Azure. Ten artykuł zawiera szczegółowe informacje o wymaganej konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Jako minimum Instalowanie usługi Azure Log Integration wymaga następujących elementów:

* **Subskrypcji platformy Azure**. Jeśli go nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/free/).
* A **konta magazynu** mogą służyć do Windows Azure Diagnostics (WAD) rejestrowania. Można użyć konta wstępnie skonfigurowanego magazynu lub Utwórz nowe konto magazynu. W dalszej części tego artykułu opisano sposób konfigurowania konta magazynu.

  > [!NOTE]
  > Zależnie od scenariusza konto magazynu nie może być wymagane. W scenariuszu diagnostyki platformy Azure, omówione w tym artykule konto magazynu jest wymagana.

* **Dwa systemy**: 
  * Maszyna, na którym uruchomiona jest usługa Azure Log Integration. Ta maszyna zbiera wszystkie informacje dziennika, który później został zaimportowany do rozwiązania SIEM. Ten system:
    * Może być lokalnej lub hostowanej na platformie Microsoft Azure.  
    * X64 musi być uruchomiona wersja systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszego, i Microsoft .NET 4.5.1 zainstalowane. Aby ustalić zainstalowaną wersję platformy .NET, zobacz [ustalić, które wersje programu .NET Framework są zainstalowane](https://msdn.microsoft.com/library/hh925568).  
    * Musi mieć połączenie z kontem usługi Azure Storage, która została użyta do rejestrowania diagnostyki platformy Azure. W dalszej części tego artykułu opisano sposób potwierdzić łączność.
  * Maszyna, na który chcesz monitorować. Jest to maszyna wirtualna jako [maszyny wirtualnej platformy Azure](../virtual-machines/virtual-machines-windows-overview.md). Rejestrowanie informacji z tego komputera są wysyłane do komputera usługi Azure Log Integration.

Szybkie demonstracyjne jak utworzyć maszynę wirtualną przy użyciu witryny Azure portal Przyjrzyj się poniższy film wideo:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Podczas testowania, możesz użyć dowolnego systemu, który spełnia wymagania minimalnej wersji systemu operacyjnego. W środowisku produkcyjnym, obciążenia może być konieczna planowanie skalowanie w górę i skalowania w poziomie.

Możesz uruchomić wiele wystąpień usługi Azure Log Integration. Można jednak uruchomić tylko jedno wystąpienie usługi na maszynie fizycznej lub wirtualnej. Ponadto można kont magazynu usługi Azure Diagnostics Równoważenie obciążenia dla WAD. Liczba subskrypcji, aby zapewnić do wystąpień jest oparty na pojemności.

> [!NOTE]
> Obecnie nie mamy zalecenia o tym, kiedy skalować wystąpień maszyn Azure Log Integration (czyli komputerów z systemem usługi Azure Log Integration) lub dla kont magazynu lub subskrypcji. Decyzje dotyczące skalowania na podstawie obserwacji Twojej wydajności w każdej z tych obszarów.

Aby zwiększyć wydajność, również mieć możliwość skalowania usługi Azure Log Integration. Następujące metryki wydajności mogą pomóc w rozmiarze maszyn, które postanowisz uruchomić usługę Azure Log Integration:

* Na maszynie 8-procesorowe (rdzenie) pojedynczego wystąpienia usługi Azure Log Integration może przetworzyć około 24 mln zdarzeń każdego dnia (około 1 milion zdarzeń na godzinę).
* Na komputerze 4 procesory (core) pojedyncze wystąpienie usługi Azure Log Integration może przetwarzać zdarzenia około 1,5 miliona na dzień (około 62,500 zdarzeń na godzinę).

## <a name="install-azure-log-integration"></a>Zainstaluj integracja dzienników platformy Azure

Uruchom za pomocą ustawień rutynowych. Wybierz, czy podać informacje o telemetrii do firmy Microsoft.

Usługa Azure Log Integration zbiera dane telemetryczne z komputera, na którym jest zainstalowany.  

Dane telemetryczne, które są zbierane obejmuje następujące funkcje:

* Wyjątki, które występują podczas wykonywania funkcji integracji dzienników Azure.
* Metryki dotyczące liczby zapytań i przetwarzania zdarzeń.
* Statystyki, o które Azlog.exe są używane opcje wiersza polecenia. 

> [!NOTE]
> Firma Microsoft zaleca, aby umożliwić firmie Microsoft na zbieranie danych telemetrycznych. Zbieranie danych telemetrycznych można wyłączyć, usuwając zaznaczenie **Zezwalaj firmie Microsoft na zbieranie danych telemetrycznych** pole wyboru.
>

![Zrzut ekranu przedstawiający okienko instalacji, zaznaczone pole wyboru telemetrii](./media/security-azure-log-integration-get-started/telemetry.png)


Proces instalacji został omówiony w poniższym klipie wideo:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Kroki po instalacji i sprawdzaniu poprawności

Po zakończeniu podstawowej konfiguracji możesz przystąpić do wykonania po instalacji i sprawdzaniu poprawności kroków:

1. Otwórz program PowerShell jako administrator. Następnie przejdź do C:\Program Files\Microsoft Azure Log Integration.
2. Zaimportować polecenia cmdlet usługi Azure Log Integration. Aby zaimportować polecenia cmdlet, uruchom skrypt `LoadAzlogModule.ps1`. Wprowadź `.\LoadAzlogModule.ps1`, a następnie naciśnij klawisz Enter (Zwróć uwagę na użycie **.\\**  w tym poleceniu). Powinien zostać wyświetlony podobny do wyświetlanych na poniższej ilustracji:

   ![Zrzut ekranu przedstawiający dane wyjściowe polecenia LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Następnie skonfiguruj integracji dzienników platformy Azure do użycia w danym środowisku platformy Azure. *Środowiska platformy Azure* jest typem centrum danych w chmurze platformy Azure, którą chcesz pracować. Chociaż istnieje kilka środowisk platformy Azure, obecnie, odpowiednie opcje są **AzureCloud** lub **AzureUSGovernment**. Uruchamianie programu PowerShell jako administrator, upewnij się, że jesteś w C:\Program Files\Microsoft Azure dziennika Integration\. Następnie uruchom następujące polecenie:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
   Jeśli chcesz korzystać z chmury Azure dla instytucji rządowych USA, użyj **AzureUSGovernment** dla **— nazwa** zmiennej. Obecnie nie są obsługiwane inne chmury platformy Azure.  

   > [!NOTE]
   > Nie otrzymasz opinii, gdy polecenie powiedzie się. 

4. Aby można było monitorować system, potrzebna jest nazwa konta magazynu, który jest używany dla usługi Azure Diagnostics. W witrynie Azure portal przejdź do **maszyn wirtualnych**. Wyszukaj maszynę wirtualną Windows, która będzie monitorować. W **właściwości** zaznacz **ustawień diagnostycznych**.  Następnie wybierz **agenta**. Zanotuj nazwę konta magazynu, który jest określony. Należy tę nazwę konta w późniejszym kroku.

   ![Zrzut ekranu okienka ustawień diagnostyki Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

   ![Zrzut ekranu włączyć przycisk monitorowania poziomie gościa](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Jeśli nie zostało włączone monitorowanie, gdy maszyna wirtualna została utworzona, można włączyć pokazany na wcześniejszej ilustracji.

5. Teraz wróć do maszyny integracji dzienników Azure. Sprawdź, czy masz połączenie z kontem usługi storage z systemu zainstalowanym Azure Log Integration. Komputera z uruchomioną usługą Azure Log Integration wymaga dostępu do konta magazynu, aby pobrać informacje, które jest rejestrowane przez diagnostykę platformy Azure na wszystkich monitorowanych systemach. Aby sprawdzić połączenie: 
   1. [Pobierz bezpłatnie Eksplorator magazynu Azure](https://storageexplorer.com/).
   2. Przeprowadź instalację.
   3. Po zakończeniu instalacji wybierz **dalej**. Pozostaw **Uruchom Eksploratora usługi Microsoft Azure Storage** zaznaczone pole wyboru.  
   4. Zaloguj się do platformy Azure.
   5. Sprawdź, czy można wyświetlić konto magazynu, który został skonfigurowany dla usługi Azure Diagnostics: 

   ![Zrzut ekranu przedstawiający kont magazynu w programie Storage Explorer](./media/security-azure-log-integration-get-started/storage-explorer.png)

   1. Jest kilka opcji są wyświetlane w ramach kont magazynu. W obszarze **tabel**, powinien zostać wyświetlony tabeli o nazwie **WADWindowsEventLogsTable**.

   Jeśli nie zostało włączone monitorowanie, gdy maszyna wirtualna została utworzona, można włączyć, zgodnie z wcześniejszym opisem.


## <a name="integrate-windows-vm-logs"></a>Integrowanie dzienników maszyn wirtualnych Windows

W tym kroku skonfigurujesz maszynie usługi Azure Log Integration, aby połączyć się z kontem magazynu, który zawiera pliki dziennika.

Aby ukończyć ten krok, konieczne jest kilka rzeczy:  
* **FriendlyNameForSource**: Przyjazna nazwa, które można zastosować do konta magazynu, czy skonfigurowano dla maszyny wirtualnej do przechowywania informacji z usługi Azure Diagnostics.
* **StorageAccountName**: Nazwa konta magazynu, które zostały określone podczas konfigurowania usługi Azure Diagnostics.  
* **StorageKey**: Klucz magazynu dla konta magazynu, w którym przechowywane są informacje diagnostyczne platformy Azure dla tej maszyny wirtualnej.  

Aby uzyskać klucz magazynu, wykonaj następujące czynności:
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W okienku nawigacji wybierz **wszystkich usług**.
3. W **filtru** wprowadź **magazynu**. Następnie wybierz **kont magazynu**.

   ![Zrzut ekranu pokazujący kont magazynu w wszystkie usługi](./media/security-azure-log-integration-get-started/filter.png)

4. Zostanie wyświetlona lista kont magazynu. Kliknij dwukrotnie konto które są przypisane do rejestrowania magazynu.

   ![Zrzut ekranu przedstawiający listę kont magazynu](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. W obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.

   ![Zrzut ekranu pokazujący opcja klucze dostępu w menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopiuj **klucz1**, a następnie zapisz go w bezpiecznej lokalizacji, w której będziesz mieć dostęp na potrzeby następnego kroku.
7. Na serwerze, na którym zostanie zainstalowany Azure Log Integration Otwórz okno wiersza polecenia jako administrator. (Pamiętaj otworzyć okno wiersza polecenia jako administrator, a nie programu PowerShell).
8. Przejdź do C:\Program Files\Microsoft Azure Log Integration.
9. Uruchom następujące polecenie: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Przykład:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Jeśli chcesz, aby identyfikator subskrypcji do wyświetlenia w zdarzeniu XML, Dołącz identyfikator subskrypcji przyjazną nazwę:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Przykład:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Poczekaj do 60 minut, a następnie przeglądać zdarzenia, które są pobierane z konta magazynu. Zaznacz, aby wyświetlić zdarzenia, w usłudze Azure Log Integration **Podgląd zdarzeń** > **Dzienniki Windows** > **zdarzenia przesyłane dalej**.

Poniższy film wideo omawia powyższych kroków:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Jeśli dane nie są wyświetlane w folderze zdarzenia przesyłane dalej
Jeśli dane nie są wyświetlane w folderze zdarzenia przesyłane dalej po upływie godziny, wykonaj następujące kroki:

1. Sprawdź, czy komputer, na którym działa usługa Azure Log Integration Upewnij się, że mają dostęp do platformy Azure. Aby przetestować łączność, w przeglądarce, spróbuj przejść do [witryny Azure portal](https://portal.azure.com).
2. Upewnij się, że konto użytkownika Azlog ma uprawnienia do zapisu dla users\Azlog folderu.
   1. Otwórz Eksploratora plików.
   2. Przejdź do C:\users.
   3. Kliknij prawym przyciskiem myszy C:\users\Azlog.
   4. Wybierz **zabezpieczeń**.
   5. Wybierz **NT Service\Azlog**. Sprawdź uprawnienia dla konta. Jeśli konto nie ma na tej karcie lub odpowiednie uprawnienia nie są wyświetlane, można przyznać uprawnień konta na tej karcie.
3. Po uruchomieniu polecenia `Azlog source list`, upewnij się, że konto magazynu, który został dodany w poleceniu `Azlog source add` znajduje się w danych wyjściowych.
4. Aby zobaczyć, jeśli błędy są zgłaszane z usługi Azure Log Integration, przejdź do **Podgląd zdarzeń** > **Dzienniki Windows** > **aplikacji**.

Jeśli napotkasz problemy podczas instalacji i konfiguracji, możesz utworzyć [żądania pomocy technicznej](../azure-supportability/how-to-create-azure-support-request.md). Usługi, wybierz **integracji dzienników**.

Inną możliwością obsługi [forum MSDN integracji dziennika Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Forum MSDN społeczności może zapewnić obsługę przez udostępnianie porady i wskazówki dotyczące w pełni wykorzystać usługi Azure Log Integration i udzielenie odpowiedzi na pytania. Zespół Azure Log Integration również monitoruje to forum. Pomagają w każdym przypadku, gdy to możliwe.

## <a name="integrate-azure-activity-logs"></a>Integrowanie dzienników aktywności platformy Azure

Dziennik aktywności platformy Azure jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji dla zdarzeń kondycji usługi. Alerty Centrum zabezpieczeń Azure znajdują się również do tego dziennika.
> [!NOTE]
> Przed rozpoczęciem kroków opisanych w tym artykule, należy przejrzeć [wprowadzenie](security-azure-log-integration-get-started.md) i wykonaj kroki istnieje.

### <a name="steps-to-integrate-azure-activity-logs"></a>Kroki, aby zintegrować Dzienniki aktywności platformy Azure

1. Otwórz wiersz polecenia i uruchom następujące polecenie:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Uruchom następujące polecenie:  ```azlog createazureid```

    To polecenie wyświetli monit o podanie logowania do systemu Azure. Polecenie utworzy usługi Azure Active Directory nazwy głównej usługi w dzierżawach usługi Azure AD, obsługujące subskrypcji platformy Azure, w których zalogowanego użytkownika jest administrator, administrator współpracujący lub właściciela. Polecenie zakończy się niepowodzeniem, jeśli użytkownik zalogowany jest tylko użytkownik-Gość w dzierżawie usługi Azure AD. Uwierzytelnianie na platformie Azure odbywa się za pośrednictwem usługi Azure AD. Tworzenie nazwy głównej usługi Azure Log Integration tworzy tożsamości usługi Azure AD, która otrzymuje dostęp do odczytu z subskrypcji platformy Azure.
3. Uruchom następujące polecenie, aby autoryzować nazwę główną usługi Azure Log Integration, które zostały utworzone w kroku poprzedniego możliwość odczytu dziennika aktywności dla subskrypcji. Musisz być właścicielem subskrypcji do uruchomienia polecenia.

   ```Azlog.exe authorize subscriptionId``` Przykład:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji usługi Azure Active Directory są tworzone w nich:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje zapewniania informacji w plikach JSON security information and event management (SIEM) systemu skontaktuj się z dostawcą rozwiązania SIEM.

Pomoc społeczności jest dostępna za pośrednictwem [forum MSDN dotyczącym usługi Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). To forum temu inni członkowie społeczności integracji dzienników platformy Azure do obsługi innych za pomocą pytań, odpowiedzi, porady i wskazówki. Ponadto zespół Azure Log Integration monitoruje to forum i pomaga w każdym przypadku, gdy jest to możliwe.

Możesz również otworzyć [żądania pomocy technicznej](../azure-supportability/how-to-create-azure-support-request.md). Wybierz integracji dzienników jako usługi, dla której zażądano pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat integracji dzienników platformy Azure, zobacz następujące artykuły: Przed próbą kroki opisane w tym artykule należy zapoznaj się z artykułem wprowadzenie Get i wykonaj kroki istnieje.

* [Wprowadzenie do integracji dziennika Azure](security-azure-log-integration-overview.md). Ten artykuł stanowi wprowadzenie do integracji dzienników platformy Azure, jego kluczowych możliwości i jak to działa.
* [Partner czynności konfiguracyjnych](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ten wpis w blogu dowiesz się, jak skonfigurować program Azure Log Integration do pracy z rozwiązaniami partnerów Splunk, HP ArcSight i QRadar firmy IBM. Opisano w nim nasze Bieżące wskazówki dotyczące sposobu konfigurowania składników rozwiązania SIEM. Skontaktuj się z dostawcą rozwiązania SIEM, aby uzyskać więcej informacji.
* [Integracja dzienników platformy Azure — często zadawane pytania (FAQ)](security-azure-log-integration-faq.md). Tych często zadawanych PYTAŃ, odpowiedzi na często zadawane pytania dotyczące usługi Azure Log Integration.
* [Integrowanie alertów usługi Azure Security Center z usługą Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). W tym artykule pokazano, jak można zsynchronizować alertów usługi Security Center i zdarzenia zabezpieczeń maszyny wirtualnej, które są zbierane przez usługi Azure Diagnostics i aktywności platformy Azure dzienników. Dzienniki są synchronizowane przy użyciu dzienników usługi Azure Monitor lub rozwiązania SIEM.
* [Dzienniki inspekcji nowe funkcje dla usługi Azure Diagnostics i Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ten wpis w blogu zawiera wprowadzenie do dzienników inspekcji platformy Azure oraz inne funkcje, które mogą pomóc Ci uzyskać wgląd w operacje zasobów platformy Azure.
