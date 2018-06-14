---
title: Azure Functions instalacji środowiska uruchomieniowego | Dokumentacja firmy Microsoft
description: Jak zainstalować środowisko uruchomieniowe Functions Azure preview 2
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290661"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Zainstaluj środowisko uruchomieniowe Functions Azure preview 2

Jeśli chcesz zainstalować środowisko uruchomieniowe Functions Azure preview 2, wykonaj następujące kroki:

1. Upewnij się, że komputer przekazuje wymagania minimalne.
1. Pobierz [Azure Functions środowiska wykonawczego w wersji zapoznawczej Instalatora](https://aka.ms/azafrv2).
1. Odinstaluj Podgląd środowisko uruchomieniowe Functions Azure 1.
1. Zainstaluj środowisko uruchomieniowe Functions Azure preview 2.
1. Zakończ konfigurację środowiska uruchomieniowego funkcji Azure Preview 2.
1. Tworzenie pierwszej funkcji w wersji zapoznawczej środowiska uruchomieniowego funkcji platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Aby zainstalować środowisko uruchomieniowe Functions Azure w wersji zapoznawczej, musi mieć następujące zasoby dostępne:

1. Maszynie z systemem Microsoft Windows Server 2016 lub Microsoft Windows 10 twórców aktualizacji (Professional lub Enterprise Edition).
1. Wystąpienie programu SQL Server działających w sieci.  Minimalna wersja wymagana jest programu SQL Server Express.

## <a name="uninstall-previous-version"></a>Odinstaluj poprzednią wersję

Jeśli wcześniej zainstalowano środowisko uruchomieniowe Functions Azure w wersji zapoznawczej, należy odinstalować przed zainstalowaniem najnowszej wersji.  Odinstaluj Podgląd środowisko uruchomieniowe Functions Azure przez usunięcie programu w aplecie Dodaj lub usuń programy w systemie Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Zainstaluj usługę Azure Functions podglądu środowiska wykonawczego

Instalator podglądu środowiska uruchomieniowego funkcji Azure przeprowadzi Cię przez instalację środowisko uruchomieniowe Functions Azure w wersji zapoznawczej zarządzania i roli proces roboczy.  Istnieje możliwość zainstalowania roli zarządzania i proces roboczy na tym samym komputerze.  Jednak podczas dodawania więcej funkcji aplikacji, należy wdrożyć jedną rolę procesu roboczego na dodatkowych komputerach możliwość skalowania funkcji na wielu pracowników.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Zainstaluj na tym samym komputerze zarządzania i roli procesu roboczego

1. Uruchom Instalatora programu Azure Functions środowiska wykonawczego w wersji zapoznawczej.

    ![Azure Instalatora w wersji zapoznawczej funkcji środowiska uruchomieniowego][1]

1. Kliknij przycisk **Dalej**.
1. Po przeczytaniu warunki **umowy licencyjnej**, **zaznacz pole wyboru** Akceptuję postanowienia, a następnie kliknij przycisk **dalej** można poprawić.
1. Wybierz role, którą chcesz zainstalować na tym komputerze **funkcje zarządzania roli** i/lub **roli procesu roboczego funkcji** i kliknij przycisk **dalej**.

    ![Instalator Azure w wersji zapoznawczej funkcji środowiska uruchomieniowego — Wybór roli][3]

    > [!NOTE]
    > Można zainstalować **roli procesu roboczego funkcji** na innych komputerach. Aby to zrobić, wykonaj te instrukcje, a jedynie opcję **roli procesu roboczego funkcji** w Instalatorze.

1. Kliknij przycisk **dalej** mają **Kreatora instalacji programu Azure funkcji środowiska uruchomieniowego** rozpocząć proces instalacji na tym komputerze.
1. Po wykonaniu tych czynności kreatora instalacji uruchamia **środowisko uruchomieniowe Functions Azure** narzędzia do konfiguracji.

    ![Zakończenie Azure Instalatora w wersji zapoznawczej funkcji środowiska uruchomieniowego][6]

    > [!NOTE]
    > Jeśli instalujesz na **systemu Windows 10** i **kontenera** funkcji nie został wcześniej włączony, **instalacja środowiska uruchomieniowego funkcji Azure** wyświetli monit o ponowny rozruch komputera Aby zakończyć instalację.

## <a name="configure-the-azure-functions-runtime"></a>Skonfiguruj usługę Azure Functions środowiska uruchomieniowego

Aby ukończyć instalację środowisko uruchomieniowe Functions Azure, należy wykonać konfigurację.

1. **Środowisko uruchomieniowe Functions Azure** zawiera narzędzia do konfiguracji role są zainstalowane na tym komputerze.

    ![Narzędzia do konfiguracji Azure w wersji zapoznawczej funkcji środowiska uruchomieniowego][7]

1. Kliknij przycisk **bazy danych** wprowadź szczegóły połączenia dla wystąpienia programu SQL Server, łącznie z określeniem [klucz główny bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)i kliknij przycisk **Zastosuj**.  Łączność z wystąpieniem programu SQL Server jest wymagany w celu obsługi funkcji Azure utworzenie bazy danych obsługuje środowisko uruchomieniowe.

    ![Azure środowisko uruchomieniowe Functions Podgląd bazy danych konfiguracji][8]

1. Kliknij przycisk **poświadczenia** kartę.  W tym miejscu należy utworzyć dwa nowe poświadczenia do użycia z udziałem plików do obsługi wszystkich funkcji aplikacji.  Określ **nazwy użytkownika** i **hasło** kombinacji dla **właściciela udziału pliku** i **użytkownika udziału pliku**, kliknij przycisk **Zastosuj**.

    ![Poświadczenia Azure w wersji zapoznawczej funkcji środowiska uruchomieniowego][9]

1. Kliknij przycisk **udziału plików** kartę.  W tym miejscu podaj szczegóły lokalizację udziału plików.  Można utworzyć udziału plików dla Ciebie lub można użyć istniejącego udziału plików i kliknij przycisk **Zastosuj**.  Wybierz nową lokalizację udziału plików, należy określić katalog do użycia przez środowisko uruchomieniowe Functions Azure.

    ![Azure udziału plików w wersji zapoznawczej funkcji środowiska uruchomieniowego][10]

1. Kliknij przycisk **IIS** kartę.  Ta karta przedstawia szczegóły witryn sieci Web w usługach IIS tworzonego przez środowisko uruchomieniowe Functions Azure narzędzie konfiguracji.  Można określić niestandardowe nazwy DNS tutaj dla portalu środowisko uruchomieniowe Functions Azure w wersji zapoznawczej.  Kliknij przycisk **Zastosuj** do wykonania.

    ![Azure podglądu środowiska uruchomieniowego funkcje usług IIS][11]

1. Kliknij przycisk **usług** kartę.  Ta karta przedstawia stan usługi narzędzia konfiguracji programu obsługi funkcji Azure.  Jeśli **usługi aktywacji hosta funkcji Azure** jest nie działa po wykonaniu konfiguracji początkowej, kliknij przycisk **Uruchom usługę**.

    ![Środowisko uruchomieniowe Functions Podgląd Konfiguracja programu Azure ukończone][12]

1. Przejdź do **Azure Functions portalu środowiska uruchomieniowego** jako `https://<machinename>.<domain>/`.

    ![Portal Azure w wersji zapoznawczej funkcji środowiska uruchomieniowego][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Tworzenie pierwszej funkcji w środowisku uruchomieniowym funkcji Azure w wersji zapoznawczej

Aby utworzyć swoją pierwszą funkcję w środowisku uruchomieniowym funkcji Azure w wersji zapoznawczej

1. Przejdź do **Azure Functions portalu środowiska uruchomieniowego** jako https://<machinename>.<domain> na przykład https://mycomputer.mydomain.com
1. Zostanie wyświetlony monit o **Zaloguj**, w przypadku wdrażania w Użyj domeny, nazwę domeny konta użytkownika i hasło, w przeciwnym razie użyj nazwę lokalnego konta użytkownika i hasło, aby zalogować się do portalu.

![Środowisko uruchomieniowe Functions wersji zapoznawczej portalu logowania do systemu Azure][14]

1. Do tworzenia aplikacji dla funkcji, należy utworzyć subskrypcję.  W lewym górnym rogu portalu kliknij  **+**  opcję obok subskrypcji

![Subskrypcje platformy Azure środowiska uruchomieniowego funkcje wersji zapoznawczej portalu][15]

1. Wybierz **DefaultPlan**, wprowadź nazwę dla Twojej subskrypcji i kliknij przycisk **Utwórz**.

![Azure funkcje środowiska wykonawczego w wersji zapoznawczej portalu subskrypcji i nazwy][16]

1. Wszystkich funkcji aplikacji są wyświetlane w okienku po lewej stronie portalu.  Aby utworzyć nową aplikację funkcji, wybierz nagłówek **aplikacji funkcji** i kliknij przycisk  **+**  opcji.

1. Wprowadź nazwę dla aplikacji funkcja wybierz poprawną subskrypcję, wybrać wersję środowiska uruchomieniowego usługi Azure Functions do programów przed, a następnie kliknij przycisk **Utwórz**

![Środowisko uruchomieniowe Functions Podgląd portalu nowych funkcji aplikacji Azure][17]

1. Nowej aplikacji funkcji znajduje się w okienku po lewej stronie portalu.  Wybierz funkcje, a następnie kliknij przycisk **nową funkcję** u góry w środkowym okienku w portalu.

![Szablony usługi Azure w wersji zapoznawczej funkcji środowiska uruchomieniowego][18]

1. Wybierz funkcję wyzwalacza czasomierza, w wysuwane po prawej stronie nazwy funkcji i Zmień harmonogram `*/5 * * * * *` (tego wyrażenia cron powoduje, że funkcja czasomierza wykonanie co pięć sekund) i kliknij przycisk **Utwórz**

![Konfiguracji platformy Azure środowisko uruchomieniowe Functions Podgląd nowego czasomierza — funkcja][19]

1. Funkcja została utworzona.  Dziennik wykonywania aplikacji funkcji można wyświetlić, rozwijając **dziennika** okienko w dolnej części portalu.

![Wykonywanie funkcji podglądu środowisko uruchomieniowe Functions Azure][20]

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
