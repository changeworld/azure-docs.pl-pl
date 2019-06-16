---
title: Otrzymasz wiadomość e-mail po otwarciu drzwi biblioteki za pomocą usługi SendGrid i Azure Functions | Dokumentacja firmy Microsoft
description: Monitorowanie czujnika magnetycznego Wykryj, kiedy drzwi są otwarte i Wyślij powiadomienie e-mail za pomocą usługi Azure Functions.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61370360"
---
# <a name="door-monitor"></a>Monitor drzwi biblioteki          

Zestawu deweloperskiego IoT Mxchip zawiera wbudowane czujnika magnetycznego. W tym projekcie można wykryć obecności lub braku pobliskich silne pola magnetycznego — w tym przypadku pochodzące z małą magnesami.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak wykryć przepływu pobliskich magnes za pomocą zestawu deweloperskiego IoT Mxchip firmy czujnika magnetycznego.
- Jak używać usługi SendGrid, aby wysłać powiadomienie na adres e-mail.

> [!NOTE]
> Aby uzyskać praktyczne wykorzystanie tego projektu należy wykonać następujące zadania:
> - Zainstaluj magnes na urządzenia brzegowe drzwi biblioteki.
> - Zainstaluj Mxchip na posiadanego drzwi blisko magnes. Otwierających ani zamykających nawiasów drzwi spowoduje wyzwolenie czujnika skutkuje otrzymywanie wiadomości e-mail z powiadomieniem zdarzenia.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [przewodnik wprowadzenie](iot-hub-arduino-iot-devkit-az3166-get-started.md) do:

* Twoje Mxchip nawiązano połączenie z sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować się za pomocą jednej z następujących metod:

