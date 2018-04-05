---
title: Otrzymasz wiadomość e-mail po otwarciu drzwi przy użyciu usługi SendGrid i usługi Azure Functions | Dokumentacja firmy Microsoft
description: Monitorowanie czujnika magnetycznego wykrywania po otwarciu drzwi i używania usługi Azure Functions, aby wysłać wiadomość e-mail z powiadomieniem.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e0882a6c87454498d0d1370ee244bfffc137aafb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="door-monitor"></a>Monitor drzwi          

Zestaw deweloperski IoT MXChip zawiera wbudowane czujnik magnetycznego. W tym projekcie można wykryć obecności lub braku pobliskich silne pola magnetycznego — w takim przypadku pochodzące z małą. magnesami.

## <a name="what-you-learn"></a>Omawiane zagadnienia

W tym projekcie dowiesz się:
- Jak używać czujnik magnetycznego zestaw MXChip IoT deweloperski wykryć przepływu magnes pobliżu.
- Jak używać usługi SendGrid, aby wysłać powiadomienie do Twojego adresu e-mail.

> [!NOTE]
> Do praktyczne zastosowania tego projektu:
> - Zainstaluj magnes krawędzią drzwi.
> - Zainstaluj zestaw deweloperski na posiadanego drzwi bliski magnes. Otwarcie lub zamknięcie drzwi wyzwoli czujnika powodujące wiadomość e-mail z powiadomieniem zdarzenia odbierania.

## <a name="what-you-need"></a>Co jest potrzebne

Zakończ [Getting Started Guide]({{"/docs/get-started/" | absolute_url }}) do:

* Twoje zestaw deweloperski ma podłączony do sieci Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli nie masz, możesz zarejestrować za pomocą jednej z następujących metod:

