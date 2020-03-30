---
title: Wysyłanie wiadomości e-mail po otwarciu drzwi przy użyciu usługi Azure Functions
description: Monitoruj czujnik magnetyczny, aby wykryć, kiedy drzwi są otwierane, i użyj usługi Azure Functions do wysyłania powiadomień e-mail.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977297"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor drzwi — przy użyciu funkcji azure i sendgrid, wysyłanie wiadomości e-mail po otwarciu drzwi           

MXChip IoT DevKit zawiera wbudowany czujnik magnetyczny. W tym projekcie wykrywa się obecność lub brak pobliskiego silnego pola magnetycznego - w tym przypadku pochodzącego z małego, trwałego magnesu.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie uczysz się:
- Jak używać czujnika magnetycznego MXChip IoT DevKit do wykrywania ruchu pobliskiego magnesu.
- Jak korzystać z usługi SendGrid, aby wysłać powiadomienie na swój adres e-mail.

> [!NOTE]
> Aby w praktyce wykorzystać ten projekt, wykonaj następujące zadania:
> - Zamontuj magnes na krawędzi drzwi.
> - Zamontuj DevKit na ościeżnicy drzwi w pobliżu magnesu. Otwarcie lub zamknięcie drzwi spowoduje uruchomienie czujnika, w wyniku czego zostanie wysłane powiadomienie e-mail o zdarzeniu.

## <a name="what-you-need"></a>Co jest potrzebne

Ukończ [przewodnik Wprowadzenie](iot-hub-arduino-iot-devkit-az3166-get-started.md) do:

* Podłącz devkit do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się za pomocą jednej z następujących metod:

