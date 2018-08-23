---
title: Tworzenie Centrum IoT za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Sposób tworzenia, zarządzania i usuwania usługi Azure IoT hubs za pomocą witryny Azure portal. Zawiera informacje na temat warstw cenowych, skalowanie, zabezpieczeń i komunikatów konfiguracji.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054808"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Tworzenie Centrum IoT przy użyciu witryny Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano:

* Jak znaleźć tę usługę IoT Hub w witrynie Azure portal.
* Jak tworzyć i zarządzać nimi centra IoT Hub.

## <a name="where-to-find-the-iot-hub-service"></a>Gdzie można znaleźć usługi IoT Hub

Usługa IoT Hub można znaleźć w następujących lokalizacjach w portalu:

* Wybierz **+ nowy**, następnie wybierz **Internet of Things**.
* W witrynie Marketplace, wybierz **Internet of Things**.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Można utworzyć usługi IoT hub przy użyciu następujących metod:

* **+ Nowy** opcja powoduje otwarcie bloku pokazano na poniższym zrzucie ekranu. Kroki tworzenia Centrum IoT hub za pośrednictwem tej metody, jak i w witrynie marketplace są identyczne.

* W witrynie Marketplace, wybierz **Utwórz** aby otworzyć blok pokazano na poniższym zrzucie ekranu.

W poniższych sekcjach opisano kilka kroków, aby utworzyć Centrum IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Wybierz nazwę Centrum IoT hub

Aby utworzyć Centrum IoT hub, nadaj nazwę Centrum IoT hub. Ta nazwa musi być unikatowa we wszystkich centrach IoT.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Wybierz warstwę cenową

Można wybrać z kilku warstwach, w zależności od tego, ile funkcje chcesz i ile komunikatów można wysyłać za pośrednictwem tego rozwiązania na dzień. Bezpłatna warstwa jest przeznaczona do testowania i oceny. Umożliwia ona 500 urządzeń do podłączenia do usługi IoT hub i maksymalnie 8000 komunikatów dziennie. Każda subskrypcja platformy Azure można utworzyć jedno centrum IoT w ramach warstwy bezpłatna. 

Aby uzyskać szczegółowe informacje na temat innych opcji warstwy, zobacz [wybierając właściwą warstwę usługi IoT Hub](iot-hub-scaling.md).

### <a name="iot-hub-units"></a>Jednostek usługi IoT hub

Liczba komunikatów dopuszczalną na jednostkę dziennie zależy od warstwy cenowej Twojego Centrum. Na przykład chcąc usługi IoT hub do obsługi przychodzących 700 000 wiadomości, możesz wybrać dwie jednostki warstwy S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Urządzenia do chmury, partycji i grupy zasobów

Można zmienić liczby partycji Centrum IoT. Domyślna liczba partycji wynosi 4; z listy rozwijanej, można wybrać inny numer.

Nie trzeba jawnie Utwórz pustą grupę zasobów. Podczas tworzenia zasobu, możesz utworzyć nową grupę zasobów lub użyj istniejącej grupy zasobów.

