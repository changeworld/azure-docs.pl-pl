---
title: Instalacja środowiska wykonawczego funkcji Azure
description: Jak zainstalować środowisko uruchomieniowe usług Azure Functions w wersji 2
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 7ad748aa9a5b45af10121648a668344548484cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226735"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalowanie środowiska uruchomieniowego usługi Azure Functions w wersji zapoznawczej 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Jeśli chcesz zainstalować środowisko uruchomieniowe usługi Azure Functions w wersji 2, wykonaj następujące kroki:

1. Upewnij się, że urządzenie spełnia minimalne wymagania.
1. Pobierz [Instalator programu Azure Functions Runtime Preview](https://aka.ms/azafrv2).
1. Odinstaluj w wersji zapoznawczej 1 środowiska uruchomieniowego funkcji platformy Azure.
1. Zainstaluj środowisko uruchomieniowe usługi Azure Functions w wersji 2.
1. Ukończ konfigurację środowiska uruchomieniowego usługi Azure Functions w wersji 2.
1. Tworzenie pierwszej funkcji w usłudze Azure Functions Runtime Preview

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions muszą być dostępne następujące zasoby:

1. Komputer z systemem Microsoft Windows Server 2016 lub Microsoft Windows 10 Creators Update (Professional lub Enterprise Edition).
1. Wystąpienie programu SQL Server działające w sieci.  Minimalna wymagana wersja to SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstaluj poprzednią wersję

Jeśli wcześniej zainstalowano podgląd środowiska uruchomieniowego usług Azure Functions, należy odinstalować przed zainstalowaniem najnowszej wersji.  Odinstaluj podgląd środowiska uruchomieniowego funkcji Azure Functions, usuwając program w obszarze Dodaj/Usuń programy w systemie Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalowanie środowiska uruchomieniowego usługi Azure Functions Runtime Preview

Instalator usługi Azure Functions Runtime Preview przeprowadzi Cię przez instalację ról zarządzania i ról procesu roboczego w usłudze Azure Functions Runtime.  Istnieje możliwość zainstalowania zarządzania i pracy roli na tym samym komputerze.  Jednak w miarę dodawania większej liczby aplikacji funkcji, należy wdrożyć więcej ról procesu roboczego na dodatkowych komputerach, aby móc skalować funkcje na wielu procesach produkcyjnych.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalowanie roli zarządzania i pracownika na tym samym komputerze

1. Uruchom Instalatora środowiska uruchomieniowego usługi Azure Runtime Preview.

    ![Instalator w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][1]

1. Kliknij przycisk **alej**.
1. Po zapoznaniu się z warunkami **umowy Licencyjnej ,** **zaznacz pole,** aby zaakceptować warunki i kliknij przycisk **Dalej,** aby przejść dalej.
1. Wybierz role, które chcesz zainstalować na tym komputerze **Rola zarządzania funkcjami** i/lub **Rola procesu roboczego funkcji** i kliknij przycisk **Dalej**.

    ![Instalator podglądu środowiska uruchomieniowego usługi Azure Functions — wybór roli][3]

    > [!NOTE]
    > Rolę procesu **roboczego funkcji** można zainstalować na wielu innych komputerach. Aby to zrobić, postępuj zgodnie z tymi instrukcjami i wybierz tylko **funkcje roli procesu roboczego** w instalatorze.

1. Kliknij **przycisk Dalej,** aby **Kreator instalacji środowiska wykonawczego usług Azure Functions** rozpoczął proces instalacji na komputerze.
1. Po zakończeniu kreatora instalacji uruchamia narzędzie konfiguracji **środowiska wykonawczego usług Azure Functions.**

    ![Ukończono instalację w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][6]

    > [!NOTE]
    > Jeśli instalujesz w **systemie Windows 10,** a funkcja **Kontener** nie została wcześniej włączona, Instalator środowiska wykonawczego funkcji **Azure Functions** monituje o ponowne uruchomienie komputera w celu ukończenia instalacji.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurowanie środowiska wykonawczego funkcji platformy Azure

Aby zakończyć instalację środowiska uruchomieniowego usług Azure Functions, należy ukończyć konfigurację.

1. Narzędzie konfiguracji **usługi Azure Functions Runtime** pokazuje, które role są zainstalowane na komputerze.

    ![Narzędzie konfiguracji środowiska uruchomieniowego usługi Azure Functions w wersji zapoznawczej][7]

1. Kliknij kartę **Baza danych,** wprowadź szczegóły połączenia wystąpienia programu SQL Server, w tym określając [klucz główny bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)i kliknij przycisk **Zastosuj**.  Łączność z wystąpieniem programu SQL Server jest wymagana, aby środowisko uruchomieniowe funkcji platformy Azure utworzyło bazę danych do obsługi środowiska wykonawczego.

    ![Konfiguracja bazy danych w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions][8]

1. Kliknij kartę **Poświadczenia.**  W tym miejscu należy utworzyć dwa nowe poświadczenia do użycia z udziałem plików do obsługi wszystkich aplikacji funkcji.  Określ **kombinacje nazwy użytkownika** i **hasła** dla właściciela **udziału plików** i użytkownika **udziału plików,** a następnie kliknij przycisk **Zastosuj**.

    ![Poświadczenia w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][9]

1. Kliknij kartę **Udostępnianie plików.**  W tym miejscu należy określić szczegóły lokalizacji udziału plików.  Udział plików można utworzyć dla Ciebie lub użyć istniejącego udziału plików i **kliknąć**zastosuj .  Jeśli wybierzesz nową lokalizację udziału plików, należy określić katalog do użycia przez środowisko uruchomieniowe funkcji azure.

    ![Udział plików w podglądzie środowiska uruchomieniowego usługi Azure Functions][10]

1. Kliknij kartę **IIS.**  Ta karta zawiera szczegóły witryn sieci Web w usługach IIS, które tworzy narzędzie konfiguracji środowiska wykonawczego usług Azure Functions.  W tym miejscu można określić niestandardową nazwę DNS dla portalu w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions.  Kliknij **przycisk Zastosuj,** aby zakończyć.

    ![Usługi IIS środowiska uruchomieniowego funkcji Azure][11]

1. Kliknij kartę **Usługi.**  Ta karta pokazuje stan usług w narzędziu konfiguracji środowiska wykonawczego usług Azure Functions.  Jeśli **usługa aktywacji hosta usług Azure Functions** nie jest uruchomiona po wstępnej konfiguracji, kliknij przycisk **Uruchom usługę**.

    ![Ukończona konfiguracja w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][12]

1. Przejdź do **portalu środowiska wykonawczego usług Azure Functions w** stanie `https://<machinename>.<domain>/`.

    ![Portal w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Tworzenie pierwszej funkcji w podglądzie środowiska uruchomieniowego usług Azure Functions

Aby utworzyć pierwszą funkcję w podglądzie środowiska uruchomieniowego usług Azure Functions

1. Przejdź do **portalu środowiska wykonawczego usług Azure Functions,** jak `https://<machinename>.<domain>` na przykład `https://mycomputer.mydomain.com`.

1. Zostanie wyświetlony monit o **zalogowanie się,** jeśli wdrożone w domenie użyj nazwy użytkownika i hasła konta domeny, w przeciwnym razie użyj nazwy użytkownika i hasła do lokalnego konta, aby zalogować się do portalu.

    ![Logowanie do portalu w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions][14]

1. Aby utworzyć aplikacje funkcji, należy utworzyć subskrypcję.  W lewym górnym rogu portalu kliknij **+** opcję obok subskrypcji.

    ![Subskrypcje portalu w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions][15]

1. Wybierz **defaultplan**, wprowadź nazwę subskrypcji i kliknij przycisk **Utwórz**.

    ![Plan subskrypcji i nazwa portalu w wersji zapoznawczej usługi Azure Functions Runtime][16]

1. Wszystkie aplikacje funkcji są wyświetlane w lewym okienku portalu.  Aby utworzyć nową aplikację funkcji, wybierz nagłówek **+** **Aplikacje funkcji** i kliknij tę opcję.

1. Wprowadź nazwę aplikacji funkcji, wybierz właściwą subskrypcję, wybierz wersję środowiska uruchomieniowego usługi Azure Functions, którą chcesz zaprogramować, a następnie kliknij przycisk **Utwórz**

    ![Usługa Azure Functions Runtime Preview portal nowa aplikacja funkcji][17]

1. Nowa aplikacja funkcji jest wyświetlana w lewym okienku portalu.  Wybierz pozycję Funkcje, a następnie kliknij pozycję **Nowa funkcja** u góry środkowego okienka w portalu.

    ![Szablony w wersji zapoznawczej środowiska uruchomieniowego usług Azure Functions][18]

1. Wybierz funkcję Wyzwalacz czasomierza, w prawej nazwie wysuń funkcję i zmień harmonogram na `*/5 * * * * *` (to wyrażenie cron powoduje, że funkcja czasomierza jest wykonywana co pięć sekund), a następnie kliknij przycisk **Utwórz**

    ![Usługa Azure Functions Runtime — wersja zapoznawcza nowej konfiguracji funkcji czasomierza][19]

1. Funkcja została utworzona.  Dziennik wykonywania aplikacji Function można wyświetlić, rozwijając okienko **dziennika** u dołu portalu.

    ![Funkcja azure function runtime— wykonywanie funkcji][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
