---
title: Instalacja środowiska uruchomieniowego usługi Azure Functions | Dokumentacja firmy Microsoft
description: Jak zainstalować środowisko uruchomieniowe usługi Azure Functions korzystania z wersji zapoznawczej 2
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547560"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Zainstaluj środowisko uruchomieniowe usługi Azure Functions preview 2

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Jeśli chcesz zainstalować Podgląd środowiska uruchomieniowego usługi Azure Functions, 2, wykonaj następujące kroki:

1. Upewnij się, że komputer przekazuje minimalne wymagania.
1. Pobierz [Instalatora w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions](https://aka.ms/azafrv2).
1. Odinstaluj Podgląd środowiska uruchomieniowego usługi Azure Functions 1.
1. Zainstaluj środowisko uruchomieniowe usługi Azure Functions preview 2.
1. Zakończ konfigurację programu środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej 2.
1. Tworzenie pierwszej funkcji w usłudze Azure Functions Runtime — wersja zapoznawcza

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions, konieczne jest posiadanie dostępne następujące zasoby:

1. Maszyny z systemem Microsoft Windows Server 2016 lub Microsoft Windows 10 Creators Update (Edycja Enterprise lub Professional).
1. Wystąpienie programu SQL Server uruchomiony w ramach sieci użytkownika.  Minimalna wymagana wersja to SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstalowywanie poprzedniej wersji

Jeśli wcześniej zainstalowano środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej, należy odinstalować przed zainstalowaniem najnowszej wersji.  Odinstaluj środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej, usuwając program w aplecie Dodaj/Usuń programy w Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalowanie środowiska uruchomieniowego usługi Azure Functions w wersji zapoznawczej

Instalator w wersji zapoznawczej środowiska uruchomieniowego funkcji Azure przeprowadzi Cię przez instalację środowiska uruchomieniowego usługi Azure Functions w wersji zapoznawczej zarządzania i ról procesów roboczych.  Istnieje możliwość zainstalować rolę zarządzania i proces roboczy na tym samym komputerze.  Jednak w miarę dodawania większej liczby aplikacji funkcji, należy wdrożyć większej liczby ról procesów roboczych na dodatkowe maszyny, aby można było skalować funkcji na wielu procesów roboczych.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Zainstaluj na tym samym komputerze zarządzania i roli procesu roboczego

1. Uruchom Instalatora w wersji zapoznawczej środowiska uruchomieniowego usługi Azure Functions.

    ![Usługa Azure Instalatora w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][1]

1. Kliknij przycisk **Dalej**.
1. Po zapoznaniu się z postanowieniami **Umowa licencyjna EULA**, **zaznacz pole wyboru** Zaakceptuj warunki, a następnie kliknij przycisk **dalej** Aby awansować.
1. Wybierz role, którą chcesz zainstalować na tym komputerze **roli zarządzania funkcje** i/lub **roli procesu roboczego funkcji** i kliknij przycisk **dalej**.

    ![Instalator platformy Azure w wersji zapoznawczej środowiska uruchomieniowego usługi Functions — Wybór roli][3]

    > [!NOTE]
    > Możesz zainstalować **roli procesu roboczego funkcji** na innych komputerach. Aby to zrobić, wykonaj te instrukcje, a następnie wybrać tylko **roli procesu roboczego funkcji** w Instalatorze.

1. Kliknij przycisk **dalej** mieć **Kreatora instalacji środowiska uruchomieniowego funkcji Azure** rozpocząć proces instalacji na komputerze.
1. Po wykonaniu tych czynności, zostanie uruchomiony Kreator instalacji **środowisko uruchomieniowe usługi Azure Functions** narzędzie konfiguracji.

    ![Wykonaj Azure Instalatora w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][6]

    > [!NOTE]
    > Jeśli instalujesz na **systemu Windows 10** i **kontenera** funkcja nie została wcześniej włączona, **konfiguracji środowiska uruchomieniowego funkcji usługi Azure** wyświetli monit o ponowne uruchomienie komputera Aby zakończyć instalację.

## <a name="configure-the-azure-functions-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi Azure Functions

Aby ukończyć instalację środowiska uruchomieniowego usługi Azure Functions, należy wykonać konfigurację.

1. **Środowisko uruchomieniowe usługi Azure Functions** narzędzie konfiguracji pokazuje, które role są zainstalowane na tym komputerze.

    ![Narzędzia do konfiguracji platformy Azure w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][7]

1. Kliknij przycisk **bazy danych** wprowadź szczegóły połączenia dla wystąpienia programu SQL Server, łącznie z określeniem [klucz główny bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)i kliknij przycisk **Zastosuj**.  Aby środowisko uruchomieniowe usługi Azure Functions utworzyć bazę danych do obsługi środowiska uruchomieniowego, wymagana jest łączność z wystąpieniem programu SQL Server.

    ![Konfiguracja bazy danych (wersja zapoznawcza) w usłudze Azure środowisko uruchomieniowe usługi Functions][8]

1. Kliknij przycisk **poświadczenia** kartę.  W tym miejscu należy utworzyć dwa nowe poświadczenia do użycia z udziałem plików do hostowania aplikacji funkcji.  Określ **nazwy użytkownika** i **hasło** kombinacje w celach **właściciela udziału plików** i **użytkownika udziału plików**, następnie kliknij przycisk **Zastosuj**.

    ![Poświadczenia platformy Azure w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][9]

1. Kliknij przycisk **udziału plików** kartę.  W tym miejscu podaj szczegółowe informacje o lokalizację udziału plików.  Można utworzyć udziału plików dla Ciebie, lub możesz użyć istniejącego udziału plików i kliknij **Zastosuj**.  Jeśli wybierzesz nową lokalizację udziału plików, należy określić katalog do użycia przez środowisko uruchomieniowe usługi Azure Functions.

    ![Udział plików w wersji zapoznawczej w usłudze platformy Azure środowiska uruchomieniowego usługi Functions][10]

1. Kliknij przycisk **IIS** kartę.  Ta karta przedstawia szczegółowe informacje o witrynach sieci Web w usługach IIS, który tworzy za pomocą narzędzia konfiguracji środowiska uruchomieniowego usługi Azure Functions.  Można określić niestandardową nazwę DNS w tym miejscu dla portalu w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions.  Kliknij przycisk **Zastosuj** do ukończenia.

    ![Azure Functions Runtime — wersja zapoznawcza usług IIS][11]

1. Kliknij przycisk **usług** kartę.  Ta karta przedstawia stan usługi w narzędziu konfiguracji środowiska uruchomieniowego usługi Azure Functions.  Jeśli **usługi aktywacji hosta funkcji Azure** jest nie działa po wykonaniu konfiguracji początkowej, kliknij przycisk **Uruchom usługę**.

    ![Środowisko uruchomieniowe usługi Functions (wersja zapoznawcza) konfiguracji ukończone][12]

1. Przejdź do **Portal środowiska uruchomieniowego usługi Azure Functions** jako `https://<machinename>.<domain>/`.

    ![Azure portal w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Tworzenie pierwszej funkcji w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions

Tworzenie pierwszej funkcji w wersji zapoznawczej środowisko uruchomieniowe usługi Azure Functions

1. Przejdź do **Portal środowiska uruchomieniowego usługi Azure Functions** jako `https://<machinename>.<domain>` na przykład `https://mycomputer.mydomain.com`.

1. Zostanie wyświetlony monit o **Zaloguj**, jeśli wdrożono używane domeny, nazwę domeny konta użytkownika i hasło, w przeciwnym razie użyj nazwę lokalnego konta użytkownika i hasło, aby zalogować się do portalu.

    ![Środowisko uruchomieniowe usługi Functions w wersji zapoznawczej portalu logowanie do platformy Azure][14]

1. Aby utworzyć aplikację funkcji, należy utworzyć subskrypcję.  W lewym górnym rogu portalu kliknij **+** opcję obok subskrypcje.

    ![Subskrypcje platformy Azure środowiska uruchomieniowego usługi Functions w wersji zapoznawczej portalu][15]

1. Wybierz **DefaultPlan**, wprowadź nazwę dla Twojej subskrypcji i kliknij przycisk **Utwórz**.

    ![Plan subskrypcji w portalu w wersji zapoznawczej w usłudze Azure środowisko uruchomieniowe usługi Functions i nazwa][16]

1. Wszystkie Twoje aplikacje funkcji są wyświetlane w okienku po lewej stronie portalu.  Aby utworzyć nową aplikację funkcji, wybierz nagłówek **aplikacje funkcji** i kliknij przycisk **+** opcji.

1. Wprowadź nazwę aplikacji funkcji, wybierz poprawną subskrypcję, którą wersję środowiska uruchomieniowego usługi Azure Functions, aby programować przy użyciu, a następnie kliknij przycisk Wybierz **Create**

    ![Usługa Azure środowisko uruchomieniowe usługi Functions w wersji zapoznawczej portalu nowej aplikacji funkcji][17]

1. Nową aplikację funkcji znajduje się w okienku po lewej stronie portalu.  Wybierz funkcje, a następnie kliknij przycisk **nową funkcję** w górnej części okienka Centrum w portalu.

    ![Szablony platformy Azure w wersji zapoznawczej środowiska uruchomieniowego usługi Functions][18]

1. Wybierz funkcję wyzwalacza czasomierza, w menu po prawej stronie wysuwane nadaj nazwę funkcji i Zmień harmonogram `*/5 * * * * *` (to wyrażenie cron powoduje, że funkcję czasomierza, aby wykonać co pięć sekund) i kliknij przycisk **Create**

    ![Konfiguracji platformy Azure środowiska uruchomieniowego usługi Functions w wersji zapoznawczej nowego czasomierza — funkcja][19]

1. Funkcja została utworzona.  Dziennik wykonywania aplikacji funkcji można wyświetlić, rozwijając **dziennika** okienku u dołu portalu.

    ![Wykonywanie funkcji w wersji zapoznawczej środowiska uruchomieniowego usługi Functions platformy Azure][20]

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
