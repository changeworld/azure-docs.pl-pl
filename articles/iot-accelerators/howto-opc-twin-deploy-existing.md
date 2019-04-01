---
title: Sposób wdrażania modułu zarządzania urządzeniami Azure IoT OPC UA do istniejącego projektu | Dokumentacja firmy Microsoft
description: Jak wdrożyć bliźniaczej reprezentacji OPC do istniejącego projektu.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: da11d4977cc998c9fccb3628a44e45db95c7d977
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759536"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Wdrażanie bliźniaczej reprezentacji OPC do istniejącego projektu

Moduł bliźniaczej reprezentacji urządzenia OPC jest uruchamiany w usłudze IoT Edge i udostępnia usługi edge do bliźniaczej reprezentacji urządzenia OPC i rejestr usług. 

Bliźniacza reprezentacja urządzenia OPC wczesnych usługa ułatwia tworzenie komunikacji między operatorami fabryki i urządzeń serwerów OPC UA na etapie produkcji przy użyciu moduł usługi IoT Edge w bazie wiedzy OPC bliźniaczej reprezentacji. Usługa wczesnych przedstawia usług OPC UA (przeglądania, odczytu, zapisu i wykonania) za pośrednictwem jego interfejsu API REST. 

Usługa wczesnych OPC UA urządzenia rejestru zapewnia dostęp do zarejestrowanych aplikacji serwera OPC UA i ich punktami końcowymi. Operatorzy i Administratorzy można rejestrować i wyrejestrować nowe aplikacje serwera OPC UA i przeglądanie istniejących, łącznie z ich punkty końcowe. Oprócz aplikacji i punkt końcowy zarządzania usługi rejestru Skatalogowano zarejestrowanych OPC urządzenia bliźniaczej reprezentacji moduły usługi IoT Edge. Interfejs API usługi zapewnia kontrolę nad moduł funkcji, na przykład uruchamianie lub zatrzymywanie odnajdywaniem serwera (skanowania usługi) lub aktywowanie nowego bliźniaczych reprezentacji punktu końcowego, które mogą być udostępniane przy użyciu usługi wczesnych OPC bliźniaczej reprezentacji.

