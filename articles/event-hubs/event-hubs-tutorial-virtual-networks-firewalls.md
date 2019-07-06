---
title: Samouczek — Włączanie integracji sieci wirtualnych i zapór w usłudze Event Hubs | Microsoft Docs
description: W tym samouczku dowiesz się, jak zintegrować usługę Event Hubs z sieciami wirtualnymi i zaporami, aby umożliwić bezpieczny dostęp.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 0f7c7e348c154aab1deb10273346a5395599b745
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605860"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Samouczek: Włączanie integracji sieci wirtualnych i zapór w przestrzeni nazw usługi Event Hubs

[Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) rozszerzają prywatną przestrzeń adresową i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

Zapory umożliwiają ograniczenie dostępu do przestrzeni nazw usługi Event Hubs z określonych adresów IP (lub zakresów adresów IP)

W tym samouczku pokazano, jak zintegrować punkty końcowe usługi sieci wirtualnych i skonfigurować zapory (filtrowanie adresów IP), korzystając z istniejącej przestrzeni nazw usługi Azure Event Hubs, za pomocą portalu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Integrowanie punktów końcowych usługi sieci wirtualnych z przestrzenią nazw usługi Event Hubs.
> * Konfigurowanie zapory (filtrowania adresów IP) przy użyciu przestrzeni nazw usługi Event Hubs.

>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Event Hubs.
>
> Gdy włączone są sieci wirtualne, integracje firmy Microsoft nie są obsługiwane.
> Typowe scenariusze platformy Azure, które nie działają z sieciami wirtualnymi:
> * Diagnostyka Azure i rejestrowanie
> * Usługa Azure Stream Analytics
> * Integracja usługi Event Grid
> * Usługi Web Apps i Functions muszą być włączone w sieci wirtualnej.
> * Funkcja IoT Hub Routes
> * Program IoT Device Explorer


> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].

## <a name="prerequisites"></a>Wymagania wstępne

Będziemy korzystać z istniejącej przestrzeni nazw usługi Event Hubs, dlatego upewnij się, że masz dostępną przestrzeń nazw usługi Event Hubs. Jeśli jej nie masz, zapoznaj się z [tym samouczkiem](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Przejdź najpierw do [witryny Azure portal][Azure portal] i zaloguj się przy użyciu subskrypcji platformy Azure.

## <a name="select-event-hubs-namespace"></a>Wybieranie przestrzeni nazw usługi Event Hubs

Na potrzeby tego samouczka utworzyliśmy przestrzeń nazw usługi Event Hubs i przejdziemy do niej.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Przechodzenie do środowiska Zapory i sieci wirtualne

W menu nawigacji w lewym okienku portalu wybierz opcję **Zapory i sieci wirtualne**.

  ![Przechodzenie do menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Przy pierwszej wizycie na tej stronie musi być wybrany przycisk radiowy **Wszystkie sieci**. Oznacza to, że przestrzeń nazw usługi Event Hubs zezwala na wszystkie połączenia przychodzące.

## <a name="add-virtual-network-service-endpoint"></a>Dodawanie punktu końcowego usługi dla sieci wirtualnej

Aby ograniczyć dostęp, musisz zintegrować punkt końcowy usługi dla sieci wirtualnej dla tej przestrzeni nazw usługi Event Hubs.

1. Kliknij przycisk radiowy **Wybrane sieci** w górnej części strony, aby włączyć pozostałą część strony z opcjami menu.
  ![wybrane sieci](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. W sekcji Sieci wirtualne wybierz opcję ***+Dodaj istniejącą sieć wirtualną***. Spowoduje to wysunięcie okienka umożliwiającego wybranie już utworzonej sieci wirtualnej.
  ![dodawanie istniejącej sieci wirtualnej](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Wybierz sieć wirtualną z listy i wybierz podsieć.
   ![wybieranie podsieci](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Musisz włączyć punkt końcowy usługi przed dodaniem sieci wirtualnej do listy. Jeśli punkt końcowy usługi nie jest włączony, portal wyświetli monitu o jego włączenie.
  ![wybieranie podsieci i włączanie punktu końcowego](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Jeśli nie można włączyć punktu końcowego usługi, możesz zignorować brakujący punkt końcowy usługi dla sieci wirtualnej za pomocą szablonu ARM. Ta funkcja nie jest dostępna w portalu.

5. Po włączeniu punktu końcowego usługi w wybranej podsieci możesz kontynuować, aby dodać ją do listy dozwolonych sieci wirtualnych.
  ![dodawanie podsieci po włączeniu punktu końcowego](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Wybierz przycisk **Zapisz** na górnej wstążce, aby zapisać konfigurację sieci wirtualnej w usłudze. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.

## <a name="add-firewall-for-specified-ip"></a>Dodawanie zapory dla określonego adresu IP

Przy użyciu reguł zapory można umożliwić dostęp do przestrzeni nazw usługi Event Hubs tylko ograniczonemu zakresowi adresów IP lub adresowi IP.

1. Kliknij przycisk radiowy **Wybrane sieci** w górnej części strony, aby włączyć pozostałą część strony z opcjami menu.
  ![wybrane sieci](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. W sekcji **Zapory** w obszarze ***Zakres adresów*** możesz dodać jeden lub wiele konkretnych adresów IP lub zakresów adresów IP.
  ![dodawanie adresów IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Po dodaniu wielu adresów IP (lub zakresów adresów IP) wybierz przycisk **Zapisz** na górnej wstążce, aby upewnić się, że konfiguracja jest zapisywana po stronie usługi. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.
  ![dodawanie adresów i wybieranie przycisku Zapisz](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Dodawanie bieżącego adresu IP do reguł zapory

1. Możesz również szybko dodać swój bieżący adres IP, zaznaczając pole wyboru ***Dodaj swój adres IP klienta (TWÓJ BIEŻĄCY ADRES IP)*** tuż nad obszarem ***Zakres adresów***.
  ![dodawanie bieżącego adresu IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Po dodaniu swojego bieżącego adresu IP do reguły zapory wybierz przycisk **Zapisz** na górnej wstążce, aby upewnić się, że konfiguracja jest zapisywana po stronie usługi. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.
  ![dodawanie bieżącego adresu IP i wybieranie przycisku Zapisz](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Podsumowanie

W ramach tego samouczka zintegrowano punkty końcowe sieci wirtualnej i reguły zapory z istniejącą przestrzenią nazw usługi Event Hubs. Omówione czynności:
> [!div class="checklist"]
> * Integrowanie punktów końcowych usługi sieci wirtualnych z przestrzenią nazw usługi Event Hubs.
> * Konfigurowanie zapory (filtrowania adresów IP) przy użyciu przestrzeni nazw usługi Event Hubs.


[Azure portal]: https://portal.azure.com/
