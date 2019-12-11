---
title: Dodawanie urządzenia Azure IoT Edge do usługi Azure IoT Central | Microsoft Docs
description: Jako operator Dodaj urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979074"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak dodać i skonfigurować urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure. W tym samouczku została wybrana maszyna wirtualna z systemem Linux z obsługą IoT Edge z poziomu portalu Azure Marketplace.

Ten samouczek składa się z dwóch części:

* Po pierwsze, jako operator, dowiesz się, jak przeprowadzić pierwsze Inicjowanie obsługi administracyjnej urządzenia IoT Edge przez chmurę.
* Następnie dowiesz się, jak wykonać wstępne Inicjowanie obsługi administracyjnej urządzenia IoT Edge.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj nowe urządzenie IoT Edge
> * Skonfiguruj urządzenie IoT Edge, aby ułatwić obsługę administracyjną przy użyciu klucza sygnatury dostępu współdzielonego (SAS)
> * Wyświetlanie pulpitów nawigacyjnych i kondycji modułu w IoT Central
> * Wysyłanie poleceń do modułu uruchomionego na urządzeniu IoT Edge
> * Ustawianie właściwości w module uruchomionym na urządzeniu IoT Edge

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Postępuj zgodnie [z tym przewodnikiem Szybki Start, aby utworzyć aplikację IoT Central platformy Azure](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Włącz Azure IoT Edge grupę rejestracji
Na stronie **Administracja** Włącz klucze SAS dla grupy rejestracji Azure IoT Edge.

![Zrzut ekranu przedstawiający stronę Administracja z wyróżnionym połączeniem urządzenia](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Inicjowanie obsługi administracyjnej urządzenia Azure IoT Edge w chmurze  
W tej sekcji utworzysz nowe urządzenie IoT Edge przy użyciu szablonu czujnika środowiska i udostępniasz urządzenie. Wybierz pozycję **urządzenia** > **szablon czujnika środowiska**. 

![Zrzut ekranu przedstawiający stronę urządzeń z wyróżnionym szablonem czujnika środowiska](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Wybierz pozycję **+ Nowy**, a następnie wprowadź identyfikator i wybraną nazwę urządzenia. Wybierz pozycję **Utwórz**.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego urządzenia z IDENTYFIKATORem urządzenia i tworzenie wyróżnionych](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Urządzenie przechodzi w tryb **zarejestrowany** .

![Zrzut ekranu przedstawiający stronę szablonu czujnika środowiska z wyróżnioną pozycją stan urządzenia](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Wdrażanie IoT Edge z włączoną maszyną wirtualną z systemem Linux

> [!NOTE]
> Można wybrać opcję użycia dowolnego komputera lub urządzenia. System operacyjny może być w systemie Linux lub Windows.

Na potrzeby tego samouczka korzystamy z maszyny wirtualnej z systemem Linux z obsługą usługi Azure IoT utworzoną na platformie Azure. W [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)wybierz pozycję **Pobierz teraz**. 

![Zrzut ekranu witryny Azure Marketplace z wyróżnioną pozycją Pobierz teraz](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Wybierz przycisk **Kontynuuj**.

![Zrzut ekranu przedstawiający tworzenie tej aplikacji w systemie Azure okno dialogowe z wyróżnioną pozycją Kontynuuj](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Nastąpi przekierowanie do Azure Portal. Wybierz pozycję **Utwórz**.

![Zrzut ekranu przedstawiający Azure Portal z wyróżnioną pozycją Utwórz](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Wybierz pozycję **subskrypcja**, Utwórz nową grupę zasobów i wybierz pozycję **(Stany Zjednoczone) zachodnie stany USA 2** , aby uzyskać dostęp do maszyny wirtualnej. Następnie wprowadź informacje o użytkowniku i haśle. Te elementy będą wymagane do następnych kroków, dlatego należy je zapamiętać. Wybierz pozycję **Przegląd + utwórz**.

![Zrzut ekranu przedstawiający stronę szczegółów tworzenia maszyny wirtualnej z wyróżnionymi różnymi opcjami](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Po walidacji wybierz pozycję **Utwórz**.

![Zrzut ekranu przedstawiający stronę Tworzenie maszyny wirtualnej z zakończono walidacją i tworzenie wyróżnionych](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Utworzenie zasobów może potrwać kilka minut. Wybierz pozycję **Przejdź do zasobu**.

![Zrzut ekranu przedstawiający stronę ukończenie wdrożenia z wyróżnioną opcją przejdź do zasobu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Udostępnianie maszyny wirtualnej jako urządzenia IoT Edge 

W obszarze **Pomoc techniczna i rozwiązywanie problemów**wybierz pozycję **konsola szeregowa**.

![Zrzut ekranu przedstawiający opcje pomocy technicznej i rozwiązywania problemów z wyróżnioną Konsola szeregowa](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Zobaczysz ekran podobny do poniższego:

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Naciśnij klawisz ENTER, podaj nazwę użytkownika i hasło, a następnie naciśnij klawisz ENTER. 

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Aby uruchomić polecenie jako administrator (użytkownik "root"), wprowadź: **sudo Su-**

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Sprawdź wersję środowiska uruchomieniowego IoT Edge. W czasie tego pisania bieżąca wersja systemu GA to 1.0.8.

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Zainstaluj Edytor vim lub użyj nano, jeśli wolisz. 

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Edytuj plik IoT Edge config. YAML.

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Przewiń w dół i Skomentuj część parametrów połączenia w pliku YAML. 

**Przed**

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Po** (naciśnij klawisz ESC i naciśnij pozycję małe litery a, aby rozpocząć edycję).

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Usuń komentarz do części klucza symetrycznego pliku YAML. 

**Przed**

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Po**

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Przejdź do IoT Central. Pobierz identyfikator zakresu, identyfikator urządzenia i klucz symetryczny urządzenia IoT Edge.
![zrzut ekranu IoT Central z różnymi opcjami połączenia z urządzeniem](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Przejdź na komputer z systemem Linux i Zastąp identyfikator zakresu i Identyfikator rejestracji identyfikatorem urządzenia i kluczem symetrycznym.

Naciśnij klawisz ESC i wpisz **: WQ!** . Naciśnij klawisz ENTER, aby zapisać zmiany.

Uruchom ponownie IoT Edge, aby przetworzyć zmiany, a następnie naciśnij klawisz ENTER.

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Wpisz **iotedge**. Po kilku minutach zobaczysz wdrożone trzy moduły.

![Zrzut ekranu konsoli](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Eksplorator urządzeń 

W IoT Central urządzenie zostanie przesunięte w stan aprowizacji.

![Zrzut ekranu przedstawiający opcje IoT Central urządzeń z wyróżnioną pozycją stan urządzenia](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Karta **moduły** przedstawia stan urządzenia i modułu w IoT Central. 

![Zrzut ekranu przedstawiający kartę moduły IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


W formularzu zostaną wyświetlone właściwości chmury z szablonu urządzenia utworzonego w poprzednich krokach. Wprowadź wartości, a następnie wybierz pozycję **Zapisz**. 

![Zrzut ekranu przedstawiający formularz urządzenia brzegowego z systemem Linux](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Oto widok przedstawiony w postaci kafelka pulpitu nawigacyjnego.

![Zrzut ekranu przedstawiający kafelki pulpitu nawigacyjnego urządzenia z systemem Linux Edge](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z IoT Edge urządzeń i zarządzać nimi w IoT Central, Oto sugerowany następny krok:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Konfigurowanie przezroczystej bramy](../../iot-edge/how-to-create-transparent-gateway.md)
