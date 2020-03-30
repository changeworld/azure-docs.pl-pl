---
title: Zwracaj lub wymieniaj urządzenie usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zwracania lub zastępowania urządzenia usługi Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651104"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Zwracanie lub zastępowanie urządzenia usługi Azure Data Box Edge

W tym artykule opisano sposób czyszczenia danych, a następnie zwracania urządzenia usługi Azure Data Box Edge. Po zwróceniu urządzenia można również usunąć zasób skojarzony z urządzeniem lub zamówić urządzenie zastępcze.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Czyszczenie danych z dysków z danymi na urządzeniu
> * Otwórz bilet pomocy technicznej, aby zwrócić urządzenie
> * Spakuj urządzenie i zaplanuj odbiór
> * Usuwanie zasobu w witrynie Azure portal
> * Uzyskaj urządzenie zastępcze

## <a name="erase-data-from-the-device"></a>Wymazywanie danych z urządzenia

Aby wymazać dane z dysków z danymi urządzenia, należy zresetować urządzenie. Urządzenie można zresetować przy użyciu lokalnego interfejsu użytkownika sieci Web lub interfejsu programu PowerShell.

Przed zresetowaniem utwórz kopię danych lokalnych na urządzeniu, jeśli to konieczne. Dane można skopiować z urządzenia do kontenera usługi Azure Storage.

Aby zresetować urządzenie przy użyciu lokalnego interfejsu użytkownika sieci Web, należy wykonać następujące czynności.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do **resetowania > urządzenia konserwacji**.
2. Wybierz **pozycję Resetuj urządzenie**.

    ![Resetowanie urządzenia](media/data-box-edge-return-device/device-reset-1.png)

3. Po wyświetleniu monitu o potwierdzenie przejrzyj ostrzeżenie i wybierz **opcję Tak,** aby kontynuować.

    ![Potwierdź resetowanie](media/data-box-edge-return-device/device-reset-2.png)  

Resetowanie powoduje wymazanie danych z dysków z danymi urządzenia. W zależności od ilości danych na urządzeniu proces ten trwa około 30-40 minut.