* Aktywuj [bezpłatnej 30-dniowej wersji próbnej konta systemu Microsoft Azure](https://azure.microsoft.com/free/).
* Oświadczenie użytkownika [platformy Azure w wysokości](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Jeśli jesteś subskrybentem MSDN lub Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Wdrażanie usługi SendGrid w systemie Azure

[Usługa SendGrid](https://sendgrid.com/) to platforma dostarczania oparte na chmurze wiadomości e-mail. Ta usługa będzie służyć do wysyłania powiadomień e-mail.

> [!NOTE]
> Jeśli masz już wdrożone usługi SendGrid, można przejść bezpośrednio do [wdrażanie Centrum IoT na platformie Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Wdrożenie usługi SendGrid

Aby zainicjować obsługę usług platformy Azure, należy użyć **Wdróż na platformie Azure** przycisku. Ten przycisk umożliwia szybkie i łatwe wdrażanie projektów typu open source platformy Microsoft Azure.

Kliknij przycisk **Wdróż na platformie Azure** przycisk poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Jeśli użytkownik nie jest już zarejestrowany na koncie platformy Azure, zarejestruj się teraz. 

Zobaczysz teraz formularz rejestracji usługi SendGrid.

![Wdrożenie usługi SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Wypełnij formularz rejestracji:

   * **Grupa zasobów**: Utwórz grupę zasobów do obsługi usługi SendGrid, lub użyj istniejącej. Zobacz [używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nazwa**: Nazwa usługi SendGrid. Wybierz unikatową nazwę, różniące się od innych usług, które mogą wiązać Ciebie.

   * **Hasło**: Usługa wymaga hasła, które nie będą używane dla wszystkich elementów, w tym projekcie.

   * **adres e-mail**: Usługa SendGrid wyśle weryfikacji na ten adres e-mail.

Sprawdź **Przypnij do pulpitu nawigacyjnego** opcji w celu ułatwienia tej aplikacji można znaleźć w przyszłości, a następnie kliknij przycisk **zakupu** można przesłać formularza logowania.
 
### <a name="sendgrid-api-key-creation"></a>Tworzenie klucza interfejsu API usługi SendGrid

Po zakończeniu wdrożenia, kliknij go, a następnie kliknij przycisk **Zarządzaj** przycisku. Pojawi się strony swojego konta usługi SendGrid, gdzie musisz zweryfikować swój adres e-mail.

![Zarządzanie usługi SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stronie usługi SendGrid, kliknij **ustawienia** > **klucze interfejsu API** > **Utwórz klucz interfejsu API**.

![Najpierw Utwórz interfejs API usługi SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na **Utwórz klucz interfejsu API** strony, wprowadź **klucz interfejsu API o nazwie** i kliknij przycisk **widoku & Utwórz**.

![Po drugie Tworzenie interfejsu API usługi SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klucz interfejsu API jest wyświetlany tylko jeden raz. Pamiętaj skopiować i zapisać ją bezpiecznie, ponieważ jest używany w następnym kroku.

## <a name="deploy-iot-hub-in-azure"></a>Wdrażanie usługi IoT Hub na platformie Azure

Poniższe kroki przydzieli inne usługi Azure IoT związane z usługami i wdrażanie usługi Azure Functions dla tego projektu.

Kliknij przycisk **Wdróż na platformie Azure** przycisk poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zostanie wyświetlony formularz rejestracji.

![Wdrożenie usługi IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Wypełnij pola w formularzu tworzenia konta.

   * **Grupa zasobów**: Utwórz grupę zasobów do obsługi usługi SendGrid, lub użyj istniejącej. Zobacz [używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Nazwa centrum iot Hub**: Nazwa centrum IoT. Wybierz unikatową nazwę, różniące się od innych usług, które mogą wiązać Ciebie.

   * **Jednostka Sku usługi iot Hub**: F1 (maksymalnie jedno na subskrypcję) jest bezpłatne. Więcej informacji o cenach można wyświetlić na [stronę z cennikiem](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Wiadomości E-mail z**: To pole powinno być tego samego adresu e-mail, którego użyto podczas konfigurowania usługi SendGrid.

Sprawdź **Przypnij do pulpitu nawigacyjnego** opcji w celu ułatwienia tej aplikacji można znaleźć w przyszłości, a następnie kliknij przycisk **zakupu** kiedy wszystko będzie gotowe przejść do następnego kroku.
 
## <a name="build-and-upload-the-code"></a>Tworzenie i przekazywanie kodu

Następnie załaduj przykładowego kodu w programie VS Code i aprowizować niezbędnych usług platformy Azure.

### <a name="start-vs-code"></a>Uruchom program VS Code

- Upewnij się, jest Twoja Mxchip **nie** podłączone do komputera.
- Uruchom program VS Code.
- Podłącz Mxchip do komputera.

> [!NOTE]
> Po uruchomieniu programu VS Code, może zostać wyświetlony komunikat o błędzie informujący, że nie można znaleźć w środowisku IDE Arduino lub pakietu powiązane tablicy. Jeśli zostanie wyświetlony ten błąd, zamknij program VS Code, uruchom ponownie IDE Arduino i program VS Code powinna zlokalizować środowisku IDE Arduino ścieżkę poprawnie.

### <a name="open-arduino-examples-folder"></a>Otwórz folder przykłady Arduino

Po lewej stronie rozwiń **przykłady ARDUINO** sekcji, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi > AzureIoT**i wybierz **DoorMonitor**. Ta akcja powoduje otwarcie nowego okna programu VS Code z folderu projektu w nim.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Przykładowa aplikacja można również otworzyć z palety poleceń. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

### <a name="provision-azure-services"></a>Aprowizacja usług platformy Azure

W oknie rozwiązania Uruchom chmury inicjowania obsługi zadań:
- Typ `Ctrl+P` (z systemem macOS: `Cmd+P`).
- Wprowadź `task cloud-provision` w polu tekstowym podana.

W terminalu programu VS Code interaktywne wiersza polecenia przeprowadzi Cię przez Inicjowanie obsługi administracyjnej wymaganych usług platformy Azure. Zaznacz wszystkie tych samych towarów z monitem listy, która wcześniej aprowizowane w [wdrażanie Centrum IoT na platformie Azure](#deploy-iot-hub-in-azure).

![Aprowizowanie chmury](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Jeśli strony stanu ładowania, zawiesza się podczas próby logowanie do platformy Azure, zapoznaj się ["page miany podczas logowania się w" sekcji często zadawanych pytań IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) Aby rozwiązać ten problem. 

### <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

Następnie Przekaż kod dla urządzenia.

#### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.

2. Naciśnij w terminalu wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony numer identyfikacyjny Mxchip oraz słowo *konfiguracji*.

#### <a name="macos"></a>macOS

1. Umieść Mxchip tryb konfiguracji: Przytrzymaj naciśnięty przycisk A, a następnie Wypchnij, a następnie zwolnij przycisk resetowania. Na ekranie są wyświetlane "Konfiguracja".

2. Kliknij przycisk `Cmd+P` do uruchomienia `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Sprawdź, przekazywanie i uruchamianie przykładowej aplikacji

Parametry połączenia, które są pobierane z [świadczenia usługi Azure services](#provision-azure-services) kroku jest teraz skonfigurowane. 

Program VS Code, a następnie rozpoczyna się weryfikowanie i przekazywanie Arduino szkic do Mxchip.

![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Mxchip rozruchu i uruchamiania kodu.

> [!NOTE]
> Czasami może pojawić się "Błąd: AZ3166: Nieznany pakiet"komunikat o błędzie. Ten błąd występuje, gdy indeksu pakietów tablicy nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się [sekcji Projektowanie Mxchip IoT często zadawanych pytań](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

Program najpierw inicjuje po Mxchip obecności stabilne pola magnetycznego.

Po zainicjowaniu `Door closed` jest wyświetlany na ekranie. W przypadku zmiany pola magnetycznego stan zmieni się na `Door opened`. Każdym drzwi zmiany stanu, otrzymasz wiadomość e-mail z powiadomieniem. (Te wiadomości e-mail może potrwać do pięciu minut do odebrania).

![Pól blisko czujnika: Drzwi zamknięte](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "pól blisko czujnika: Drzwi zamknięte")

![Magnes odsunąć od czujnika: Drzwi Opened](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "magnes odsunąć od czujnika: Otworzyć drzwi biblioteki")

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub połączyć się przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Pokazaliśmy, jak połączyć urządzenie Mxchip Twój akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT i usługi SendGrid umożliwia wysyłanie wiadomości e-mail. Oto zalecane kolejne kroki:

* [Omówienie usługi Azure akcelerator rozwiązań IoT zdalnego monitorowania](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
