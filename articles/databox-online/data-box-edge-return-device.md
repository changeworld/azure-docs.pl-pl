---
title: Zwróć urządzenia usługi Azure Data Box Edge | Dokumentacja firmy Microsoft
description: Opisuje sposób zwrot urządzenia usługi Azure Data Box Edge i usuwanie zamówienia dla tego urządzenia.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468607"
---
# <a name="return-your-azure-data-box-edge-device"></a>Zwróć urządzenia usługi Azure Data Box Edge

W tym artykule opisano, jak czyszczenie danych, a następnie wróć urządzenia usługi Azure Data Box Edge. Po po zwróceniu urządzenia, można również usunąć zasób skojarzony z urządzeniem.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Czyszczenie danych z dysków danych na urządzeniu
> * Otwórz bilet pomocy technicznej, aby zwrócić urządzenia
> * Pakiet urządzenia i zaplanować odbioru
> * Usuwanie zasobu w witrynie Azure portal

## <a name="erase-data-from-the-device"></a>Wymazywanie danych z urządzenia

Czyszczenie danych z dysków danych, urządzenia, należy resetować Twoje urządzenie. Możesz zresetować urządzenie przy użyciu lokalnego Interfejsu w przeglądarce lub interfejsu programu PowerShell.

Przed zresetowaniem, Utwórz kopię dane lokalne na urządzeniu, jeśli to konieczne. Możesz skopiować dane z urządzenia do kontenera usługi Azure Storage.

Aby zresetować urządzenie przy użyciu lokalnego Interfejsu w przeglądarce, wykonaj następujące kroki.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > zresetować urządzenie**.
2. Wybierz **resetowania urządzenia**.

    ![Resetowanie urządzenia](media/data-box-edge-return-device/device-reset-1.png)

3. Po wyświetleniu monitu o potwierdzenie przejrzyj ostrzeżenie i wybierz pozycję **tak** aby kontynuować.

    ![Potwierdź resetowanie](media/data-box-edge-return-device/device-reset-2.png)  

Resetowanie usuwa dane z dysków danych urządzenia. W zależności od ilości danych na swoim urządzeniu ten proces trwa około 30 – 40 minut.

Alternatywnie nawiązać połączenie z interfejsu programu PowerShell urządzenia i wymuszają stosowanie `Reset-HcsAppliance` polecenia cmdlet, aby wymazać dane na dyskach danych. Aby uzyskać więcej informacji, zobacz [zresetowania urządzenia z systemem](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Jeśli wymiana lub uaktualnienie do nowego urządzenia, firma Microsoft zaleca zresetowania urządzenia, tylko wtedy, gdy w przypadku otrzymania nowego urządzenia.
> - Urządzenia, tylko Zresetuj powoduje usunięcie wszystkich lokalnych danych poza urządzeniem. Dane, które są w chmurze nie jest usuwany i zbiera [opłaty](https://azure.microsoft.com/pricing/details/storage/). Te dane, należy usunąć osobno przy użyciu narzędzia zarządzania magazynu chmurze, takiego jak [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Otwórz bilet pomocy technicznej

Aby rozpocząć proces zwrotu, wykonaj następujące kroki.

1. Otwórz bilet pomocy technicznej przy użyciu Microsoft Support wskazujący, że chcesz przeprowadzić przywrócenie urządzenia. Wybierz typ problemu jako **sprzętu krawędzi pola danych**.

    ![Otwórz bilet pomocy technicznej](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Inżynier firmy Microsoft Support się z Tobą. Podaj szczegóły dotyczące wysyłki.
3. Zwracany pole wysyłki, należy mogą go żądać. Odpowiedź **tak** na pytanie **muszą pustego pola do zwrócenia**.


## <a name="schedule-a-pickup"></a>Zaplanować odbioru

1. Zamknij urządzenie. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > Ustawienia zasilania**.
2. Wybierz **Zamknij**. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby kontynuować. Aby uzyskać więcej informacji, zobacz [zarządzania energią](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odłącz kable zasilania, a następnie usuń wszystkie kable sieciowe z urządzenia.
4. Przygotowywanie pakietu wydania przy użyciu własnego pola lub pustym polu otrzymany z platformy Azure. Umieść urządzenia i przewodów zasilania, które zostały wysłane do urządzenia w polu.
5. Umieszcza etykietę wysyłkową, otrzymany z platformy Azure w pakiecie.
6. Za pomocą operatora regionalnej, należy zaplanować odbioru. Jeśli zwraca urządzenia w Stanach Zjednoczonych, operatora jest UPS. Aby zaplanować odbioru:

    1. Wywołanie lokalne UPS (specyficzne dla kraju darmowy numer).
    2. W swojej rozmowy oferty odwrotnej wydanie numer, jak pokazano na etykiecie drukowanych śledzenia.
    3. Jeśli numer śledzenia nie jest w cudzysłowach, UPS będą wymagać dodatkowych opłaty są naliczane podczas odbioru.

    Zamiast planować odbiór, można również usunąć się w obrębie pola danych w najbliższej lokalizacji nadania.

## <a name="delete-the-resource"></a>Usuń zasób

Po otrzymaniu urządzenia w centrum danych platformy Azure, urządzenie jest sprawdzane pod kątem szkód lub jakiekolwiek oznaki włamania.

- Jeśli urządzenie zostanie odebrana nienaruszone i w dobrym stanie, zatrzymuje wpływu na rozliczenia, dla tego zasobu. Microsoft Support z Tobą, aby potwierdzić, że urządzenia została zwrócona. Następnie można usunąć zasób skojarzony z urządzeniem w witrynie Azure portal.
- Jeśli urządzenie zostanie odebrana, znacznie uszkodzony, mogą mieć zastosowanie kar pieniężnych. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące utraconego lub uszkodzonego urządzenia](https://azure.microsoft.com/pricing/details/databox/edge/) i [produktu warunki użytkowania usługi](https://www.microsoft.com/licensing/product-licensing/products).  


Możesz usunąć urządzenia w witrynie Azure portal:
-   Po złożeniu zamówienia, a jeśli tak, to przed urządzenia jest przygotowana przez firmę Microsoft.
-   Po po zwróceniu urządzenia do firmy Microsoft, przekazuje on kontroli fizycznej w centrum danych platformy Azure i wywołuje Microsoft Support, aby upewnić się, że urządzenia został zwrócony.

Jeśli aktywowano urządzenia względem innej subskrypcji lub lokalizacji, Microsoft zostanie przeniesione do nowej subskrypcji lub lokalizacji zamówienia w ciągu jednego dnia. Po przeniesieniu kolejności, można usunąć tego zasobu.


Wykonaj następujące kroki, aby usunąć urządzenie i zasobów w witrynie Azure portal.

1. W witrynie Azure portal przejdź do zasobu a **Przegląd**. Na pasku poleceń Wybierz **Usuń**.

    ![Wybierz opcję Usuń](media/data-box-edge-return-device/delete-resource-1.png)

2. W **Usuń urządzenie** bloku, wpisz nazwę urządzenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

    ![Potwierdzenie usunięcia](media/data-box-edge-return-device/delete-resource-2.png)

Otrzymasz powiadomienie po urządzenia i skojarzony zasób został pomyślnie usunięty.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).
