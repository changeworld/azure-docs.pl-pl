---
title: Tworzenie centrum IoT Hub za pomocą witryny Azure Portal | Dokumenty firmy Microsoft
description: Jak tworzyć, zarządzać i usuwać centra Usługi Azure IoT za pośrednictwem witryny Azure portal. Zawiera informacje o warstwach cenowych, skalowaniu, zabezpieczeniach i konfiguracji obsługi wiadomości.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284735"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Tworzenie centrum IoT przy użyciu portalu Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano sposób tworzenia centrów IoT i zarządzania nimi przy użyciu [witryny Azure Portal.](https://portal.azure.com)

Aby wykonać kroki opisane w tym samouczku, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Zmienianie ustawień centrum IoT

Ustawienia istniejącego centrum IoT hub można zmienić po jego utworzeniu w okienku Centrum IoT.

![Zrzut ekranu przedstawiający ustawienia centrum IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Oto niektóre z właściwości, które można ustawić dla centrum IoT Hub:

**Cennik i skala:** Ta właściwość służy do migracji do innej warstwy lub ustawiania liczby jednostek Usługi IoT Hub. 

**Monitorowanie operacji:** Włącz lub wyłącz różne kategorie monitorowania, takie jak rejestrowanie zdarzeń związanych z komunikatami między urządzeniami a chmurą lub komunikatami z chmury do urządzenia.

**Filtr IP**: Określ zakres adresów IP, które będą akceptowane lub odrzucane przez centrum IoT Hub.

**Właściwości:** Zawiera listę właściwości, które można kopiować i używać w innym miejscu, na przykład identyfikator zasobu, grupa zasobów, lokalizacja i tak dalej.

### <a name="shared-access-policies"></a>Zasady dostępu współużytkowanego

Listę zasad dostępu udostępnionego można również wyświetlić lub zmodyfikować, klikając pozycję **Zasady dostępu współdzielonego** w sekcji **Ustawienia.** Te zasady definiują uprawnienia urządzeń i usług do łączenia się z centrum IoT Hub. 

Kliknij **przycisk Dodaj,** aby otworzyć blok **Dodaj zasady dostępu współdzielonego.**  Można wprowadzić nową nazwę zasad i uprawnienia, które chcesz skojarzyć z tą zasadą, jak pokazano na poniższym rysunku:

![Zrzut ekranu przedstawiający dodawanie zasad dostępu współdzielonego](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Zasady **odczytu** i **zapisu rejestru** przyznają prawa dostępu do odczytu i zapisu do rejestru tożsamości. Te uprawnienia są używane przez usługi zaplecza w chmurze do zarządzania tożsamościami urządzeń. Wybranie opcji zapisu powoduje automatyczne wybranie opcji odczytu.

* **Zasady łączyć usługi** udziela uprawnień dostępu do punktów końcowych usługi. To uprawnienie jest używane przez usługi zaplecza w chmurze do wysyłania i odbierania wiadomości z urządzeń, a także do aktualizowania i odczytywania bliźniaczych danych urządzenia i bliźniaczych danych modułu.

* Zasady **łączyć urządzenia** udziela uprawnień do wysyłania i odbierania wiadomości przy użyciu punktów końcowych po stronie urządzenia Usługi IoT Hub. To uprawnienie jest używane przez urządzenia do wysyłania i odbierania wiadomości z centrum IoT hub, aktualizacji i odczytu bliźniaczej reprezentacji urządzenia i bliźniaczych danych modułu oraz do przekazywania plików.

Kliknij **przycisk Utwórz,** aby dodać nowo utworzoną zasadę do istniejącej listy.

Aby uzyskać bardziej szczegółowe informacje na temat dostępu udzielonego przez określone uprawnienia, zobacz [Uprawnienia Usługi IoT Hub](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Routing wiadomości dla centrum IoT

Kliknij **pozycję Routing wiadomości** w obszarze **Wiadomości,** aby wyświetlić okienko Routing wiadomości, w którym definiujesz trasy i niestandardowe punkty końcowe centrum. [Routing wiadomości](iot-hub-devguide-messages-d2c.md) umożliwia zarządzanie sposób wysyłania danych z urządzeń do punktów końcowych. Pierwszym krokiem jest dodanie nowej trasy. Następnie można dodać istniejący punkt końcowy do trasy lub utworzyć nowy jeden z obsługiwanych typów, takich jak magazyn obiektów blob. 

![Okienko routingu wiadomości](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy to pierwsza karta w okienku Routing wiadomości. Aby dodać nową trasę, kliknij przycisk +**Dodaj**. Zostanie wyświetlony następujący ekran. 

![Zrzut ekranu przedstawiający dodawanie nowej trasy](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nazwij swoje centrum. Nazwa musi być unikatowa na liście tras dla tego koncentratora. 

W przypadku **punktu końcowego**można wybrać jeden z listy rozwijanej lub dodać nową. W tym przykładzie konto magazynu i kontener są już dostępne. Aby dodać je jako punkt końcowy, kliknij przycisk +**Dodaj** obok listy rozwijanej Punktu końcowego i wybierz pozycję **Magazyn obiektów Blob**. Na poniższym ekranie pokazano, gdzie określono konto magazynu i kontener.

![Zrzut ekranu przedstawiający dodawanie punktu końcowego magazynu dla reguły routingu](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kliknij **przycisk Wybierz kontener,** aby wybrać konto magazynu i kontener. Po wybraniu tych pól zostanie ono ponownie wybrane do okienka Punkt końcowy. Użyj wartości domyślnych dla pozostałych pól i **Utwórz,** aby utworzyć punkt końcowy dla konta magazynu i dodać go do reguł routingu.

W obszarze **Źródło danych**wybierz pozycję Komunikaty telemetryczne urządzenia. 

Następnie dodaj kwerendę routingu. W tym przykładzie wiadomości, które `level` mają właściwość `critical` aplikacji wywoływane o wartości równej są kierowane do konta magazynu.

![Zrzut ekranu przedstawiający zapisywanie nowej reguły routingu](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Kliknij **przycisk Zapisz,** aby zapisać regułę marszruty. Powrót do okienka Routing wiadomości zostanie wyświetlony i zostanie wyświetlona nowa reguła routingu.

### <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Kliknij kartę **Niestandardowe punkty końcowe.** Widoczne są wszystkie niestandardowe punkty końcowe już utworzone. W tym miejscu można dodać nowe punkty końcowe lub usunąć istniejące punkty końcowe. 

> [!NOTE]
> Jeśli usuniesz trasę, nie spowoduje to usunięcia punktów końcowych przypisanych do tej trasy. Aby usunąć punkt końcowy, kliknij kartę Niestandardowe punkty końcowe, wybierz punkt końcowy, który chcesz usunąć, a następnie kliknij pozycję Usuń.
>

Więcej informacji na temat niestandardowych punktów końcowych można przeczytać w [programie Odwołuje się — punkty końcowe centrum IoT](iot-hub-devguide-endpoints.md).

Można zdefiniować maksymalnie 10 niestandardowych punktów końcowych dla centrum IoT hub. 

Aby zobaczyć pełny przykład używania niestandardowych punktów końcowych z routingiem, zobacz [Routing wiadomości z Centrum IoT](tutorial-routing.md)Hub .

## <a name="find-a-specific-iot-hub"></a>Znajdowanie określonego centrum IoT

Oto dwa sposoby znajdowania określonego centrum IoT w ramach subskrypcji:

1. Jeśli znasz grupę zasobów, do której należy centrum IoT, kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów z listy. Ekran grupy zasobów pokazuje wszystkie zasoby w tej grupie, w tym centra IoT. Kliknij koncentrator, którego szukasz.

2. Kliknij pozycję **Wszystkie zasoby**. W okienku **Wszystkie zasoby** znajduje się lista rozwijana, która jest domyślnie `All types`łączona. Kliknij listę rozwijanej, odznacz `Select all`. Znajdź `IoT Hub` i sprawdź to. Kliknij pole listy rozwijanej, aby ją zamknąć, a wpisy zostaną odfiltrowane, wyświetlanie tylko centrów IoT.

## <a name="delete-the-iot-hub"></a>Usuwanie centrum IoT

Aby usunąć centrum Iot, znajdź centrum IoT, który chcesz usunąć, a następnie kliknij przycisk **Usuń** poniżej nazwy centrum IoT.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z tych łączy, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Routing wiadomości za pomocą Centrum IoT](tutorial-routing.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)