* Aktywuj [bezpłatne 30-dniowe konto testowe platformy Microsoft Azure](https://azure.microsoft.com/free/).
* Ubiegaj się o [kredyt platformy Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jeśli jesteś subskrybentem usługi MSDN lub Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Wdrażanie usługi SendGrid na platformie Azure

[SendGrid](https://sendgrid.com/) to chmurowa platforma dostarczania wiadomości e-mail. Ta usługa będzie używana do wysyłania powiadomień e-mail.

> [!NOTE]
> Jeśli usługa SendGrid została już wdrożona, możesz przejść bezpośrednio do [usługi Wdrażanie usługi IoT Hub na platformie Azure.](#deploy-iot-hub-in-azure)

### <a name="sendgrid-deployment"></a>Wdrożenie SendGrid

Aby aprowizować usługi platformy Azure, użyj przycisku Wdrażanie na **platformie Azure.** Ten przycisk umożliwia szybkie i łatwe wdrażanie projektów typu open source na platformie Microsoft Azure.

Kliknij przycisk **Wdrażanie na platformie Azure** poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Jeśli nie jesteś jeszcze zalogowany do konta platformy Azure, zaloguj się teraz. 

Teraz zobaczysz formularz rejestracji SendGrid.

![Wdrożenie SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Wypełnij formularz rejestracji:

   * **Grupa zasobów:** Utwórz grupę zasobów do obsługi usługi SendGrid lub użyj istniejącej. Zobacz [Zarządzanie zasobami platformy Azure przy użyciu grup zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nazwa**: Nazwa usługi SendGrid. Wybierz unikatową nazwę, która różni się od innych usług, które możesz mieć.

   * **Hasło:** Usługa wymaga hasła, które nie będzie używane do niczego w tym projekcie.

   * **E-mail:** Usługa SendGrid wyśle weryfikację na ten adres e-mail.

Zaznacz opcję **Przypnij do pulpitu nawigacyjnego,** aby ułatwić znajdowanie tej aplikacji w przyszłości, a następnie kliknij przycisk **Kup,** aby przesłać formularz logowania.
 
### <a name="sendgrid-api-key-creation"></a>Tworzenie klucza interfejsu API SendGrid

Po zakończeniu wdrażania kliknij go, a następnie kliknij przycisk **Zarządzaj.** Zostanie wyświetlona strona konta SendGrid, na której musisz zweryfikować swój adres e-mail.

![Zarządzanie sendgridem](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stronie SendGrid kliknij pozycję **Ustawienia** > **kluczy** > interfejsu API**Tworzenie klucza INTERFEJSU API**.

![SendGrid Najpierw utwórz interfejs API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na stronie **Tworzenie klucza interfejsu API** wprowadź nazwę **klucza interfejsu API** i kliknij przycisk **Utwórz widok &**.

![SendGrid Tworzenie interfejsu API drugi](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klucz interfejsu API jest wyświetlany tylko jeden raz. Pamiętaj, aby skopiować i przechowywać go bezpiecznie, ponieważ jest używany w następnym kroku.

## <a name="deploy-iot-hub-in-azure"></a>Wdrażanie usługi IoT Hub na platformie Azure

Poniższe kroki zapewnią inne usługi związane z usługą Azure IoT i wy wdrożyą usługę Azure Functions dla tego projektu.

Kliknij przycisk **Wdrażanie na platformie Azure** poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Pojawi się formularz rejestracji.

![Wdrożenie usługi IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Wypełnij pola w formularzu rejestracji.

   * **Grupa zasobów:** Utwórz grupę zasobów do obsługi usługi SendGrid lub użyj istniejącej. Zobacz [Zarządzanie zasobami platformy Azure przy użyciu grup zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Nazwa centrum Iot:** nazwa centrum IoT Hub. Wybierz unikatową nazwę, która różni się od innych usług, które możesz mieć.

   * **Iot Hub Sku**: F1 (ograniczona do jednego na subskrypcję) jest bezpłatna. Więcej informacji o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Z adresu e-mail:** To pole powinno być tym samym adresem e-mail, który został użyty podczas konfigurowania usługi SendGrid.

Zaznacz opcję **Przypnij do pulpitu nawigacyjnego,** aby ułatwić znajdowanie tej aplikacji w przyszłości, a następnie kliknij pozycję **Kup,** gdy będziesz gotowy, aby przejść do następnego kroku.
 
## <a name="build-and-upload-the-code"></a>Tworzenie i przesyłanie kodu

Następnie należy załadować przykładowy kod w programie VS Code i aprowizować niezbędne usługi platformy Azure.

### <a name="start-vs-code"></a>Uruchom kod VS

- Upewnij się, że devkit **nie** jest podłączony do komputera.
- Uruchom program VS Code.
- Podłącz devkit do komputera.

> [!NOTE]
> Po uruchomieniu programu VS Code może pojawić się komunikat o błędzie informujący, że nie można znaleźć Arduino IDE lub powiązanego pakietu tablicy. Jeśli ten błąd, zamknij kod VS, uruchom Arduino IDE ponownie i VS Code należy zlokalizować ścieżkę Arduino IDE poprawnie.

### <a name="open-arduino-examples-folder"></a>Otwórz folder Przykłady Arduino

Rozwiń sekcję **ARDUINO EXAMPLES** po lewej stronie, przejdź do **przykładów dla programu MXCHIP AZ3166 > AzureIoT**i wybierz **opcję DoorMonitor**. Ta akcja otwiera nowe okno kodu programu VS z folderem projektu.

![przykłady mini-rozwiązania](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Przykładowej aplikacji można również otworzyć z palety poleceń. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: Przykłady**.

### <a name="provision-azure-services"></a>Aprowizuj usługi platformy Azure

W oknie rozwiązania uruchom zadanie inicjowania obsługi administracyjnej w chmurze:
- (macOS: `Ctrl+P` `Cmd+P`).
- Wprowadź `task cloud-provision` w podanym polu tekstowym.

W terminalu kodu VS interaktywny wiersz polecenia prowadzi użytkownika przez inicjowanie obsługi administracyjnej wymaganych usług platformy Azure. Wybierz wszystkie te same elementy z listy monitów, które zostały wcześniej zainicjowane w [usłudze Wdrażanie usługi IoT Hub na platformie Azure.](#deploy-iot-hub-in-azure)

![Aprowizuj chmurę](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Jeśli strona zawiesza się w stanie ładowania podczas próby zalogowania się na platformie Azure, zapoznaj się z [sekcją "strona zawiesza się podczas logowania" w często zadawanych pytaniach ioT DevKit,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) aby rozwiązać ten problem. 

### <a name="build-and-upload-the-device-code"></a>Tworzenie i przesyłanie kodu urządzenia

Następnie prześlij kod urządzenia.

#### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do `task device-upload`uruchomienia .

2. Terminal wyświetli monit o przełączenie do trybu konfiguracji. Aby to zrobić, przytrzymaj przycisk A, a następnie naciśnij i zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony numer identyfikacyjny DevKit i wyraz *Konfiguracja*.

#### <a name="macos"></a>macOS

1. Przełóż DevKit w tryb konfiguracji: przytrzymaj przycisk A, a następnie naciśnij i zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony komunikat "Konfiguracja".

2. Kliknij, `Cmd+P` `task device-upload`aby uruchomić plik .

#### <a name="verify-upload-and-run-the-sample-app"></a>Weryfikowanie, przekazywanie i uruchamianie przykładowej aplikacji

Ciąg połączenia, który jest pobierany z kroku [aprowizuj usługi platformy Azure](#provision-azure-services) jest teraz ustawiona. 

VS Code następnie rozpoczyna weryfikację i przekazywanie szkicu Arduino do DevKit.

![przesyłanie urządzenia](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit uruchamia się ponownie i uruchamia kod.

> [!NOTE]
> Od czasu do czasu może pojawić się komunikat o błędzie "Błąd: AZ3166: Nieznany pakiet". Ten błąd występuje, gdy indeks pakietu płyty nie jest odświeżany poprawnie. Aby rozwiązać ten błąd, zapoznaj się [z sekcją rozwoju często zadawanych pytań ioT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testowanie projektu

Program po raz pierwszy inicjuje, gdy DevKit jest w obecności stabilnego pola magnetycznego.

Po inicjalizacji jest `Door closed` wyświetlany na ekranie. Gdy nawęzchłe jest zmiana `Door opened`pola magnetycznego, stan zmienia się na . Za każdym razem, gdy stan drzwi się zmienia, otrzymasz powiadomienie e-mail. (Odebranie tych wiadomości e-mail może potrwać do pięciu minut).

![Magnesy w pobliżu czujnika: Drzwi zamknięte](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnesy w pobliżu czujnika: Drzwi zamknięte")

![Magnes odsunięty od czujnika: Drzwi otwarte](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnes odsunięty od czujnika: Drzwi otwarte")

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z [często zadawanymi pytaniami dotyczącymi programu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub połącz się z następującymi kanałami:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak połączyć urządzenie DevKit z akceleratorem rozwiązań zdalnego monitorowania usługi Azure IoT i wysłać wiadomość e-mail z usługą SendGrid. Oto sugerowane następne kroki:

* [Omówienie akceleratora rozwiązań do zdalnego monitorowania usługi Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