* Aktywuj [bezpłatne 30-dniowej wersji próbnej konto Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
* Oświadczenie użytkownika [Azure środki](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Jeśli jesteś subskrybentem MSDN lub Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Wdrażanie usługi SendGrid na platformie Azure

[SendGrid](https://sendgrid.com/) to platforma dostarczania poczty e-mail hostowanego w chmurze. Ta usługa będzie używana do wysyłania powiadomień e-mail.

> [!NOTE]
> Jeśli usługa SendGrid zostały już wdrożone, można przejść bezpośrednio do [wdrażania Centrum IoT na platformie Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Włączenie wdrożenia

Aby zainicjować obsługę usług platformy Azure, użyj **wdrażanie na platformie Azure** przycisku. Ten przycisk umożliwia szybkie i łatwe wdrażanie projekt open source do systemu Microsoft Azure.

Kliknij przycisk **wdrażanie na platformie Azure** przycisk poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Następnie wyświetlona następująca strona.

> [!NOTE]
> Jeśli nie ma następującą stronę, może być konieczne pierwszy raz logujesz się do konta platformy Azure.

Wypełnij formularz rejestracji:

  * **Grupa zasobów**: Utwórz grupę zasobów do obsługi usługi SendGrid lub użyć istniejącego. Zobacz [używanie grup zasobów do zarządzania zasobami Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **Nazwa**: Nazwa usługi SendGrid. Wybierz unikatową nazwę, różniące się od innych usług, które mogą mieć.

  * **Hasło**: usługa wymaga hasła, które nie będą dla wszystkich elementów w tym projekcie.

  * **Wiadomości e-mail**: SendGrid Usługa wyśle weryfikacji do tego adresu e-mail.

  > [!NOTE]
  > Sprawdź **Przypnij do pulpitu nawigacyjnego** opcję, aby ułatwić znajdowanie w przyszłości tej aplikacji.
 
![Włączenie wdrożenia](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Utworzenie klucza interfejsu API SendGrid

Po wdrożeniu zakończy się powodzeniem, kliknij go, a następnie kliknij przycisk **Zarządzaj** przycisku. Zostają przeniesieni do strony swojego SendGrid i musimy zweryfikować adres e-mail.

![Zarządzanie SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stronie SendGrid kliknij **ustawienia** > **klucze interfejsu API** > **Utwórz klucz interfejsu API**. Dane wejściowe **nazwa klucza interfejsu API** i kliknij przycisk **widoku & Utwórz**.

![Najpierw utwórz SendGrid interfejsu API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid Utwórz drugi interfejs API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klucz interfejsu API jest wyświetlany tylko jeden raz. Pamiętaj skopiować i zapisać go bezpieczne, ponieważ jest używana w następnym kroku.

## <a name="deploy-iot-hub-in-azure"></a>Wdrażanie Centrum IoT na platformie Azure

Poniższe kroki Obsługa będzie inicjowana innych Azure IoT związane z usługami i wdrażanie usługi Azure Functions dla tego projektu.

Kliknij przycisk **wdrażanie na platformie Azure** przycisk poniżej. 

[![Wdrażanie na platformie Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Następnie wyświetlona następująca strona.

> [!NOTE]
> Jeśli nie widzisz następującą stronę, może być konieczne pierwszy raz logujesz się do konta platformy Azure.

Wypełnij formularz rejestracji:

  * **Grupa zasobów**: Utwórz grupę zasobów do obsługi usługi SendGrid lub użyć istniejącego. Zobacz [używanie grup zasobów do zarządzania zasobami Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **Nazwa centrum iot**: Nazwa centrum IoT. Wybierz unikatową nazwę, różniące się od innych usług, które mogą mieć.

  * **Jednostka Sku Centrum iot**: F1 (ograniczone jednym dla każdej subskrypcji) jest bezpłatna. Widać więcej informacje o cenach w [warstwę cenową i wartę skali](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Z wiadomości E-mail z**: powinien to być ten sam adres e-mail używany podczas konfigurowania usługi SendGrid.

  > [!NOTE]
  > Sprawdź **Przypnij do pulpitu nawigacyjnego** opcję, aby ułatwić znajdowanie w przyszłości tej aplikacji.
 
![Wdrożenia z Centrum IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Tworzenie i przekazywanie kodu

### <a name="start-vs-code"></a>Kod uruchomienia programu VS

- Upewnij się, że jest Twoje zestaw deweloperski **nie** podłączone do komputera.
- Uruchom kod programu VS.
- Zestaw deweloperski można połączyć się z komputerem.

Kod VS automatycznie wykryje Twoje zestaw deweloperski i Otwórz stronę wprowadzenie:

![VSCode](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-start.png)

> [!NOTE]
> Podczas uruchamiania kodu programu VS, może zostać wyświetlony komunikat o błędzie informujący, że nie można odnaleźć Arduino IDE lub pakietu pokrewne tablicy. Jeśli ten błąd jest wyświetlany, zamknij kodzie VS, uruchom ponownie IDE Arduino i kodzie VS należy zlokalizować Arduino IDE ścieżkę poprawnie.

### <a name="open-arduino-examples-folder"></a>Otwórz folder Arduino przykłady

Po lewej stronie rozwiń **przykłady ARDUINO** przejdź do **przykłady MXCHIP AZ3166 > AzureIoT**i wybierz **DoorMonitor**. Ta akcja powoduje otwarcie nowego okna kodzie VS z folderu projektu w nim.

![Mini solution — przykłady](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

### <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

W oknie rozwiązania Uruchom chmury inicjowania obsługi zadań:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Wprowadź `task cloud-provision` w polu tekstowym podana.

W terminalu VS kodu interaktywnego wiersza polecenia przeprowadzi Cię przez Inicjowanie obsługi administracyjnej wymaganych usług Azure. Wybierz wszystkie te same elementy na liście zostanie wyświetlony monit o, który wcześniej zainicjowane w [wdrażania Centrum IoT na platformie Azure](#deploy-iot-hub-in-azure).

![Zainicjuj obsługę chmury](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Jeśli strony w stan ładowania, zawiesza się podczas próby logowanie do platformy Azure, zobacz [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) Aby rozwiązać ten problem. 

### <a name="build-and-upload-the-device-code"></a>Tworzenie i przekazywanie kodu urządzenia

#### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.
2. Terminal wyświetli monit o wprowadzenie trybu konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie są wyświetlane numeru identyfikacyjnego zestaw deweloperski i słowem *konfiguracji*.

Ta procedura ustawia parametry połączenia, które są pobierane z [usług Azure należy](#provision-azure-services) kroku.

Aby zestaw deweloperski szkic kodzie VS, a następnie uruchamia Weryfikowanie i przekazywanie Arduino:

![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE]
> Czasami może pojawić się "Błąd: AZ3166: Nieznany pakiet" komunikat o błędzie. Ten błąd występuje, gdy indeks tablicy pakietu nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się to [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Ustawić zestaw deweloperski trybu konfiguracji: przytrzymaj naciśnięty przycisk A, a następnie wypychania i zwolnij przycisk resetowania. Na ekranie "Konfiguracja".
2. Użyj `Cmd+P` do uruchomienia `task device-upload`.

Ta procedura ustawia parametry połączenia, które są pobierane z [usług Azure należy](#provision-azure-services) kroku.

Aby zestaw deweloperski szkic kodzie VS, a następnie uruchamia Weryfikowanie i przekazywanie Arduino:

![przekazywanie urządzeń](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE]
> Czasami może pojawić się "Błąd: AZ3166: Nieznany pakiet" komunikat o błędzie. Ten błąd występuje, gdy indeks tablicy pakietu nie jest odświeżany poprawnie. Aby rozwiązać ten problem, zapoznaj się to [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Projekt testowy

Program najpierw inicjuje po zestaw deweloperski obecności stabilna pola magnetycznego.

Po zainicjowaniu `Door closed` jest wyświetlany na ekranie. W przypadku zmiany pola magnetycznego stan zmieni się na `Door opened`. Po każdej aktualizacji zmian stanu drzwi, otrzymasz wiadomość e-mail z powiadomieniem. (Te wiadomości e-mail może potrwać do pięciu minut, aby otrzymywać).

![Pól bliski czujnika: zamknięta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "pól bliski czujnika: zamknięta")

![Magnes odsunąć od czujnika: otworzyć drzwi](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "magnes odsunąć od czujnika: drzwi otwarte")

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub łączyć się przy użyciu następujących kanałów:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

Znasz jak zestaw deweloperski urządzenie podłączone do pakietu IoT Azure i korzystać z usługi SendGrid do wysyłania wiadomości e-mail. Poniżej znajdują się Sugerowane następne kroki:

* [Omówienie pakiet IoT Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Podłącz urządzenie z systemem zestaw deweloperski IoT MXChip do aplikacji Microsoft IoT centralnego](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
