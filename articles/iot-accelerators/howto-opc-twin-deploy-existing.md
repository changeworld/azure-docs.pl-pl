---
title: Jak wdrożyć moduł OPC Twin w istniejącym projekcie platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wdrażania programu OPC Twin w istniejącym projekcie. Można również dowiedzieć się, jak rozwiązywać problemy z błędami wdrażania.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824116"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Wdrażanie programu OPC Twin w istniejącym projekcie

Moduł OPC Twin działa na ioT Edge i zapewnia kilka usług brzegowych usług OPC Twin i Registry.

Mikrousługa OPC Twin ułatwia komunikację między operatorami fabryki a urządzeniami serwerowymi OPC UA w hali produkcyjnej za pośrednictwem modułu OPC Twin IoT Edge. Mikrousługa udostępnia usługi OPC UA (przeglądaj, odczytuj, zapisuj i realizuj) za pośrednictwem interfejsu API REST. 

Mikrousługa rejestru urządzeń OPC UA zapewnia dostęp do zarejestrowanych aplikacji OPC UA i ich punktów końcowych. Operatorzy i administratorzy mogą rejestrować i wyrymazyć nowe aplikacje OPC UA i przeglądać istniejące, w tym ich punkty końcowe. Oprócz zarządzania aplikacjami i punktami końcowymi usługa rejestru kataloguje również zarejestrowane moduły OPC Twin IoT Edge. Interfejs API usługi zapewnia kontrolę nad funkcjami modułu brzegowego, na przykład uruchamianie lub zatrzymywanie odnajdowania serwera (usługi skanowania) lub aktywowanie nowych bliźniaczych reprezentacji punktu końcowego, do których można uzyskać dostęp za pomocą mikrousługi OPC Twin.

Rdzeniem modułu jest tożsamość przełożonego. Nadzorca zarządza bliźniaczej reprezentacji punktu końcowego, która odpowiada punktom końcowym serwera OPC UA, które są aktywowane przy użyciu odpowiedniego interfejsu API rejestru OPC UA. Ten punkt końcowy bliźniaczych reprezentacji przetłumaczyć OPC UA JSON odebrane z OPC Twin mikrousługi uruchomionej w chmurze do OPC UA komunikatów binarnych, które są wysyłane za pomocą stanowego bezpiecznego kanału do zarządzanego punktu końcowego. Nadzorca udostępnia również usługi odnajdywania, które wysyłają zdarzenia odnajdywania urządzeń do usługi dołączania urządzenia OPC UA do przetwarzania, gdzie te zdarzenia powodują aktualizacje rejestru UA OPC.  W tym artykule pokazano, jak wdrożyć moduł OPC Twin w istniejącym projekcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrażania i instrukcji, zobacz [repozytorium](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że masz zainstalowane rozszerzenia [programu PowerShell i AzureRM PowerShell.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) Jeśli jeszcze tego nie zrobiono, sklonuj to repozytorium GitHub. Uruchom następujące polecenia w programie PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Wdrażanie przemysłowych usług IoT na platformie Azure

1. W sesji programu PowerShell uruchom:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Postępuj zgodnie z monitami, aby przypisać nazwę do grupy zasobów wdrożenia i nazwę do witryny sieci Web.   Skrypt wdraża mikrousługi i ich zależności platformy Azure do grupy zasobów w subskrypcji platformy Azure.  Skrypt rejestruje również aplikację w dzierżawie usługi Azure Active Directory (AAD) do obsługi uwierzytelniania opartego na technologii OAUTH.  Wdrożenie potrwa kilka minut.  Przykład tego, co zobaczysz po pomyślnym wdrożeniu rozwiązania:

   ![Przemysłowe IoT OPC Twin wdrożyć do istniejącego projektu](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Dane wyjściowe zawiera adres URL publicznego punktu końcowego. 

3. Po pomyślnym zakończeniu pracy skryptu wybierz, `.env` czy chcesz zapisać plik.  Potrzebny jest `.env` plik środowiska, jeśli chcesz połączyć się z punktem końcowym chmury przy użyciu narzędzi, takich jak konsola lub wdrożyć moduły do zarządzania i debugowania.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrażania

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używasz krótkiej i prostej nazwy grupy zasobów.  Nazwa jest również używana do nazwania zasobów jako takich, które muszą być zgodne z wymaganiami dotyczącymi nazewnictwa zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny już używana

Możliwe, że nazwa strony internetowej jest już używana.  Jeśli napotkasz ten błąd, musisz użyć innej nazwy aplikacji.

### <a name="azure-active-directory-aad-registration"></a>Rejestracja usługi Azure Active Directory (AAD)

Skrypt wdrażania próbuje zarejestrować dwie aplikacje usługi AAD w usłudze Azure Active Directory.  W zależności od praw do wybranej dzierżawy usługi AAD wdrożenie może zakończyć się niepowodzeniem. Dostępne są dwie opcje:

1. Jeśli wybierzesz dzierżawę usługi AAD z listy dzierżaw, uruchom ponownie skrypt i wybierz inny z listy.
2. Alternatywnie wdrożyć prywatną dzierżawę usługi AAD w innej subskrypcji, uruchom ponownie skrypt i wybierz, aby go użyć.

> [!WARNING]
> NIGDY nie kontynuuj bez uwierzytelniania.  Jeśli zdecydujesz się to zrobić, każdy może uzyskać dostęp do punktów końcowych OPC Twin z Internetu nieuwierzyte.   Zawsze możesz wybrać [opcję "lokalnego" wdrażania,](howto-opc-twin-deploy-dependencies.md) aby kopnąć opony.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Wdrażanie demonstracyjnego przemysłowych usług IoT typu "wszystko w jednym"

Zamiast tylko usług i zależności można również wdrożyć demo wszystko w jednym.  Wszystkie w jednym demo zawiera trzy serwery OPC UA, moduł OPC Twin, wszystkie mikrousługi i przykładowej aplikacji sieci Web.  Jest przeznaczony do celów demonstracyjnych.

1. Upewnij się, że masz klon repozytorium (patrz wyżej). Otwórz monit programu PowerShell w katalogu głównym repozytorium i uruchom:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Postępuj zgodnie z monitami, aby przypisać nową nazwę do grupy zasobów i nazwę do witryny sieci Web.  Po pomyślnym wdrożeniu skrypt wyświetli adres URL punktu końcowego aplikacji sieci web.

## <a name="deployment-script-options"></a>Opcje skryptu wdrażania

Skrypt przyjmuje następujące parametry:

```powershell
-type
```

Typ wdrożenia (vm, local, demo)

```powershell
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```powershell
-subscriptionId
```

Opcjonalnie identyfikator subskrypcji, w którym zostaną wdrożone zasoby.

```powershell
-subscriptionName
```

Lub nazwę subskrypcji.

```powershell
-resourceGroupLocation
```

Opcjonalnie lokalizacja grupy zasobów. Jeśli zostanie określony, spróbuje utworzyć nową grupę zasobów w tej lokalizacji.

```powershell
-aadApplicationName
```

Nazwa aplikacji AAD do zarejestrowania się w obszarze.

```powershell
-tenantId
```

Dzierżawy usługi AAD do użycia.

```powershell
-credentials
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak wdrożyć OPC Twin do istniejącego projektu, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja klienta OPC UA i OPC UA PLC](howto-opc-vault-secure.md)