Podstawowy moduł jest tożsamością kierownika. Nadzorca zarządza bliźniaczej reprezentacji punktu końcowego, który odnosi się do punktów końcowych serwera OPC UA, które są aktywowane przy użyciu odpowiedniego rejestru OPC UA interfejsu API. Ten punkt końcowy twins wykonuje translację elementu OPC UA JSON otrzymanych z usługi wczesnych bliźniaczej reprezentacji OPC działające w chmurze do wiadomości binarnych serwera OPC UA, które są wysyłane za pośrednictwem stanowych bezpiecznego kanału do zarządzanego punktu końcowego. Nadzorca udostępnia również usługi odnajdywania, wysyłających zdarzenia odnajdywania urządzenia w usłudze OPC UA urządzenia dołączania do przetworzenia, w których te zdarzenia spowodować aktualizacje w rejestrze serwera OPC UA.  W tym artykule przedstawiono sposób wdrażania modułu bliźniaczej reprezentacji OPC do istniejącego projektu. 

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrażania oraz instrukcje, zobacz GitHub [repozytorium](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Uwaga: Ze względu na zależność względem modułu AzureRM wdrażania aktualnie jest obsługiwana tylko na Windows.

Upewnij się, że program PowerShell został i [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) zainstalowanych rozszerzeń.   Jeśli użytkownik nie zostało zrobione jeszcze, sklonuj to repozytorium serwisu GitHub.  Otwórz wiersz polecenia lub terminalu i uruchom:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Wdrażanie usług przemysłowych IoT na platformie Azure

1. Otwórz wiersz polecenia lub terminalu Uruchom:

   ```bash
   deploy
   ```

2. Postępuj zgodnie z monitami, aby przypisać nazwy do wdrożenia grupy zasobów i nazwy do witryny sieci Web.   Skrypt wdraża się mikrousługi i ich zależności platformy Azure w wybranej grupie zasobów w subskrypcji platformy Azure.  Skrypt rejestruje także aplikację w dzierżawie usługi Azure Active Directory (AAD) do obsługi uwierzytelniania OAUTH.  Wdrożenie potrwa kilka minut.  Przykład widoczne po pomyślnym wdrożeniu rozwiązania:

   ![Przemysłowych Twin OPC IoT wdrażanie istniejącego projektu](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Dane wyjściowe obejmują adres URL punktu końcowego publicznego. 

3. Po pomyślnym ukończeniu działania skryptu, wybierz, czy chcesz zapisać pliku env.  Będzie potrzebny plik środowiska ENV, jeśli chcesz nawiązać połączenie z punktem końcowym w chmurze za pomocą narzędzi takich jak konsola lub wdrożenie modułów na potrzeby programowania i debugowania.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów dotyczących niepowodzenia wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używasz nazwę grupy zasobów krótka i prosta.  Nazwa jest używana również do nazwy zasobów związku z tym musi być zgodne z wymaganiami w zakresie nazewnictwa zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny sieci Web już w użyciu

Istnieje możliwość, że nazwa witryny sieci Web jest już używany.  Jeśli napotkasz ten błąd, należy użyć nazwy innej aplikacji.

### <a name="azure-active-directory-aad-registration"></a>Rejestracja w usłudze Azure Active Directory (AAD)

Skrypt wdrażania próbuje zarejestrować dwie aplikacje usługi AAD w usłudze Azure Active Directory.  W zależności od prawa do wybranej dzierżawy usługi AAD wdrożenie może się nie powieść. Dostępne są dwie opcje:

1. Jeśli została wybrana opcja dzierżawy usługi AAD z listy dzierżaw, uruchom ponownie skrypt i wybrać inny z listy.
2. Alternatywnie wdrażanie prywatnego dzierżawy usługi AAD w innej subskrypcji, uruchom ponownie skrypt i wybrać z niej korzystać.

> [!WARNING]
> Nigdy nie jest kontynuowane bez uwierzytelniania.  Jeśli wybierzesz to zrobić, każdy dostęp do punktów końcowych zarządzania urządzeniami OPC z Internetu, nieuwierzytelnione.   Zawsze możesz [opcji wdrożenia "local"](howto-opc-twin-deploy-dependencies.md) do testów laboratoryjnych.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Wdrażanie pokaz usługi IoT przemysłowych w jednym

Zamiast po prostu usług i zależności można także wdrożyć pokaz All-in-one.  Wszystko w jednym pokaz zawiera trzy serwery OPC UA, OPC bliźniaczej reprezentacji modułu, wszystkie mikrousługi i przykładowej aplikacji sieci Web.  Jest ona przeznaczona dla celów demonstracyjnych.

1. Upewnij się, że masz klon repozytorium (zobacz powyżej). Otwórz wiersz polecenia lub terminalu w katalogu głównym repozytorium i uruchomienia:

   ```bash
   deploy -type demo
   ```

2. Postępuj zgodnie z monitami, aby przypisać nazwę nowej grupy zasobów i nazwę witryny sieci Web.  Po pomyślnym wdrożeniu, skrypt wyświetli adres URL punktu końcowego aplikacji sieci web.

## <a name="deployment-script-options"></a>Opcje skryptu wdrożenia

Skrypt przyjmuje następujące parametry:

```bash
-type
```

Typ wdrożenia (pokaz lokalnych, maszyn wirtualnych)

```bash
-resourceGroupName
```

Może być nazwą istniejącej lub nowej grupy zasobów.

```bash
-subscriptionId
```

Opcjonalne, identyfikator subskrypcji, w którym będą wdrażane zasoby.

```bash
-subscriptionName
```

Lub nazwy subskrypcji.

```bash
-resourceGroupLocation
```

Opcjonalne, lokalizację grupy zasobów. Jeśli zostanie określony, spróbuje utworzyć nową grupę zasobów w tej lokalizacji.

```bash
-aadApplicationName
```

Nazwa aplikacji usługi AAD zarejestrować się w obszarze. 

```bash
-tenantId
```

Dzierżawy usługi AAD do użycia.

```bash
-credentials
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć bliźniaczej reprezentacji OPC do istniejącego projektu, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja klienta OPC i OPC PLC ](howto-opc-vault-deploy-existing-client-plc-communication.md)