---
title: Tworzenie Centrum IoT za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Sposób tworzenia, zarządzania i usuwania usługi Azure IoT hubs za pomocą witryny Azure portal. Zawiera informacje na temat warstw cenowych, skalowanie, zabezpieczeń i komunikatów konfiguracji.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 21758d112cd7eaf45b8b279cf2a6f6060c44c9c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742033"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Tworzenie Centrum IoT przy użyciu witryny Azure portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano sposób tworzenia i zarządzanie centrami IoT Hub przy użyciu [witryny Azure portal](https://portal.azure.com).

Aby skorzystać z instrukcji w tym samouczku, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Zmień ustawienia usługi IoT hub

Po utworzeniu jest ono w okienku usługi IoT Hub, możesz zmienić ustawienia już istniejące Centrum IoT.

![Zrzut ekranu przedstawiający ustawienia dla usługi IoT hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Oto niektóre właściwości, które można ustawić dla Centrum IoT:

**Ceny i Skala**: Ta właściwość służy do migracji do innej warstwy lub ustawić liczbę usługi IoT Hub jednostki. 

**Monitorowanie operacji**: Włącz lub wyłącz, różne kategorie monitorowania takich jak rejestrowanie zdarzeń związanych z komunikatów z urządzenia do chmury lub komunikatów z chmury do urządzeń.

**Filtr adresów IP**: Określ zakres adresów IP, które zostanie zaakceptowany lub odrzucony przez usługę IoT hub.

**Właściwości**: Zawiera listę właściwości, można skopiować i używane w innych miejscach, takie jak identyfikator zasobu, grupy zasobów, lokalizację i tak dalej.

### <a name="shared-access-policies"></a>Zasady dostępu współużytkowanego

Można również wyświetlić lub zmodyfikować listę zasady dostępu współdzielonego, klikając **zasady dostępu współdzielonego** w **ustawienia** sekcji. Te zasady zdefiniować uprawnienia dla urządzeń i usług do łączenia z usługą IoT Hub. 

Kliknij przycisk **Dodaj** otworzyć **Dodawanie zasad dostępu współdzielonego** bloku.  Można wprowadzić nową nazwę zasady i uprawnienia, które chcesz skojarzyć z zasadami, jak pokazano na poniższej ilustracji:

![Zrzut ekranu przedstawiający dodawanie zasad dostępu współdzielonego](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* **Odczyt rejestru** i **zapis w rejestrze** zasady przyznają uprawnienia dostępu odczytu i zapisu w rejestrze tożsamości. Wybranie opcji zapisu automatycznie wybiera wybranie opcji odczytu.

* **Połączenie z usługą** zasad udziela uprawnień do dostępu do punktów końcowych usług, takich jak **otrzymywać urządzenia do chmury**. 

* **Połączenie urządzenia** zasad przyznaje uprawnienia do wysyłania i odbierania komunikatów za pomocą punktów końcowych po stronie urządzenia usługi IoT Hub.

Kliknij przycisk **Utwórz** można dodać tę nowo utworzoną zasad do istniejącej listy.

## <a name="message-routing-for-an-iot-hub"></a>Routing komunikatów usługi IoT hub

Kliknij przycisk **Routing komunikatów** w obszarze **komunikatów** do można znaleźć w okienku przesyłanie wiadomości, gdzie należy zdefiniować trasy i niestandardowe punkty końcowe dla koncentratora. [Routing komunikatów](iot-hub-devguide-messages-d2c.md) pozwala na zarządzanie, jak dane są wysyłane z urządzeń do punktów końcowych. Pierwszym krokiem jest, aby dodać nową trasę. Następnie możesz dodać istniejący punkt końcowy do trasy lub Utwórz nowe typy obsługiwane, takie jak magazyn obiektów blob. 

![Okienko routing wiadomości](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy jest pierwsza karta w okienku przesyłanie wiadomości. Aby dodać nową trasę, kliknij pozycję +**Dodaj**. Zostanie wyświetlony następujący ekran. 

![Zrzut ekranu przedstawiający dodawanie nowego trasy](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nazwij Centrum. Nazwa musi być unikatowa w obrębie listy trasy dla koncentratora. 

Aby uzyskać **punktu końcowego**, wybierz ją z listy rozwijanej lub dodać nowy. W tym przykładzie konto magazynu i kontener są już dostępne. Aby dodać je jako punkt końcowy, kliknij pozycję +**Dodaj** obok listy rozwijanej punktu końcowego, a następnie wybierz pozycję **magazynu obiektów Blob**. Poniższy ekran przedstawia gdzie określone konto magazynu i kontener.

![Zrzut ekranu przedstawiający dodawanie punktu końcowego magazynu dla reguły routingu](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kliknij przycisk **Wybierz kontener** wybrać konto magazynu i kontener. Po wybraniu tych pól, zwraca do okienka punktu końcowego. Użyj wartości domyślnych dla pozostałych pól i **Utwórz** do tworzenia punktu końcowego konta magazynu i dodaj go do reguły routingu.

Aby uzyskać **źródła danych**, wybierz komunikaty telemetryczne urządzenia. 

Następnie dodaj routing zapytań. W tym przykładzie, wiadomości, które mają właściwość aplikacji o nazwie `level` o wartości równej `critical` są kierowane do konta magazynu.

![Zrzut ekranu przedstawiający zapisywanie nową regułę routingu](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Kliknij przycisk **Zapisz** można zapisać reguły routingu. Wróć do okienka Routing komunikatów i nową regułę routingu jest wyświetlana.

### <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Kliknij przycisk **niestandardowe punkty końcowe** kartę. Zobaczysz wszystkie niestandardowe punkty końcowe już utworzony. W tym miejscu możesz dodać nowe punkty końcowe lub usuń istniejące punkty końcowe. 

> [!NOTE]
> Jeśli usuniesz trasy, nie powoduje usunięcia punktów końcowych, przypisany do tej trasy. Aby usunąć punkt końcowy, kliknij kartę niestandardowa punktów końcowych, wybierz punkt końcowy, który chcesz usunąć i kliknij przycisk Usuń.
>

Możesz dowiedzieć się więcej o niestandardowych punktach końcowych w [odwołanie — punkty końcowe IoT hub](iot-hub-devguide-endpoints.md).

Można zdefiniować maksymalnie 10 niestandardowych punktów końcowych usługi IoT hub. 

Aby zobaczyć pełny przykład sposobu użycia niestandardowych punktów końcowych przy użyciu routingu, zobacz [routing za pomocą usługi IoT Hub komunikatów](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Odnaleźć określonej usługi IoT hub

Poniżej przedstawiono dwa sposoby znajdowania określonej usługi IoT hub w ramach subskrypcji:

1. Jeśli wiesz, zasobów, do którego należy Centrum IoT kliknij pozycję **grup zasobów**, następnie wybierz grupę zasobów z listy. Ekran grupa zasobów zawiera wszystkie zasoby w tej grupie, w tym centra IoT Hub. Kliknij Centrum, dla których potrzebujesz.

2. Kliknij przycisk **wszystkie zasoby**. Na **wszystkie zasoby** okienku istnieje na liście rozwijanej, która domyślnie `All types`. Kliknij listę rozwijaną, usuń zaznaczenie pola wyboru `Select all`. Znajdź `IoT Hub` i sprawdzaj ją. Kliknij w polu listy rozwijanej, aby go zamknąć, a można filtrować wpisów, przedstawiający usługi IoT Hub.

## <a name="delete-the-iot-hub"></a>Usuwanie IoT hub

Aby usunąć Centrum Iot hub, można znaleźć w Centrum IoT, które chcesz usunąć, a następnie kliknij przycisk **Usuń** znajdujący się poniżej nazwę Centrum IoT.

## <a name="next-steps"></a>Kolejne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Routing komunikatów usługi IoT Hub](tutorial-routing.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)