![Zrzut ekranu przedstawiający tworzenie Centrum w witrynie Azure portal](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>Wybierz subskrypcję

Usługa Azure IoT Hub automatycznie wyświetla listę subskrypcji platformy Azure, z którą połączony jest konto użytkownika. Możesz wybrać subskrypcję platformy Azure do skojarzenia z Centrum IoT.

### <a name="choose-the-location"></a>Wybierz lokalizację

Opcja lokalizacji zapewnia listę regionów, w których jest dostępna usługa IoT Hub.

### <a name="create-the-iot-hub"></a>Tworzenie Centrum IoT hub

Po ukończeniu wszystkich poprzednich kroków można utworzyć usługi IoT hub. Kliknij przycisk **Utwórz** można uruchomić procesu zaplecza, do tworzenia i wdrażania usługi IoT hub przy użyciu opcji została wybrana opcja.

Może upłynąć kilka minut, aby utworzyć Centrum IoT hub, ponieważ zajmuje trochę czasu wdrożenia zaplecza do uruchamiania na serwerach z odpowiedniej lokalizacji.

## <a name="change-the-settings-of-the-iot-hub"></a>Zmień ustawienia usługi IoT hub
<!--robinsh these screenshots are out of date -->

Możesz zmienić ustawienia już istniejące Centrum IoT, utworzony z bloku usługi IoT Hub.

![Zrzut ekranu przedstawiający ustawienia dla usługi IoT hub](./media/iot-hub-create-through-portal/portal-settings.png)

**Udostępnione zasady dostępu**: te zasady zdefiniować uprawnienia dla urządzeń i usług do łączenia z usługą IoT Hub. Te zasady można skorzystać, klikając **zasady dostępu współdzielonego** w obszarze **ogólne**. W tym bloku możesz zmodyfikować istniejące zasady lub Dodaj nowe zasady.

### <a name="create-a-policy"></a>Utwórz zasady

* Kliknij przycisk **Dodaj** aby otworzyć blok. W tym miejscu można wprowadzić nową nazwę zasady i uprawnienia, które chcesz skojarzyć z zasadami, jak pokazano na poniższej ilustracji:

    Istnieje kilka uprawnienia, które mogą być skojarzone z tymi zasadami udostępnionych. **Odczyt rejestru** i **zapis w rejestrze** zasady przyznają uprawnienia dostępu odczytu i zapisu w rejestrze tożsamości. Wybranie opcji zapisu automatycznie wybiera wybranie opcji odczytu.

    **Połączenie z usługą** zasad udziela uprawnień do dostępu do punktów końcowych usług, takich jak **otrzymywać urządzenia do chmury**. **Połączenie urządzenia** zasad przyznaje uprawnienia do wysyłania i odbierania komunikatów za pomocą punktów końcowych po stronie urządzenia usługi IoT Hub.

* Kliknij przycisk **Utwórz** można dodać tę nowo utworzoną zasad do istniejącej listy.

   ![Zrzut ekranu przedstawiający dodawanie zasad dostępu współdzielonego](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>Punkty końcowe

Kliknij przycisk **punktów końcowych** do wyświetlania listy punktów końcowych usługi IoT hub, która jest modyfikowana. Istnieją dwa typy punktów końcowych: punkty końcowe, które są wbudowane w usługę IoT hub i punktów końcowych, które dodajesz do usługi IoT hub po jego utworzeniu.

![Zrzut ekranu przedstawiający dodawanie punktu końcowego](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>Wbudowane punkty końcowe

Istnieją dwie wbudowane punkty końcowe: **chmury do urządzenia opinii** i **zdarzenia**.

* **Chmury do urządzenia opinii** ustawień: to ustawienie ma dwa subsettings: **TTL urządzenia w chmurze** (time-to-live) i **czas przechowywania** (w godzinach) dla wiadomości. Podczas pierwszego tworzenia Centrum IoT hub, oba te ustawienia mają wartość domyślną jedną godzinę. Aby dostosować te ustawienia, za pomocą suwaków lub wpisz wartości.

* **Zdarzenia** ustawień: to ustawienie ma kilka subsettings, niektóre z nich są przeznaczone tylko do odczytu. Na poniższej liście opisano te ustawienia:

  * **Partycje**: ustawiono wartość domyślną, po utworzeniu Centrum IoT hub. Można zmienić liczby partycji za pomocą tego ustawienia.

  * **Nazwę zgodną z Centrum zdarzeń i punkt końcowy**: gdy usługa IoT hub jest tworzony, Centrum zdarzeń jest tworzone wewnętrznie, może być wymagany dostęp do w pewnych okolicznościach. Nie można dostosować wartości nazwy i punktu końcowego zgodnego z Centrum zdarzeń, ale można je skopiować, klikając **kopiowania**.

  * **Czas przechowywania**: Domyślnie ustawiany na jeden dzień, ale można zmienić za pomocą listy rozwijanej. Ta wartość jest Days ustawienia urządzenia do chmury.

  * **Grupy konsumentów**: grupy konsumentów włączyć wielu elementów odczytujących niezależnie odczytywanie komunikatów z usługi IoT hub. Każde wystąpienie usługi IoT hub jest tworzony z domyślna grupa odbiorców. Można jednak dodać lub usunąć grupy konsumentów do usługi IoT Hub przy użyciu tego ustawienia.

  > [!NOTE]
  > Domyślna grupa odbiorców nie można edytować ani usunąć.

### <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Możesz dodać niestandardowe punkty końcowe Centrum IoT przy użyciu portalu. Z **punktów końcowych** bloku kliknij **Dodaj** u góry, aby otworzyć **Dodaj punkt końcowy** bloku. Wprowadź wymagane informacje, a następnie kliknij przycisk **OK**. Niestandardowy punkt końcowy zostanie wyświetlony w oknie głównym **punktów końcowych** bloku.

![Zrzut ekranu przedstawiający tworzenie niestandardowego punktu końcowego](./media/iot-hub-create-through-portal/endpoint-creation.png)

Możesz dowiedzieć się więcej o niestandardowych punktach końcowych w [odwołanie — punkty końcowe IoT hub]( iot-hub-devguide-endpoints.md).

## <a name="routes"></a>Trasy

Kliknij przycisk **trasy** do zarządzania, jak usługa IoT Hub wywołuje komunikatów przesyłanych z chmury do urządzenia.

![Zrzut ekranu przedstawiający dodawanie nowego trasy](./media/iot-hub-create-through-portal/routes-list.png)

Można dodać trasy do usługi IoT hub, klikając **Dodaj** w górnej części **trasy*** bloku, wprowadzając wymagane informacje i kliknięcie **OK**. Trasy następnie znajduje się w głównym **trasy** bloku. Można edytować trasę, klikając go na liście trasy. Aby włączyć trasy, kliknij je na liście tras i ustaw **włączone** Przełącz, aby **poza**. Aby zapisać zmiany, kliknij przycisk **OK** w dolnej części bloku.

![Zrzut ekranu przedstawiający edytowanie nowej reguły routingu](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>Usuwanie IoT hub

Możesz przejść do Centrum IoT, które chcesz usunąć, klikając **Przeglądaj**, a następnie wybierając odpowiednie koncentratora do usunięcia. Aby usunąć Centrum IoT, kliknij przycisk **Usuń** znajdujący się poniżej nazwę Centrum IoT.

## <a name="next-steps"></a>Kolejne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpieczać rozwiązanie IoT od podstaw w górę](../iot-fundamentals/iot-security-ground-up.md)
