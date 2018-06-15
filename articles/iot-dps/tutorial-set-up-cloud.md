---
title: Konfigurowanie chmury dla usługi Azure IoT Hub Device Provisioning w portalu | Microsoft Docs
description: Automatyczne aprowizowanie urządzeń usługi IoT Hub w witrynie Azure Portal
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e334ff0c8dec3a9611b60f64e565111064d10c18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630089"
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Konfigurowanie zasobów w chmurze pod kątem aprowizowania urządzeń za pomocą usługi IoT Hub Device Provisioning

Ten samouczek przedstawia konfigurowanie chmury pod kątem automatycznego aprowizowania urządzeń za pomocą usługi IoT Hub Device Provisioning. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użycie witryny Azure Portal do utworzenia usługi IoT Hub Device Provisioning i pobrania zakresu identyfikatorów
> * Tworzenie centrum IoT
> * Połączenie centrum IoT z usługą Device Provisioning
> * Ustawienie zasad alokacji w usłudze Device Provisioning

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Utworzenie wystąpienia usługi Device Provisioning i uzyskanie zakresu identyfikatorów

Wykonaj następujące kroki, aby utworzyć nowe wystąpienie usługi Device Provisioning.

1. W lewym górnym rogu witryny Azure Portal kliknij polecenie **Utwórz zasób**.
2. W polu wyszukiwania wpisz ciąg **device provisioning**. 
3. Kliknij pozycję **Usługa IoT Hub Device Provisioning**.
4. Wypełnij formularz **Usługa IoT Hub Device Provisioning** przy użyciu następujących informacji:
    
   | Ustawienie       | Sugerowana wartość | Opis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nazwa** | Dowolna unikatowa nazwa | -- | 
   | **Subskrypcja** | Twoja subskrypcja  | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Reguły i ograniczenia nazewnictwa). |
   | **Lokalizacja** | Dowolna prawidłowa lokalizacja | Aby uzyskać informacje na temat regionów, zobacz temat [Regiony systemu Azure](https://azure.microsoft.com/regions/). |   

   ![Wprowadzenie podstawowych informacji o usłudze DPS w portalu](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Kliknij przycisk **Utwórz**.
6. *Zakres identyfikatorów* służy do określania identyfikatorów rejestracji i daje gwarancję, że identyfikator rejestracji jest unikatowy. Aby uzyskać tę wartość, kliknij pozycję **Przegląd**, aby otworzyć stronę **Podstawowe elementy** dla usługi Device Provisioning. Skopiuj wartość **Zakres identyfikatorów** do lokalizacji tymczasowej w celu użycia później.
7. Zanotuj także wartość **Punkt końcowy usługi** lub skopiuj ją do lokalizacji tymczasowej w celu użycia później. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Łączenie usługi Device Provisioning z centrum IoT

Następnym krokiem jest połączenie usługi Device Provisioning z centrum IoT w celu umożliwienia usłudze IoT Hub Device Provisioning rejestrowania urządzeń w tym centrum. Usługa może aprowizować urządzenia tylko w centrach IoT połączonych z usługą Device Provisioning. Wykonaj następujące kroki.

1. Na stronie **Wszystkie zasoby** kliknij wystąpienie usługi Device Provisioning utworzone wcześniej.
2. Na stronie Usługa Device Provisioning kliknij pozycję **Połączone centra IoT Hub**.
3. Kliknij pozycję **Add** (Dodaj).
4. Na stronie **Dodawanie linku do centrum IoT Hub** użyj przycisków opcji, aby określić, czy połączone centrum IoT znajduje się w bieżącej subskrypcji, czy w innej subskrypcji. Następnie wybierz nazwę centrum IoT w polu **Centrum IoT**.
5. Kliknij pozycję **Zapisz**.

   ![Łączenie nazwy centrum z usługą DPS w portalu](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ustawienie zasad alokacji w usłudze Device Provisioning

Zasady alokacji to ustawienie usługi IoT Hub Device Provisioning określające, w jaki sposób urządzenia są przypisane do centrum IoT. Są obsługiwane trzy zasady alokacji: 

1. **Najmniejsze opóźnienie**: urządzenia są aprowizowane do centrum IoT z uwzględnieniem centrum z najmniejszym opóźnieniem do urządzenia.
2. **Dystrybucja z równymi wagami** (domyślnie): aprowizacja do każdego z połączonych centrów IoT jest jednakowo prawdopodobna. Jest to ustawienie domyślne. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie. 
3. **Konfiguracja statyczna za pośrednictwem listy rejestracji**: określenie żądanego centrum IoT na liście rejestracji ma wyższy priorytet niż zasady alokacji na poziomie usługi Device Provisioning.

Aby ustawić zasady alokacji, na stronie Usługa Device Provisioning kliknij pozycję **Zarządzanie zasadami alokacji**. Upewnij się, że dla zasad alokacji ustawiono wartość **Dystrybucja z równymi wagami** (wartość domyślną). Jeśli wprowadzisz zmiany, kliknij polecenie **Zapisz**, gdy wszystko będzie gotowe.

![Zarządzanie zasadami alokacji](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne samouczki w tej kolekcji zależą od tego samouczka. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym samouczku. Jeśli nie planujesz kontynuowania pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka.

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz wystąpienie usługi IoT Hub Device Provisioning. U góry strony **Wszystkie zasoby** kliknij polecenie **Usuń**.  
2. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. U góry strony **Wszystkie zasoby** kliknij polecenie **Usuń**.
 
## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użycie witryny Azure Portal do utworzenia usługi IoT Hub Device Provisioning i pobrania zakresu identyfikatorów
> * Tworzenie centrum IoT
> * Połączenie centrum IoT z usługą Device Provisioning
> * Ustawienie zasad alokacji w usłudze Device Provisioning

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować urządzenie pod kątem aprowizacji.

> [!div class="nextstepaction"]
> [Set up device for provisioning (Konfigurowanie urządzenia pod kątem aprowizacji)](tutorial-set-up-device.md)
