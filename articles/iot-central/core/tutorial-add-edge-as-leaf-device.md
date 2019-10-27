---
title: Dodawanie urządzenia Azure IoT Edge do usługi Azure IoT Central | Microsoft Docs
description: Jako operator Dodaj urządzenie Azure IoT Edge do IoT Central platformy Azure
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 568e0a9c21a9d5d9a5186f0a7311c659b8573b6e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957048"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Samouczek: Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak dodać i skonfigurować *urządzenie usługi Azure IoI Edge* do aplikacji IoT Central Microsoft Azure. W tym samouczku wybrana została Azure IoT Edge z włączoną maszyną wirtualną z systemem Linux w witrynie Azure Marketplace.

Ten samouczek składa się z dwóch części:

* Po pierwsze, jako operator, dowiesz się, jak przeprowadzić pierwsze Inicjowanie obsługi administracyjnej urządzenia Azure IoT Edge przez chmurę.
* Następnie dowiesz się, jak przeprowadzić najpierw Inicjowanie obsługi administracyjnej urządzenia Azure IoT Edge.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj nowe urządzenie Azure IoT Edge
> * Skonfiguruj urządzenie Azure IoT Edge, aby ułatwić obsługę przy użyciu klucza SAS
> * Wyświetlanie pulpitów nawigacyjnych i kondycji modułu w IoT Central
> * Wysyłanie poleceń do modułu uruchomionego na urządzeniu Azure IoT Edge
> * Ustawianie właściwości w module uruchomionym na urządzeniu Azure IoT Edge

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Postępuj zgodnie z tym przewodnikiem Szybki Start, aby [utworzyć aplikację IoT Central platformy Azure](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Włącz Azure IoT Edge grupę rejestracji
Włącz klucze SAS dla grupy rejestracji Azure IoT Edge na stronie Administracja.

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Inicjowanie obsługi urządzenia w chmurze w pierwszej kolejności Azure IoT Edge   
W tej sekcji utworzysz nowe urządzenie Azure IoT Edge przy użyciu **szablonu czujnika środowiska** i udostępnienia urządzenia. Kliknij pozycję urządzenia na lewym pasku nawigacyjnym, a następnie kliknij pozycję Szablon czujnika środowiska. 

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Kliknij pozycję **+ Nowy** i wprowadź identyfikator i nazwę urządzenia. 

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Urządzenie przechodzi w tryb **zarejestrowany** .

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Wdrażanie Azure IoT Edge z włączoną maszyną wirtualną z systemem Linux

>Uwaga: możesz wybrać opcję użycia dowolnego komputera lub urządzenia. System operacyjny: Linux lub Windows)

W tym samouczku została wybrana maszyna wirtualna z systemem Linux z obsługą usługi Azure IoT, którą można utworzyć na platformie Azure. Nastąpi przekierowanie do [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) i kliknięcie przycisku **Pobierz teraz** . 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Kliknij przycisk **Kontynuuj** .

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Nastąpi przekierowanie do Azure Portal. Kliknij przycisk **Utwórz**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Wybierz pozycję subskrypcja, Utwórz nową grupę zasobów, a następnie wybierz pozycję Stany Zjednoczone zachodnie 2, aby uzyskać dostęp do maszyny wirtualnej, wprowadź hasło użytkownika i hasła. Pamiętaj, aby zapamiętać użytkownika, hasło będzie wymagane do kolejnych kroków. Kliknij przycisk **Przegląd + Utwórz**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Po zweryfikowaniu kliknij pozycję **Utwórz** .

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Utworzenie zasobów trwa kilka minut. Kliknij pozycję Przejdź do **zasobu**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej jako urządzenia Azure IoT Edge 

W obszarze Pomoc techniczna i rozwiązywanie problemów w lewym obszarze nawigacji kliknij pozycję Konsola szeregowa

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Zobaczysz ekran podobny do poniższego

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Naciśnij klawisz ENTER i podaj nazwę użytkownika i hasło, a następnie naciśnij klawisz ENTER. 

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Aby uruchomić polecenie jako administrator/root Uruchom polecenie: **sudo Su —**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Sprawdź wersję środowiska uruchomieniowego Azure IoT Edge. Bieżąca wersja systemu GA to 1.0.8

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Zainstaluj Edytor vim lub użyj nano, jeśli jest to Twoje preferencje. 

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Edytuj plik Azure IoT Edge config. YAML

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Przewiń w dół i Skomentuj część parametrów połączenia pliku YAML. 

**Umożliwić**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Po** (naciśnij klawisz ESC i naciśnij małe litery a, aby rozpocząć edycję)

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Usuń komentarz z części klucza symetrycznego pliku YAML. 

**Umożliwić**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Otrzyma**

![Maszyna wirtualna z systemem Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Przejdź do IoT Central i Pobierz identyfikator zakresu, identyfikator urządzenia i klucz symetryczny urządzenia Azure IoT Edge urządzeniem ![Connect](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Przejdź do pola systemu Linux i Zastąp identyfikator zakresu, Identyfikator rejestracji z IDENTYFIKATORem urządzenia i kluczem symetrycznym

Naciśnij klawisz **ESC** i wpisz **: WQ!** Naciśnij klawisz **Enter** , aby zapisać zmiany

Uruchom ponownie Azure IoT Edge, aby przetworzyć zmiany, a następnie naciśnij klawisz **Enter** .

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Wpisz: **iotedge lista**, potrwa kilka minut, zobaczysz trzy wdrożone moduły

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Eksplorator urządzeń 

W IoT Central urządzenie zostanie przesunięte w stan aprowizacji

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Na karcie moduły zostanie wyświetlony stan urządzenia i modułu w IoT Central 

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Właściwości chmury będą wyświetlane w postaci (z szablonu urządzenia utworzonego w poprzednich krokach). Wprowadź wartości i kliknij przycisk **Zapisz**. 

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Kafelek pulpitu nawigacyjnego

![Połączenie z urządzeniem](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Dodaj nowe urządzenie Azure IoT Edge
* Skonfiguruj urządzenie Azure IoT Edge, aby ułatwić obsługę przy użyciu klucza SAS
* Wyświetlanie pulpitów nawigacyjnych i kondycji modułu w IoT Central
* Wysyłanie poleceń do modułu uruchomionego na urządzeniu Azure IoT Edge
* Ustawianie właściwości w module uruchomionym na urządzeniu Azure IoT Edge

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z zarządzania urządzeniami Azure IoT Edge w usłudze IoT Central, poniżej przedstawiono sugerowany następny krok:

<!-- Next how-tos in the sequence -->

Jak skonfigurować niejawną bramę, postępuj zgodnie z tym samouczkiem

> [!div class="nextstepaction"]
> [Konfigurowanie przezroczystej bramy](../../iot-edge/how-to-create-transparent-gateway.md)
