---
title: Usługa Microsoft Azure Data Box Gateway dostępu urządzeń, moc i tryb łączności | Dokumentacja firmy Microsoft
description: Opisuje sposób zarządzania dostęp, moc i tryb łączności dla urządzenia bramy pola danych platformy Azure, że ułatwia przesyłanie danych do platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d46d74544181a6b9cbfd049b2f5461b20b928483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401446"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Zarządzanie dostępem, power i tryb łączności bramy pola danych platformy Azure

W tym artykule opisano sposób zarządzania trybu dostępu, moc i łączności bramy pola danych platformy Azure. Te operacje są wykonywane za pomocą lokalnego Interfejsu w przeglądarce lub w portalu Azure.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do urządzeń
> * Zarządzanie tryb łączności
> * Zarządzanie energią

## <a name="manage-device-access"></a>Zarządzanie dostępem do urządzeń

Dostęp do Twojego urządzenia bramy pole danych jest kontrolowany przy użyciu hasła urządzenia. Można zmienić hasło przy użyciu lokalnego Interfejsu w przeglądarce. Mogą również resetować hasła urządzenia w witrynie Azure portal.

### <a name="change-device-password"></a>Zmienianie hasła urządzenia

Wykonaj następujące kroki w interfejsie użytkownika lokalnego, aby zmienić hasło urządzenia.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > zmiany hasła**.
2. Wprowadź bieżące hasło, a następnie nowe hasło. Podane hasło musi mieć długość od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Potwierdź nowe hasło.

    ![Zmień hasło](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kliknij przycisk **Zmień hasło**.
 
### <a name="reset-device-password"></a>Resetowanie hasła urządzenia

Resetowanie przepływ pracy wymaga od użytkownika przywołać stare hasło i jest przydatne, gdy hasło zostanie utracone. Ten przepływ pracy jest wykonywane w witrynie Azure portal.

1. W witrynie Azure portal przejdź do **Przegląd > Resetuj hasło administratora**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Wprowadź nowe hasło, a następnie potwierdź je. Podane hasło musi mieć długość od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Kliknij przycisk **resetowania**.

    ![Resetowanie hasła](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Zarządzanie tryb łączności

Oprócz to domyślny tryb normalny urządzenia można również uruchomić w trybie częściowo odłączone lub odłączony. Każde z tych trybów opisano poniżej:

- **Częściowo odłączony** — w tym trybie urządzenia nie można przekazywać dowolne dane do udziałów jednak mogą być zarządzane za pośrednictwem witryny Azure portal.

    W tym trybie zwykle jest używana podczas sieci satelitarnych mierzonego i celem jest minimalizacja zużycie przepustowości sieci. Użycie sieci minimalnej nadal może być operacje monitorowania urządzeń.

- **Odłączony** — w tym trybie urządzenie zostanie całkowicie odłączony od chmury i zarówno w chmurze przekazywanie i pobieranie jest wyłączone. Urządzenia mogą być zarządzane tylko za pomocą lokalnego Interfejsu w przeglądarce.

    Ten tryb jest zwykle używany przełączyć w tryb offline urządzenia.

Aby zmienić tryb urządzenia, wykonaj następujące kroki:

1. W lokalnym internetowym interfejsie użytkownika, urządzenia, przejdź do **Konfiguracja > Ustawienia funkcji Cloud**.
2. Wyłącz **chmury, przekazywanie i pobieranie**.
3. Aby uruchomić urządzenie w trybie rozłączonym częściowo, Włącz **Azure portal management**.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Aby uruchomić urządzenie w trybie odłączenia, wyłącz **Azure portal management**. Teraz urządzenie może być zarządzane za pomocą lokalnego Interfejsu w przeglądarce.

    ![Tryb łączności](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Zarządzanie energią

Można zamknąć lub ponownie uruchomić urządzenie fizyczne i wirtualne przy użyciu lokalnego Interfejsu w przeglądarce. Zaleca się, aby przed ponownym uruchomieniem przełączyć udziały w tryb offline na hoście, a następnie na urządzeniu. Ta akcja minimalizuje możliwości uszkodzenie danych.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > Ustawienia zasilania**.
2. Kliknij przycisk **zamknięcia** lub **ponowne uruchomienie** w zależności od tego, co zamierzasz wykonać.

    ![Ustawienia zasilania](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować.

> [!NOTE]
> Wyłączenie urządzenia wirtualnego należy do uruchomienia urządzenia za pośrednictwem zarządzania funkcji hypervisor.