Alternatywnie należy połączyć się z interfejsem programu `Reset-HcsAppliance` PowerShell urządzenia i użyć polecenia cmdlet, aby usunąć dane z dysków danych. Aby uzyskać więcej informacji, zobacz [Resetowanie urządzenia](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Jeśli wymieniasz lub uaktualniasz je do nowego urządzenia, zalecamy zresetowanie urządzenia dopiero po otrzymaniu nowego urządzenia.
> - Resetowanie urządzenia usuwa tylko wszystkie dane lokalne z urządzenia. Dane, które są w chmurze nie są usuwane i pobiera [opłaty](https://azure.microsoft.com/pricing/details/storage/). Te dane należy usunąć oddzielnie za pomocą narzędzia do zarządzania magazynem w chmurze, takiego jak [Eksplorator usługi Azure Storage.](https://azure.microsoft.com/features/storage-explorer/)

## <a name="open-a-support-ticket"></a>Otwieranie biletu pomocy technicznej

Aby rozpocząć proces zwrotu, należy wykonać następujące kroki.

1. Otwórz bilet pomocy technicznej z pomocą techniczną firmy Microsoft wskazując, że chcesz zwrócić urządzenie. Wybierz typ problemu jako **sprzęt krawędzi pola danych**.

    ![Otwieranie biletu pomocy technicznej](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Skontaktuje się z Tobą inżynier pomocy technicznej firmy Microsoft. Podaj szczegóły wysyłki.
3. Jeśli potrzebujesz pudełka zwrotnego, możesz go zamówić. Odpowiedz **Tak** na pytanie **Potrzebujesz pustego pola, aby zwrócić**.


## <a name="schedule-a-pickup"></a>Zaplanuj odbiór

1. Zamknij urządzenie. W lokalnym interfejsie użytkownika sieci Web przejdź do **ustawienia konserwacji > zasilania**.
2. Wybierz **pozycję Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak,** aby kontynuować. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasilaniem](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odłącz kable zasilające i wyjmij wszystkie kable sieciowe z urządzenia.
4. Przygotuj paczkę wysyłkową przy użyciu własnego pudełka lub pustego pudełka otrzymanego z platformy Azure. Umieść urządzenie i przewody zasilające, które zostały dostarczone z urządzeniem w pudełku.
5. Umieść etykietę wysyłkową otrzymaną z platformy Azure na opakowaniu.
6. Uzgodnij odbiór urządzenia z regionalną firmą kurierską. W przypadku zwrotu urządzenia w USA operatorem może być firma UPS lub FedEx. Aby zaplanować odbiór z ups:

    1. Zadzwoń do lokalnego UPS (numer bezpłatny dla poszczególnych krajów).
    2. W połączeniu podaj numer śledzenia przesyłki odwrotnej, jak pokazano na wydrukowanej etykiecie.
    3. Jeśli numer śledzenia nie jest podany, firma UPS będzie wymagać uiszczenia dodatkowej opłaty podczas odbioru.

    Zamiast planować odbiór, można również usunąć krawędź pola danych w najbliższej lokalizacji nadania.

## <a name="delete-the-resource"></a>Usuwanie zasobu

Po odebraniu urządzenia w centrum danych platformy Azure urządzenie jest sprawdzane pod kątem uszkodzeń lub jakichkolwiek oznak manipulacji.

- Jeśli urządzenie zostanie odebrane w stanie nienaruszonym i w dobrej kondycji, licznik rozliczeń zostanie zatrzymany dla tego zasobu. Pomoc techniczna firmy Microsoft skontaktuje się z Użytkownikiem w celu potwierdzenia, że urządzenie zostało zwrócone. Następnie można usunąć zasób skojarzony z urządzeniem w witrynie Azure portal.
- Jeśli urządzenie dotrze znacznie uszkodzone, mogą zostać nałożone grzywny. Szczegółowe informacje można znaleźć w [często zadawanych pytaniach dotyczących zgubienia lub uszkodzenia urządzenia](https://azure.microsoft.com/pricing/details/databox/edge/) oraz warunkach korzystania z [usługi.](https://www.microsoft.com/licensing/product-licensing/products)  


Urządzenie można usunąć w witrynie Azure portal:
-   Po złożeniu zamówienia i przed przygotowaniem urządzenia przez firmę Microsoft.
-   Po zwrócono urządzenie do firmy Microsoft, przekazuje inspekcję fizyczną w centrum danych platformy Azure i microsoft support wywołania, aby potwierdzić, że urządzenie zostało zwrócone.

Jeśli urządzenie aktywowano w innej subskrypcji lub lokalizacji, firma Microsoft przeniesie zamówienie do nowej subskrypcji lub lokalizacji w ciągu jednego dnia roboczego. Po przeniesieniu zamówienia można usunąć ten zasób.


Aby usunąć urządzenie i zasób w witrynie Azure Portal, należy wykonać następujące kroki.

1. W witrynie Azure portal przejdź do zasobu, a następnie do **przeglądu**. Na pasku poleceń wybierz pozycję **Usuń**.

    ![Wybierz usuń](media/data-box-edge-return-device/delete-resource-1.png)

2. W bloku **Usuń urządzenie** wpisz nazwę urządzenia, które chcesz usunąć, i wybierz pozycję **Usuń**.

    ![Potwierdzenie usunięcia](media/data-box-edge-return-device/delete-resource-2.png)

Zostaniesz powiadomiony po pomyślnym usunięciu urządzenia i skojarzonego zasobu.

## <a name="get-a-replacement-device"></a>Uzyskaj urządzenie zastępcze

Urządzenie zastępcze jest potrzebne, gdy istniejące urządzenie ma awarię sprzętu lub wymaga uaktualnienia. W przypadku wystąpienia problemów ze sprzętem urządzenia należy wykonać następujące czynności:

1. [Otwórz bilet pomocy technicznej dla problemu ze sprzętem](#open-a-support-ticket). Pomoc techniczna firmy Microsoft określi, że jednostka wymiany pola (FRU) nie jest dostępna dla tego wystąpienia lub urządzenie wymaga uaktualnienia sprzętu. W obu przypadkach pomoc techniczna zamówi urządzenie zastępcze.
2. [Utwórz nowy zasób](data-box-edge-deploy-prep.md#create-a-new-resource) dla urządzenia zastępczego. Upewnij się, że zaznaczysz pole wyboru względem **mam urządzenie Data Box Edge**. 
3. Po otrzymaniu urządzenia zastępczego [zainstaluj](data-box-edge-deploy-install.md) i [aktywuj](data-box-edge-deploy-connect-setup-activate.md) urządzenie zastępcze względem nowego zasobu.
4. Wykonaj wszystkie czynności, aby zwrócić oryginalne urządzenie:
    1. Otwórz inny bilet, aby zwrócić oryginalne urządzenie.
    2. [Wymazanie danych z urządzenia](#erase-data-from-the-device).
    3. [Zaplanuj odbiór](#schedule-a-pickup).
    5. [Usuń zasób](#delete-the-resource) skojarzony z zwróconym urządzeniem.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).
